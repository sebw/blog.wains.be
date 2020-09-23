---
date: 2020-09-23 
title: "Ansible uri module doesn't necessarily urlencode variables" 
---

For the longest time I have been using this bit of (kinda incorrect) Ansible code to log into IdM:

```
- name: Logging in to IPA and store session cookie                            
  uri:                                                                        
    url: "{{ ipa_url }}/session/login_password"                           
    method: POST                                                              
    force_basic_auth: yes                                                     
    headers:                                                                  
       Content-Type: "application/x-www-form-urlencoded"                      
    body: 'user={{ username }}&password={{ password }}'    
    status_code: 200                                                          
    validate_certs: false                                                     
  register: login  
```

It turns out, if the password contains a percent character `%` (such as `blah%blah`), uri will fail to authenticate.

You either need to:

- convert the password to its urlencoded form: `blah%25blah`. This would be confusing.
- urlencode the variable `{{ password | urlencode }}` as suggested [here](https://github.com/ansible/ansible/issues/63511)
- best option, use `body_format: form-urlencoded`:

```
- name: Logging in to IPA and store session cookie                            
  uri:                                                                        
    url: "{{ ipa_url }}/session/login_password"                           
    method: POST                                                              
    body_format: form-urlencoded                                              
    force_basic_auth: yes                                                     
    body:                                                                     
      user: "{{ username }}"                                            
      password: "{{ password }}"                                              
    status_code: 200                                                          
    validate_certs: false                                                     
  register: login
```

This would urlencode the body.

I have been using API calls rather than Ansible freeipa modules, because they were not maintained and contained bugs.

I will have to investigate Ansible collection for FreeIPA [https://github.com/freeipa/ansible-freeipa](https://github.com/freeipa/ansible-freeipa) to see if they are bug free.
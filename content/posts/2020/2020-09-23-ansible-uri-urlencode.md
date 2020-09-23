---
date: 2020-09-23 
title: "Ansible uri module doesn't necessarily urlencode variables" 
---

For the longest time I have been using this bit of (kinda incorrect) Ansible code to log into IdM:

```yaml
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

```yaml
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

There is also the [Freeipa collection for Ansible](https://github.com/freeipa/ansible-freeipa) but the collection contains pretty much the same [bugs](https://github.com/freeipa/ansible-freeipa/issues/160) that affect me particularly.

Plain API calls just work.

I should find time to contribute and help fixing some of those bugs...
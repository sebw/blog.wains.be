---
date: 2026-06-26
title: "Ansible Automation Platform and dynamic surveys"
---

As some of you may know, I work for Red Hat as an automation advocate in Belgium and Luxembourg.

My work is to assist customers leveraging automation to achieve their business objectives, and Ansible Automation Platform (AAP) can be instrumental in achieving those goals.

A request I hear from many customers is the desire for dynamic or programmable "surveys" inside Automation Controller (the REST API and Web UI part of AAP).

The request has actually [existed in the upstream AWX since 2017](https://github.com/ansible/awx/issues/20) and to this day hasn't been delivered. I ask customers to pitch in on the github issue and make a request for enhancement (RFE) to our Red Hat support.

It's interesting because alternatives like Rundeck has had the challenged solved for as long as I recall (at least 2010 when I started using it at my previous job) with the "Remote URL" job option.

## But what are dynamic or programmable surveys? 

Use case: you want to use AAP/AWX to deploy softwares.

The job template gives you two options/surveys: 

- a dropdown list of available softwares ready to deploy
- when software is selected, you get the versions available for that specific software

If things were dynamic, when starting a job template:

1. AAP would query an API (with Nexus, Artifactory, etc. behind the scene) and would create a dropdown list of softwares in almost real time (unless your API is down or slow)
2. AAP would query the API again with the software name as parameter, the Web UI field now shows the available versions for the selected software

Rundeck does exactly that and left the users figure out the logic of getting the values. Rundeck simply needs a properly formatted JSON output.

![](https://blog.wains.be/images/rundeck-option.png)

## Why is it so complicated to get this feature?

I'm not part of Red Hat software engineering but I believe we're looking a little too much into the Github request.

I hear argumentations how to make AAP/AWX talk to ANY API and return properly structured data something very challenging to implement.

But why should we go there in the first place? Rundeck approach is the most sensible tradeoff. You point Rundeck to a remote URL, you expect the URL to return a properly structured JSON. Basta.

If your API doesn't return a JSON in the structured way Rundeck needs, you need to manipulate the data in between. I've done exactly that with some PHP back in the days, facing Artifactory. It shouldn't be the job of Rundeck, AWX or AAP to perform data manipulation and parsing.

## "Almost dynamic" surveys

My offer and the whole point of this post is to give you some basic workaround for single survey job templates.

The survey can be programmed using AWX/AAP's API or using the Ansible Controller collection.

Here's an example:

```yaml
---
- name: Update Survey
  hosts: localhost
  gather_facts: false

  tasks:
  - name: Update survey
    ansible.controller.job_template:
      controller_host: "{{ controller_host }}"
      controller_username: "{{ controller_username }}"
      controller_password: "{{ controller_password }}"
      validate_certs: True
      name: "{{ target_job_template }}"
      survey_enabled: true
      survey_spec:
        name: Dynamic Environment Survey
        description: Generated from external API
        spec:
        - question_name: Environment
          question_description: Select deployment target
          variable: environment
          required: true
          type: multiplechoice
          choices: "{{ survey_choices }}"
          default: "{{ survey_choices[0] }}"
```

You can pass the job this payload:

```yaml
target_job_template: "Software deployment"
survey_choices:
  - artifact1
  - artifact2
  - artifact3
```

Run the job template and the survey for the "Environment" survey for the job "Software deployment" will be updated.

Now you can extend the playbook to call remote URL/APIs and construct the choices under `spec`.

There is a severe limitation to this, it is limited to a single survey.

If you want to pass the version in a second survey, there's no way to get this implemented in this almost dynamic way.

I've put the code in a Github [repo](https://github.com/sebw/AAP2-almost-dynamic-surveys/). Feel free to contribute and improve this quick and dirty playbook.

If you need dynamic surveys, let Red Hat (RFE through support) and the AWX project know (by adding a comment on the [issue](https://github.com/ansible/awx/issues/20))!

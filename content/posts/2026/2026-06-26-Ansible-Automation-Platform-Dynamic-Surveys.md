---
date: 2026-06-26
title: "Ansible Automation Platform and dynamic surveys"
---

As some of you may know, I work for Red Hat as an automation advocate.

My work is to assist customers leveraging automation to achieve their business objectives, and Ansible Automation Platform (AAP) can be instrumental in achieving those goals.

A request I hear from many customers is the desire for dynamic or programmable "surveys" inside Automation Controller (the REST API and Web UI part of AAP).

The request has actually [existed in the upstream AWX since 2017](https://github.com/ansible/awx/issues/20) and to this day hasn't been delivered. I ask customers to pitch in on the github issue and make a request for enhancement (RFE) to our Red Hat support.

It's interesting because alternatives like Rundeck has had the challenged solved for as long as I recall (at last 2010 when I started using it at my previous job) with the Remote URL job option.

## But what are dynamic or programmable surveys? 

Use case: you want to use AAP/AWX to deploy softwares.

The job template gives you two options/surveys: 

- a dropdown list of available software ready to deploy
- the version of the software you want to deploy

If things were dynamic, when starting a job template:

1. AAP would query the API of the software repository (Nexus, Artifactory, etc.) and would create a dropdown list of softwares in almost real time (unless your API is down or slow)
2. AAP would query the API again with the software name as parameter, the Web UI field now shows the available versions for the selected software

Rundeck does exactly that and left the users figure out the logic of getting the values. Rundeck simply needs a properly formatted JSON output.

![](https://blog.wains.be/images/rundeck-option.png)

## Why is it so complicated to get this feature?

I'm not part of Red Hat software engineering but I believe we're looking a little too much into the Github request.

I hear argumentations how to make AAP/AWX talk to ANY API and return properly structured values something very challenging to implement.

But why should we go there in the first place? Rundeck approach is the most sensible tradeoff. Point Rundeck to a remote URL, expect URL to return a properly structured JSON. Basta.

If you have APIs that do not talk JSON the way Rundeck wants, you need to develop something in between. It shouldn't be the job of Rundeck or AWX or AAP to manipulate the data.

## "Almost dynamic" surveys

My offer and the whole point of this post is to give you some basic workaround for single survey job templates.

The survey can be programmed using AWX/AAP's API or using the Ansible Controller collection.

Here's an example:

```
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

You can pass the job a JSON payload:

```
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

I've put the code in a Github [repo](https://github.com/sebw/AAP2-almost-dynamic-surveys/)

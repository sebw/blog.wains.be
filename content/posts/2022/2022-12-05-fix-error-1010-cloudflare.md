---
date: 2022-12-05
title: "Fix error 1010 when using Cloudflare and Python"
---

I recently moved from running a VPS in some cloud to running a VM on my home server and exposing it behind Cloudflare tunnels.

Everything was great until I decided to host an instance of Ansible Automation Platform (AAP).

I could reach the UI and API (using Insomnia). Somehow when trying to use the Automation Controller Ansible Collection to manage my AAP, I was getting an interesting error "Failed to get token: HTTP Error 403: Forbidden" with "response: error code: 1010".

I've managed AAP with the collection for about a year in many different setups and it never failed.

It turns out, Cloudflare does "browser integrity checks" and it blocks user agents such as "python-urllib".

A quick search on the error code and we see many projects (Gitlab, others) affected and the common denominator is Cloudflare.

## Solution

Go to your [Cloudflare dashboard](https://dash.cloudflare.com)

Choose your domain.

Security > Settings.

Disable "Browser integrity check"
---
date: 2020-02-17
title: "Mkdocs publishing workflow"
---

I write articles on my personal computer.

Mkdocs supports Markdown, which has some great advantages:

- articles are stored as plain text on disk
- the content is indexed making it easy to find content with Alfred/Spotlight/grep
- you can grep, sed, awk the hell out of your articles and bring corrections very quickly
- can (and should!) be stored in a Git repository

This is my current publication workflow:

- I edit articles locally with [Visual Studio Code] (yes, it is an open source Microsoft product, and it is very good)
- I commit changes to my [GitHub] 
- With an Alfred workflow, I automatically connect to my VPS and do:
    - a "git pull" to retrieve updates
    - rebuild the doc (which is served as static files by the HTTP server)
---
date: 2007-11-12
title: "Gmail IMAP under Thunderbird - problems with PDF files ?"
---







categories:
- Misc


My wife had some problems today sending her resume in PDF to some companies.
She used the web interface of Gmail.
Some people reported they were unable to open the file.

We've made some tests. She tried to send an email with the PDF attachment to these recipients  (carbon copy, not separate emails) :
- HER Gmail address configured in Thunderbird using IMAP
- MY Gmail address using the web interface
- MY work address configured in Thunderbird using IMAP (courier-imap server)

I was able to open the file on my home and work addresses while she was not able to open the file in Thunderbird.

I fetched the corrupted attachment on her side and compared both versions :

-rw-r--r-- 1 sw sw  138052 2007-11-12 19:55 cv_ok.pdf
-rwxr--r-- 1 sw sw  136198 2007-11-12 19:56 cv_not_ok.pdf

cv_ok.pdf is the file I received..
cv_not_ok.pdf is the file she received..

Apparently the file gets corrupted at the Gmail IMAP level.
Indeed, if she tries to open the PDF using the web interface it works fine.

Now, we still need to figure out why some people were not able to open the file. I'm sure they don't use Gmail IMAP.

She has been sending that copy of her resume for months and she never had any issue until today.

I found this link, which confirms there are issues with PDF in Gmail : 
[http://groups.google.com/group/Gmail-POP-and-Forwarding/browse_thread/thread/6d831984edf6a1ac/a62973111852bc82?lnk=raot
](http://groups.google.com/group/Gmail-POP-and-Forwarding/browse_thread/thread/6d831984edf6a1ac/a62973111852bc82?lnk=raot)

Any idea is welcomed !

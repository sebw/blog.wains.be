---
date: 2007-08-18
title: "ViewCVS ImportError- No module named svn (Debian)"
---







categories:
- Debian/Ubuntu
- Linux
- Versioning


Even if properly configured for using Subversion repositories, you'd get this error messages from ViewCVS :


    
    <code>An Exception Has Occurred
    Python Traceback
    
    Traceback (most recent call last):
      File "/var/lib/python-support/python2.4/viewcvs.py", line 3235, in main
        request.run_viewcvs()
      File "/var/lib/python-support/python2.4/viewcvs.py", line 268, in run_viewcvs
        import vclib.svn
      File "/var/lib/python-support/python2.4/vclib/svn/__init__.py", line 27, in ?
        from svn import fs, repos, core, delta
    ImportError: No module named svn</code>



**The reason of the error is a package missing : python-subversion**

The same issue is reported for Ubuntu : [https://bugs.launchpad.net/ubuntu/+source/viewcvs/+bug/74005](https://bugs.launchpad.net/ubuntu/+source/viewcvs/+bug/74005)



---
date: 2011-02-16
title: "Red Hat Cluster - VMware ESX fencing"
---







categories:
- High-Availability
- Red Hat/CentOS


Tested on Red Hat Enterprise Linux 5.6 64 bits and VMware ESX 3.5
Edit November 2011 : Tested on RHEL6.1 and VMware ESX 4.1

If you set up a cluster, in case of failure, you'll probably want the surviving host to be able to "fence" or "[stonith](http://en.wikipedia.org/wiki/STONITH)" the faulty node.

Red Hat Cluster provides a collection of scripts for that purpose (for APC, ILO, DRAC, etc. and VMware).


The vmware script doesn't work out of the box :


    
    <code># fence_vmware -a "esx.intranet.example.org" -l "fence_vmware_account" -p "xxx" -n 'node01'
    fence_vmware_helper returned Please install VI Perl API package to use this tool!
    Perl error: Can't locate VMware/VIRuntime.pm in @INC (@INC contains: /usr/lib64/perl5/site_perl/5.8.8/x86_64-linux-thread-multi /usr/lib/perl5/site_perl/5.8.8 /usr/lib/perl5/site_perl /usr/lib64/perl5/vendor_perl/5.8.8/x86_64-linux-thread-multi /usr/lib/perl5/vendor_perl/5.8.8 /usr/lib/perl5/vendor_perl /usr/lib64/perl5/5.8.8/x86_64-linux-thread-multi /usr/lib/perl5/5.8.8 .) at (eval 1) line 1.
    BEGIN failed--compilation aborted at (eval 1) line 1.
    
    Please use '-h' for usage</code>





Go to [http://www.vmware.com/support/developer/viperltoolkit/](http://www.vmware.com/support/developer/viperltoolkit/) (you'll need to register)

Grab either one of those : 

ESX 3.5

VMware-VIPerl-1.6.0-104313.i386.tar.gz
VMware-VIPerl-1.6.0-104313.x86_64.tar.gz

ESX 4.1

VMware-vSphere-Perl-SDK-4.1.0-*.i386.tar.gz
VMware-vSphere-Perl-SDK-4.1.0-*.x86_64.tar.gz

You'll need to install some stuff on your system :

**RHEL5**


    
    <code># yum install openssl-devel
    
    Dependencies Resolved
    
    ========================================================================================================================================================================
     Package                                   Arch                         Version                                 Repository                                         Size
    ========================================================================================================================================================================
    Installing:
     openssl-devel                             i386                         0.9.8e-12.el5_5.7                       rhel-5Server-x86_64-updates                       1.9 M
     openssl-devel                             x86_64                       0.9.8e-12.el5_5.7                       rhel-5Server-x86_64-updates                       1.9 M
    Installing for dependencies:
     e2fsprogs-devel                           x86_64                       1.39-23.el5_5.1                         rhel-5Server-x86_64-updates                       633 k
     keyutils-libs-devel                       x86_64                       1.2-1.el5                               rhel-5Server-x86_64-updates                        27 k
     krb5-devel                                x86_64                       1.6.1-55.el5                            rhel-5Server-x86_64-updates                       1.9 M
     libselinux-devel                          x86_64                       1.33.4-5.7.el5                          rhel-5Server-x86_64-updates                       149 k
     libsepol-devel                            x86_64                       1.15.2-3.el5                            rhel-5Server-x86_64-updates                       192 k
     zlib-devel                                x86_64                       1.2.3-3                                 rhel-5Server-x86_64-updates                       102 k
    
    Transaction Summary
    ========================================================================================================================================================================
    Install       8 Package(s)
    Upgrade       0 Package(s)
    
    Total download size: 6.7 M
    Is this ok [y/N]: </code>



**RHEL6**


    
    <code># yum install openssl-devel perl-Compress-Raw-Zlib perl-Compress-Zlib
    Setting up Install Process
    Resolving Dependencies
    --> Running transaction check
    ---> Package openssl-devel.x86_64 0:1.0.0-10.el6_1.5 will be installed
    --> Processing Dependency: pkgconfig for package: openssl-devel-1.0.0-10.el6_1.5.x86_64
    --> Processing Dependency: zlib-devel for package: openssl-devel-1.0.0-10.el6_1.5.x86_64
    --> Processing Dependency: krb5-devel for package: openssl-devel-1.0.0-10.el6_1.5.x86_64
    --> Processing Dependency: /usr/bin/pkg-config for package: openssl-devel-1.0.0-10.el6_1.5.x86_64
    ---> Package perl-Compress-Raw-Zlib.x86_64 0:2.023-119.el6_1.1 will be installed
    ---> Package perl-Compress-Zlib.x86_64 0:2.020-119.el6_1.1 will be installed
    --> Processing Dependency: perl(IO::Uncompress::Gunzip) >= 2.020 for package: perl-Compress-Zlib-2.020-119.el6_1.1.x86_64
    --> Processing Dependency: perl(IO::Compress::Gzip) >= 2.020 for package: perl-Compress-Zlib-2.020-119.el6_1.1.x86_64
    --> Processing Dependency: perl(IO::Compress::Gzip::Constants) >= 2.020 for package: perl-Compress-Zlib-2.020-119.el6_1.1.x86_64
    --> Processing Dependency: perl(IO::Compress::Base::Common) >= 2.020 for package: perl-Compress-Zlib-2.020-119.el6_1.1.x86_64
    --> Running transaction check
    ---> Package krb5-devel.x86_64 0:1.9-9.el6_1.2 will be installed
    --> Processing Dependency: libselinux-devel for package: krb5-devel-1.9-9.el6_1.2.x86_64
    --> Processing Dependency: libcom_err-devel for package: krb5-devel-1.9-9.el6_1.2.x86_64
    --> Processing Dependency: keyutils-libs-devel for package: krb5-devel-1.9-9.el6_1.2.x86_64
    ---> Package perl-IO-Compress-Base.x86_64 0:2.020-119.el6_1.1 will be installed
    ---> Package perl-IO-Compress-Zlib.x86_64 0:2.020-119.el6_1.1 will be installed
    ---> Package pkgconfig.x86_64 1:0.23-9.1.el6 will be installed
    ---> Package zlib-devel.x86_64 0:1.2.3-25.el6 will be installed
    --> Running transaction check
    ---> Package keyutils-libs-devel.x86_64 0:1.4-1.el6 will be installed
    ---> Package libcom_err-devel.x86_64 0:1.41.12-7.el6 will be installed
    ---> Package libselinux-devel.x86_64 0:2.0.94-5.el6 will be installed
    --> Processing Dependency: libsepol-devel >= 2.0.32-1 for package: libselinux-devel-2.0.94-5.el6.x86_64
    --> Processing Dependency: pkgconfig(libsepol) for package: libselinux-devel-2.0.94-5.el6.x86_64
    --> Running transaction check
    ---> Package libsepol-devel.x86_64 0:2.0.41-3.el6 will be installed
    --> Finished Dependency Resolution
    
    Dependencies Resolved
    
    =============================================================================================================================================
     Package                              Arch                 Version                           Repository                                 Size
    =============================================================================================================================================
    Installing:
     openssl-devel                        x86_64               1.0.0-10.el6_1.5                  rhel-6Server-x86_64-updates               1.1 M
     perl-Compress-Raw-Zlib               x86_64               2.023-119.el6_1.1                 rhel-6Server-x86_64-updates                67 k
     perl-Compress-Zlib                   x86_64               2.020-119.el6_1.1                 rhel-6Server-x86_64-updates                43 k
    Installing for dependencies:
     keyutils-libs-devel                  x86_64               1.4-1.el6                         rhel-6Server-x86_64-updates                28 k
     krb5-devel                           x86_64               1.9-9.el6_1.2                     rhel-6Server-x86_64-updates               1.2 M
     libcom_err-devel                     x86_64               1.41.12-7.el6                     rhel-6Server-x86_64-updates                30 k
     libselinux-devel                     x86_64               2.0.94-5.el6                      rhel-6Server-x86_64-updates               135 k
     libsepol-devel                       x86_64               2.0.41-3.el6                      rhel-6Server-x86_64-updates                64 k
     perl-IO-Compress-Base                x86_64               2.020-119.el6_1.1                 rhel-6Server-x86_64-updates                66 k
     perl-IO-Compress-Zlib                x86_64               2.020-119.el6_1.1                 rhel-6Server-x86_64-updates               133 k
     pkgconfig                            x86_64               1:0.23-9.1.el6                    rhel-6Server-x86_64-updates                70 k
     zlib-devel                           x86_64               1.2.3-25.el6                      rhel-6Server-x86_64-updates                43 k
    
    Transaction Summary
    =============================================================================================================================================
    Install      12 Package(s)
    
    Total download size: 3.0 M
    Installed size: 6.4 M
    Is this ok [y/N]: </code>




Unzip VMware-*.tar.gz and run :
`./vmware-install.pl`


Accept the terms. (yes, this is needed).


Then you should get to this :

`The installation of VMware VIPerl Toolkit 1.6.0 build-104313 for Linux 
completed successfully. You can decide to remove this software from your system
at any time by invoking the following command: 
"/usr/bin/vmware-uninstall-viperl.pl".`


Make sure you add "virtual machine administrator" permissions to "vmware_fence_account", for the VM's it needs to stonith.

From now on, you should be able to stonith VM's.

Here's a working RHCS config (/etc/cluster/cluster.conf) :

[sourcecode language="xml"]
<?xml version="1.0"?>
<cluster alias="ServiceClusterTEST" config_version="4" name="ServiceTEST">
<totem token="45000"/>
<fence_daemon post_fail_delay="0" post_join_delay="3"/> <fence_daemon clean_start="1"/>
<clusternodes>
<clusternode name="node01.intranet.example.org" nodeid="1" votes="1">
<fence>
<method name="1">
<device name="node01"/>
</method>
</fence>
</clusternode>
<clusternode name="node02.intranet.example.org" nodeid="2" votes="1">
<fence>
<method name="1">
<device name="node02"/>
</method>
</fence>
</clusternode>
</clusternodes>
<cman expected_votes="1" two_node="1"/>
<fencedevices>
<fencedevice agent="fence_vmware" ipaddr="esx.intranet.example.org" login="vmware_fence_account" passwd="password" name="node01" port="node01"/>
<fencedevice agent="fence_vmware" ipaddr="esx.intranet.example.org" login="vmware_fence_account" passwd="password" name="node02" port="node02"/>
</fencedevices>
<rm>
<resources>
[whatever resources you have]
</resources>
<service name="Service" autostart="1">
[whatever services the cluster is in charge of]
</service>
</rm>
</cluster>
[/sourcecode]



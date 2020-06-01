---
date: 2019-12-05
title: "Freeipa Authentication Failure in AD Trust setup"
---

**Problem:**

Authentication fails for AD users on RHEL system in an Freeipa/AD trusted environment.

The following errors can be found in the logs:

- `Cannot find KDC for realm "EXAMPLE.COM"` in `/var/log/sssd/krb5_child.log`
- `Backend is marked offline, retry later!` in `/var/log/sssd/sssd_$domain.log`
- `pam_unix(sssd:auth): authentication failure` in `/var/log/secure`

**Assumptions and context:**

- AD domain: example.org
- Freeipa domain: linux.example.org
- AD User: test
- userPrincipalName (UPN) for "test": firstname.lastname@example.com <--- UPN domain example.com is different than example.org!!!
- Connection to RHEL client with: ssh test@example.org@server1.linux.example.org
- IDM clients (in linux.example.org) can't resolve the example.com UPN domain

In the context above, IDM clients will try to use the example.com (derived from the UPN) realm to authenticate into Linux machines.

The RHEL client will use example.com as realm and try to discover KDC for example.com through DNS requests, which will return "no such name" (can be seen in a tcpdump trace).

**Solution:**

The solution is to "trick" Freeipa into using the domain passed at the SSH command line by the user (example.org derived from user fqdn test@example.org).

On all Freeipa servers, in `/etc/sssd/sssd.conf`, add in the relevant domain section for the trust:

```bash
[domain/linux.example.org/example.org]
subdomain_inherit = ldap_user_principal  # <--- this option
ldap_user_principal = nosuchattr    # <--- this option
```

Clear the cache on IDM servers and IDM clients with:

`systemctl stop sssd && rm -fr /var/lib/sssd/db/* && systemctl start sssd`

Restart ipa:

`systemctl restart ipa`

Attempt a connection with:

`ssh test@example.org@server1.linux.example.org`

**Explanation:**

The UPN domain is used first, but in this context the domain cannot be resolved. We have to force IDM clients to use the domain passed at the CLI and ignore the UPN.
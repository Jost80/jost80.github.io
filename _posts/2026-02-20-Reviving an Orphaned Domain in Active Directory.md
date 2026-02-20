---
title: Reviving an Orphaned Domain in Active Directory
date: 2026-02-20 14:21:00 +/-TTTT
categories: [Windows]
tags: [AD]     # TAG names should always be lowercase
---

We recently encountered a scenario where the forest root domain had become orphaned.
Trust was working but replication of forest wide data was not.

The issue occurred in a lab-environment were a failed DC upgrade a few months back had taken place and for some reason it was not noticed then.

Microsoft have documentation to resolve an orphaned child domain here [https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/orphaned-child-domain-isnt-replicated](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/orphaned-child-domain-isnt-replicated) but this did not work at first for us (but we will get back to it).
After troubleshooting together with copilot and testing numerous suggestions using ntdsutil, adsiedit and more that just made the error worse we reverted back to previous snapshots and with some luck found that the Microsoft documentation could be somewhat reversed. 
Microsoft assumed that a child domain was orphaned but for us it was the root. All child domains was replicating between them correctly.
Final solution for us became:

On root DC:

Adding “Replicator Allow SPN Fallback” DWORD under HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters  and give it the value 1.
Reboot the DC

On root DC:
```bash
repadmin /options fully_qualified_domain_name_(FQDN)_of_the_root_domain_controller +DISABLE_NTDSCONN_XLATE
```


On Child DC but running as enterprise admin (worked since trust was ok). In Microsofts documentation this should also be run on the root DC.
```bash
repadmin /add CN=Configuration,DC=Domain_Name,DC=Domain_Name FQDN_of_the_root_domain_controller FQDN_of_the_child_domain_controller  
```

dcdiag showed some DNS related errors after this but with a reboot of DC’s the replication now checks out ok.

Don't forget to remove "Replicator Allow SPN Fallback" when you are done.

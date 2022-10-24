---
title: ApplicationAccessPolicy
date: 2022-10-24 21:13:00 +/-TTTT
categories: [Cloud]
tags: [azure]     # TAG names should always be lowercase
---

When finally removing the last exceptions from a Conditional Access policy blocking Basic Authentication I came upon an application (external vendor) that previously used IMAP with basic authentication and now needed migrating to Oauth. 

The vendor provided easy instructions but listed horrible permissions. A big no from me was The Exchange API permission "full_access_as_app". That would give the vendor access to read all mailboxes in our organization. A [thread on reddit](https://www.reddit.com/r/AZURE/comments/vp0l3h/vendor_wants_full_access_as_app_to_our_exchange/) pointed me towards [this documentation](https://learn.microsoft.com/en-us/graph/auth-limit-mailbox-access) on  Application Access Policy's.

The solution is to add the mailbox the app should be able to access to a distribution list and create a policy to limit the applications permissions to members of that distribution list. 

```powershell
New-ApplicationAccessPolicy -AppId 17ff220f-79db-4c7f-a9a5-fef452795ace -PolicyScopeGroupId RestrictedGroup@ourtenant.onmicrosoft.com -AccessRight RestrictAccess -Description "Restrict access to members of RestrictedGroup" 
```
To test the access we can use Test-ApplicationAccessPolicy 

```powershell
Test-ApplicationAccessPolicy -Identity testuser1@ourtenant.onmicrosoft.com -AppId 17ff220f-79db-4c7f-a9a5-fef452795ace 
```
The output should list AccessCheckResult and give you Denied or Granted. 

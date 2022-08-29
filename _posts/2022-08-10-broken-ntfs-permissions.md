---
title: Broken NTFS permissions after migration
date: 2022-08-10 18:20:00 +/-TTTT
categories: [Windows]
tags: [ntfs]     # TAG names should always be lowercase
---

After migrating a fileshare using DFS replication I noticed that permissions were acting strange. 
When creating folders at the new location they did not inherit permissions from the root folder if created using the smb share. 
When folders were created locally they worked just fine.

Solution was to add a random permission to the root folder (used my admin account in this case). 
When the added permission had propagated down to all subfolder and files it was removed again. 

Remember to not check "Replace all child object permission entries with inheritable permission entries from this object" 
when doing this since that can wipe out any explicit permissions set on subfolder and files.


## References:

* [https://www.sys-manage.com/Blog/how-share-ntfs-permissions-and-inheritance-actually-work#corrupted-ntfs-permission-inheritance](https://www.sys-manage.com/Blog/how-share-ntfs-permissions-and-inheritance-actually-work#corrupted-ntfs-permission-inheritance)

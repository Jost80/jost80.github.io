---
title: SCCM Collection for WSL installations
date: 2024-05-22 19:55:00 +/-TTTT
categories: [Windows]
tags: [SCCM, WSL]     # TAG names should always be lowercase
---

SCCM isn´t really my thing but a use case came up were I needed to deploy the Defender WSL plugin to machines running WSL and after some googling I came up with this query.

```code
select SMS_R_System.NetbiosName, SMS_G_System_OPTIONAL_FEATURE.Caption, SMS_G_System_OPTIONAL_FEATURE.InstallState, SMS_G_System_OPTIONAL_FEATURE.Status from SMS_R_System inner join SMS_G_System_OPTIONAL_FEATURE on SMS_G_System_OPTIONAL_FEATURE.ResourceID = SMS_R_System.ResourceId where SMS_G_System_OPTIONAL_FEATURE.Name = "Microsoft-Windows-Subsystem-Linux" and SMS_G_System_OPTIONAL_FEATURE.InstallState = "1"
```
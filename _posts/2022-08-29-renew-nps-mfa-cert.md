---
title: Renew certificate for NPS Azure MFA extension
date: 2022-08-29 09:19:00 +/-TTTT
categories: [Windows]
tags: [azure, mfa]     # TAG names should always be lowercase
---

Got a report this morning that MFA using Azure MFA extension in NPS did not work and I found a lot of Event ID 3 in the AuthZAdminCh channel. The interesting part of the error was 

>*The certificate with identifier used to sign the client assertion is expired on application. [Reason - The key used is expired*.

I blog post on [starwind.com](https://www.starwindsoftware.com/blog/transition-a-highly-available-rd-gateway-to-use-the-nps-extension-for-azure-mfa-phase-2) sent me in the right direction. Turned out to be a certificate generated during setup with a 2 year validity that had expired. 

The certificate is located in *[Certificates - Local Computer\Personal\Certificates]* and CN equals the tenant ID.

To generate a new certificate the script AzureMfaNpsExtnConfigSetup.ps1 included in the MFA extension installation can be used. Sign in as tenant admin when prompted and press enter to keep the current tenant ID. Enter Y if you get prompted to allow NuGet.

```powershell
PS C:\Program Files\Microsoft\AzureMfa\Config> .\AzureMfaNpsExtnConfigSetup.ps1
VERBOSE: Using the provider 'PowerShellGet' for searching packages.
VERBOSE: The -Repository parameter was not specified.  PowerShellGet will use all of the registered repositories.
VERBOSE: Getting the provider object for the PackageManagement Provider 'NuGet'.
VERBOSE: The specified Location is 'https://www.powershellgallery.com/api/v2' and PackageManagementProvider is 'NuGet'.
VERBOSE: Searching repository 'https://www.powershellgallery.com/api/v2/FindPackagesById()?id='MSOnline'' for ''.
VERBOSE: Total package yield:'1' for the specified package 'MSOnline'.
VERBOSE: Performing the operation "Install-Module" on target "Version '1.1.183.66' of module 'MSOnline'".

Untrusted repository
You are installing the modules from an untrusted repository. If you trust this repository, change its
InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
'PSGallery'?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): y
VERBOSE: The installation scope is specified to be 'AllUsers'.
VERBOSE: The specified module will be installed in 'C:\Program Files\WindowsPowerShell\Modules'.

Connecting to Microsoft Azure.  Please sign on as a tenant administrator.
Starting Azure MFA NPS Extension Configuration Script
 Tenant ID currently registered with Azure MFA NPS Extension is: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Enter new Tenant ID to change or press Enter to keep the current value:
Generating client certificate

Thumbprint                                Subject
----------                                -------
AABBCCDD1122334455AABBCCDD1122334455AABB  CN=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, OU=Microsoft NPS Extension
Client Certificate successfully generated
Client Certificate associated with Service Principal: yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy
Starting registry updates
Completed registry updates
Client certificate : CN=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, OU=Microsoft NPS Extension successfully associated with Azure MFA NPS Extension for Tenant ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Granting certificate private key access to NETWORK SERVICE
Successfully granted to NETWORK SERVICE
Restarting Network Policy Server (ias) service
WARNING: Waiting for service 'Network Policy Server (ias)' to stop...
WARNING: Waiting for service 'Network Policy Server (ias)' to stop...
Configuration complete.  Press Enter to continue...:
```


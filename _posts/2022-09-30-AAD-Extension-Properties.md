---
title: Extension Attributes in Azure AD
date: 2022-09-30 15:50:00 +/-TTTT
categories: [Cloud]
tags: [azure]     # TAG names should always be lowercase
---
I needed to create a dynamic group in AzureAD that contained only users created in the last X number of days and I did a test with extension attributes.

First an application needed to be created in Azure AD under app registrations. For some reason the example codes I found to do this in powershell did not work but it was easy enough in the portal.

After the application is created we can add a new property.
```powershell
# ObjectID is the Application (client) ID of the application we created.
New-AzureADApplicationExtensionProperty -ObjectId 6421aee036014ad0937ee5ac271eb831 -Name "NewEmployee" -DataType boolean -TargetObjects "User"
```
Then we can set a value as a test.
```powershell
$UserId = "my.testuser@ourtenant.onmicrosoft.com"
Set-AzureADUserExtension -ObjectId $UserId -ExtensionName "extension_6421aee036014ad0937ee5ac271eb831_NewEmployee" -ExtensionValue $false
```


Then to set the value on all users matching a specific SMTP domain.

```powershell
$allusers = Get-AzureADUser -All $true | Where-Object {$_.UserPrincipalName -like "*@mydomain.org"} | Where-Object {$_.ExtensionProperty.extension_6421aee036014ad0937ee5ac271eb831_NewEmployee -ne $false}

$cutoverdate = (get-date).AddDays(-14)

ForEach($user in $allusers) {
	$creationDate = (Get-AzureADUserExtension -ObjectId $user.UserPrincipalName).Get_Item("createdDateTime")
	if([DateTime]$creationDate -gt $cutoverdate) {
            Set-AzureADUserExtension -ObjectId $user.UserPrincipalName -ExtensionName "extension_6421aee036014ad0937ee5ac271eb831_NewEmployee" -ExtensionValue $true
		} else {
            Set-AzureADUserExtension -ObjectId $user.UserPrincipalName -ExtensionName "extension_6421aee036014ad0937ee5ac271eb831_NewEmployee" -ExtensionValue $false
        }
}
```

In the dynamic rule builder in Azure AD we can then use the following to get all users were our attribute is true.

```code
user.extension_19de210501aa496ebbef77282aec382c_NewEmployee -eq true
```

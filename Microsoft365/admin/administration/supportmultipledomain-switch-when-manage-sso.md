---
title: SupportMultipleDomain switch when managing single sign-on (SSO) to Microsoft 365
description: SupportMultipleDomain switch when managing SSO to Microsoft 365.
author: helenclu
ms.author: luche
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
localization_priority: Normal
ms.custom: 
  - CSSTroubleshoot
  - has-azure-ad-ps-ref
ms.reviewer: 
appliesto: 
  - Microsoft 365
search.appverid: 
  - MET150
ms.date: 03/31/2022
---

# SupportMultipleDomain switch when managing SSO to Microsoft 365

## Summary

### Use of SupportMultipleDomain switch, when managing SSO to Microsoft 365 using AD FS

When an SSO is enabled for Microsoft 365 via AD FS, you should see the Relying Party (RP) trust created for Microsoft 365.

:::image type="content" source="media/supportmultipledomain-switch-when-manage-sso/relying-party-trust-created-for-o365.png" alt-text="Screenshot shows the Relying Party trust created for Microsoft 365.":::

### Commands that would create the RP trust for Microsoft 365

```PowerShell
New-MsolFederatedDomain -DomainName<domain>
Update-MSOLFederatedDomain -DomainName <domain>
Convert-MsolDomainToFederated -DomainName <domain>
```

[!INCLUDE [Azure AD PowerShell deprecation note](../../../includes/aad-powershell-deprecation-note.md)]

The RP trust created came with two claims rules

`Get-MsolFederationProperty -DomainName <domain>` on the federated domains shows that the "FederationServiceIdentifier" was the same for source AD FS and Microsoft 365,  which is `http://stsname/adfs/Services/trust`.

Earlier before the AD FS [Rollup 1](https://support.microsoft.com/kb/2607496) and [Rollup 2](https://support.microsoft.com/kb/2681584) updates, Microsoft 365 customers who utilize SSO through AD FS 2.0 and have multiple top-level domains for users' UPN suffixes within their organization (for example, @contoso.com or @fabrikam.com) are required to deploy a separate instance of AD FS 2.0 Federation Service for each suffix.  There's now a rollup for AD FS 2.0 ([https://support.microsoft.com/kb/2607496](https://support.microsoft.com/kb/2607496)) that works with the "SupportMultipleDomain" switch to enable the AD FS server to support this scenario without requiring more AD FS 2.0 servers.

### With the AD FS rollup 1 update, we added the following functionality

#### Multiple Issuers Supports

Previously, Microsoft 365 customers who require SSO by using AD FS 2.0 and use multiple top-level domains for users' UPN suffixes within their organization (for example, @contoso.us or @contoso.de) are required to deploy a separate instance of AD FS 2.0 Federation Service for each suffix. After you install this Update Rollup on all the AD FS 2.0 federation servers in the farm and follow the instructions for using this feature with Microsoft 365, new claim rules will be set to dynamically generate token issuer IDs based on the UPN suffixes of the Microsoft 365 users. As a result, you don't have to set up multiple instances of AD FS 2.0 federation server to support SSO for multiple top-level domains in Microsoft 365.

#### Support for Multiple Top-Level Domains

"Currently, Microsoft 365 customers who use single sign-on (SSO) through AD FS 2.0 and have multiple top-level domains for users' user principal name (UPN) suffixes within their organization (for example, @contoso.com or @fabrikam.com) are required to deploy a separate instance of AD FS 2.0 Federation Service for each suffix.  There's now a rollup for AD FS 2.0 ([https://support.microsoft.com/kb/2607496](https://support.microsoft.com/kb/2607496)) that works with the "SupportMultipleDomain" switch to enable the AD FS server to support this scenario without requiring more AD FS 2.0 servers."

### Commands that would create the RP trust for Microsoft 365

```powershell
New-MsolFederatedDomain -DomainName<domain>-SupportMultiDomain

Update-MSOLFederatedDomain -DomainName <domain>-SupportMultipleDomain

Convert-MsolDomainToFederated -DomainName <domain>-supportMultipleDomain
```

`Get-MsolFederationProperty -DomainName <domain>` on the federated domains now shows that the "FederationServiceIdentifier" is different for AD FS and Microsoft 365. Every federated domain has the "FederationServiceIdentifier" as
`http://<domainname>/adfs/services/trust/`, whereas the AD FS configuration still has `http://STSname/adfs/Services/trust`.

Due to this mismatch in configuration, we need to ensure that when a token is sent to Microsoft 365 the issuer mentioned in it, is the same as one configured for the Domain in Microsoft 365. Otherwise, you get the error.

So when you add or update RP trust with SupportMultipleDomain switch, a third claim rule is automatically added to the RP trust for Microsoft 365.

Default third rule:

c:[Type == "`http://schemas.xmlsoap.org/claims/UPN](http://schemas.xmlsoap.org/claims/UPN`"]
=> issue(Type = "`https://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`", Value = regexreplace(c.Value, .`+@(?<domain>.+`), `http://${domain}/adfs/services/trust/`));

This rule uses the suffix value of the user's UPN and uses it to generate a new claim called "Issuerid". For example, `http://contoso.com/adfs/services/trust/`.

Using [fiddler](https://www.fiddler2.com/fiddler2/), we can trace the token being passed to login.microsoftonline.com/login.srf. After copying the token passed in `wresult`, paste the content in notepad and save that file as .xml.
Later you can open the token saved as .xml file using IE and see its content.

:::image type="content" source="media/supportmultipledomain-switch-when-manage-sso/fidder-wresult.png" alt-text="Screenshot to copy the security token passed in wresult.":::

It's interesting to note that the rule issues "Issuerid" claim, we don't see this claim in the response token, in fact we see the "Issuer" attribute modified to the newly composed value.

```adoc
<saml:Assertion MajorVersion="1" MinorVersion="1" AssertionID="_2546eb2e-a3a6-4cf3-9006-c9f20560097f"Issuer="[http://contoso.com/adfs/services/trust/](http://contoso.com/adfs/services/trust/)" IssueInstant="2012-12-23T04:07:30.874Z" xmlns:saml="urn:oasis:names:tc:SAML:1.0:assertion">
```

**NOTE**

- SupportMultipleDomain is used without the AD FS rollup 1 or 2 installed. You'll see that the response token generated by AD FS has BOTH the Issuer="`http://STSname/adfs/Services/trust`" and the claim "Issuerid" with the composed value as per the third claim rule.

    ```adoc
    <saml:Assertion MajorVersion="1" MinorVersion="1" AssertionID="_2546eb2e-a3a6-4cf3-9006-c9f20560097f"Issuer=`http://STS.contoso.com/adfs/services/trust/` IssueInstant="2012-12-23T04:07:30.874Z" xmlns:saml="urn:oasis:names:tc:SAML:1.0:assertion">
    
    …
    
    <saml:Attribute AttributeName="issuerid" AttributeNamespace="[http://schemas.microsoft.com/ws/2008/06/identity/claims](http://schemas.microsoft.com/ws/2008/06/identity/claims)"><saml:AttributeValue>[http://contoso.com/adfs/services/trust/</saml:AttributeValue](http://contoso.com/adfs/services/trust/%3c/saml:AttributeValue)></saml:Attribute>
    - This will again lead to error "Your organization could not sign you in to this service"
    ```

#### Support for subdomains

It's important to note that the"SupportMultipleDomain" switch isn't required when you have a single top-level domain and multiple subdomains. For example, if the domains used for UPN suffixes are @sales.contoso.com, @marketing.contoso.com, and @contoso.com, and the top-level domain (contoso.com in this case) was added first and federated, then you don't need to use the "SupportMultipleDomain" switch. These subdomains are effectively managed within the scope of the parent, and a single AD FS server can be used to handle this scenario.

If however, you have multiple top-level domains (@contoso.com and @fabrikam.com), and these domains also have subdomains (@sales.contoso.com and @sales.fabrikam.com), the "SupportMultipleDomain" switch won't work for the subdomains and these users won't be able to log in.

#### Why will this switch not work, in the above scenario?

Answer:

- For child domain, sharing the same namespace, we don't federate them separately. The federated root domain covers the child as well, which mean that the
federationServiceIdentifier value for the child domain will also be the same as that of parent, that is `https://contoso.com/adfs/services/trust/`.

- But the third claim rule, which ends up picking the UPN suffix for the user to compose the Issuer value ends up with `https://Child1.contoso.com/adfs/services/trust/`, again causing a mismatch and hence the error "Your organization could not sign you in to this service."

  To resolve this issue, modify the third rule such that it ends up generating an Issuer value that matches "FederationServiceIdentifier" for the domain at Microsoft 365 end. Two different rules that can work in this scenario is below. This rule just picks up the root domain from the UPN suffix to compose the Issuer value. For a UPN suffix child1.contoso.com, it will still generate an Issuer value of `https://contoso.com/adfs/services/trust/` instead of `https://Child1.contoso.com/adfs/services/trust/` (with default rule)

:::image type="content" source="media/supportmultipledomain-switch-when-manage-sso/claim-rule.png" alt-text="Screenshot of selecting the Edit Rule option to modify the third rule." border="false":::

#### Customized third rule

Rule 1:

```adoc
c:[Type == `http://schemas.xmlsoap.org/claims/UPN`]
=> issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));
```

Rule 2:

```adoc
c:[Type == `http://schemas.xmlsoap.org/claims/UPN`]
=> issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value =regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*.(com|net|co|org)(.\w\w)?)$", "[http://${domain}/adfs/services/trust/](http://$%7bdomain%7d/adfs/services/trust/)"));
```

**NOTE**

These rules may not apply to all scenarios, but can be customized to ensure that the "Issuerid" value matches "FederationServiceIdentifier" for the domain added/federated at Microsoft 365 end.

The mismatch of federationServiceIdentifier between AD FS and Microsoft 365 for a domain can also be corrected by modifying the "federationServiceIdentifier" for the domain at Microsoft 365 end, to match the "federationServiceIdentifier" for AD FS. But the federationServiceIdentifier can only be configured for ONE federated domain and not all.

Set-MSOLDomainFederationSettings -domain name  Contoso.com –issueruri `http://STS.contoso.com/adfs/services/trust/`

### More information that should help you write your own claim rules.**

- [The Role of the Claim Rule Language](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd807118(v=ws.10))
- [Regular Expressions](/previous-versions/system-center/system-center-2012-R2/hh440535(v=sc.12))

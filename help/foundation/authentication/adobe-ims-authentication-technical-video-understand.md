---
title: Understanding Adobe IMS authentication with AEM on Adobe Managed Services
seo-title: Understanding Adobe IMS authentication with AEM on Adobe Managed Services
description: Adobe Experience Manager introduces Admin Console support for AEM instances and Adobe IMS (Identity Management System) based authentication for AEM on Managed Services.   This integration allows AEM Managed Services customers to manage all Experience Cloud users in a single unified Web console. Users and Groups can be assigned to product profiles associated with AEM instances, granting centrally managed access to the specific AEM instances.
seo-description: Adobe Experience Manager introduces Admin Console support for AEM instances and Adobe IMS (Identity Management System) based authentication for AEM on Managed Services.   This integration allows AEM Managed Services customers to manage all Experience Cloud users in a single unified Web console. Users and Groups can be assigned to product profiles associated with AEM instances, granting centrally managed access to the specific AEM instances.
uuid: abe0ec95-00c9-4125-8566-f400a4c475e0
topic-tags: authentication
products: SG_EXPERIENCEMANAGER/6.4
products: SG_EXPERIENCEMANAGER/6.5
discoiquuid: 39cf83cb-b093-49bd-a187-22adb72e197b
index: y
internal: n
snippet: y
kt: 781
---

# Understanding Adobe IMS authentication with AEM on Adobe Managed Services{#understanding-adobe-ims-authentication-with-aem-on-adobe-managed-services}

Adobe Experience Manager introduces Admin Console support for AEM instances and Adobe IMS (Identity Management System) based authentication for AEM on Managed Services.   This integration allows AEM Managed Services customers to manage all Experience Cloud users in a single unified Web console. Users and Groups can be assigned to product profiles associated with AEM instances, granting centrally managed access to the specific AEM instances.

>[!VIDEO](https://video.tv.adobe.com/v/26170?quality=12)

* Adobe Experience Manager IMS authentication support is only for "internal" users (authors, reviewers, administrators, developers, etc.), and not external end-users such as Web Site visitors.
* [Admin Console](https://adminconsole.adobe.com/) will represent AEM Managed Services customers as IMS Orgs and their Instances as Product Contexts. Admin Console System and Product Admins can define and manage.
* AEM Managed Services sync your topology with Admin Console, creating a 1-to-1 mapping between Product Context and AEM instance.
* Product Profiles in Admin Console will determine which AEM instances a user can access.
* Authentication support includes customer SAML 2 compliant IDPs for SSO.
* Only Enterprise or Federated IDs (for customer SSO) will be supported (Personal Adobe IDs are not supported).

*&#42; This feature is supported for AEM 6.4 SP3 and later for Adobe Managed Services customers.*


## Best practices {#best-practices}

Applying permissions and access at the user level should be avoided in both Admin Console and in Adobe Experience Manager.

In Admin Console users should be granted access via User Groups at the Product Context level. User Groups are typically best expressed by logical role within the organization to promote the User Groups' usability across Adobe Experience Cloud products.

In Adobe Experience Manager, user groups synced from Adobe IMS should be in term added to [AEM-provided user groups](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security.html), which come preconfigured with the appropriate permissions to execute specific sets of tasks in AEM. Users synced from Adobe IMS should not be directly added to [AEM-provided user groups](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security.html). 

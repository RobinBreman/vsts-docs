---
title: Troubleshooting Azure Resource Manager service connections in VSTS and TFS
description: DevOps CI CD - Troubleshoot Azure Resource Manager service connections in VSTS and Team Foundation Server (TFS)
ms.assetid: B43E78DE-5D73-4303-981F-FB86D46F0CAE
ms.prod: devops
ms.technology: devops-cicd
ms.topic: conceptual
ms.manager: douge
ms.author: ahomer
author: alexhomer1
ms.date: 07/09/2018
monikerRange: '>= tfs-2015'
---

# Troubleshoot Azure Resource Manager service connections

**VSTS | TFS 2018 | TFS 2017 | TFS 2015**

::: moniker range="<= tfs-2018"
[!INCLUDE [temp](../_shared/concept-rename-note.md)]
::: moniker-end

This topic will help you resolve issues you may encounter when creating
a connection to Microsoft Azure using an **Azure Resource Manager** service connection
for your DevOps CI/CD processes.

<a name="whathappens"></a>
## What happens when you create a Resource Manager service connection?

You open the **Add Azure Resource Manager srvice connection** dialog,
provide a connection name, and select a subscription from drop-down
list of your subscriptions.  

![The Add Azure Resource Manager srvice connection dialog](_img/azure-rm-endpoint/azure-rm-endpoint-01.png)

When you choose **OK**, the system:

1. Connects to the Azure Active Directory (AAD) tenant for to the selected subscription
1. Creates an application in AAD on behalf of the user
1. After the application has been successfully created, assigns the application as a contributor to the selected subscription
1. Creates an Azure Resource Manager service connection using this application's details

<a name="troubleshoot"></a>
## How to troubleshoot errors that may occur

Errors that may occur when the system attempts to create the service connection include:

* [Insufficient privileges to complete the operation](#privileges)
* [Failed to obtain an access token](#sessionexpired)
* [A valid refresh token was not found](#sessionexpired)
* [Failed to assign contributor role](#contributorrole)

<a name="privileges"></a>
### Insufficient privileges to complete the operation

This typically occurs when the system attempts to create an
application in AAD on your behalf.

![Insufficient privileges to complete the operation error](_img/azure-rm-endpoint/azure-rm-endpoint-02.png)

This is a permission issue that may be due to the following causes:

* [The user has only guest permission in the directory](#guestonly)
* [The user is not authorized to add applications in the directory](#notauthtoadd)

<a name="guestonly"></a>
#### The user has only guest permission in the directory

The best approach to resolve this issue, while granting only the minimum additional permissions
to the user, is to increase the Guest user permissions as follows:

1. Sign into the Azure portal at [https://portal.azure.com](https://portal.azure.com) using an administrator account.
   The account should be an [owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner),
   [global administrator](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal#global-administrator), or
   [user account administrator](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal#user-account-administrator). 

1. Choose **Azure Active Directory** in the left navigation bar.

1. Ensure you are editing the appropriate directory corresponding to the user subscription. If not, select **Switch directory** and log in using the appropriate credentials if required.

1. In the **MANAGE** section choose **Users**.

1. Choose **User settings**.

1. In the **External users** section, change **Guest user permissions are limited** to **No**.

Alternatively, if you are prepared to give the user additional (administrator-level) permissions,
you can make the user a member of the **Global administrator** role as follows:

1. Sign into the Azure portal at [https://portal.azure.com](https://portal.azure.com) using an administrator account.
   The account should be an [owner](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#owner),
   [global administrator](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles-azure-portal#global-administrator), or
   [user account administrator](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles-azure-portal#user-account-administrator). 

1. Choose **Azure Active Directory** in the left navigation bar.

1. Ensure you are editing the appropriate directory corresponding to the user subscription. If not, select **Switch directory** and log in using the appropriate credentials if required.

1. In the **MANAGE** section choose **Users**.
   
1. Use the search box to filter the list and then choose the user you want to manage.

1. In the **MANAGE** section choose **Directory role** and change the role to **Global administrator**.

1. Save the change.

It typically takes 15 to 20 minutes to apply the changes globally.
After this period has elapsed, the user can retry creating the service connection.

<a name="notauthtoadd"></a>
#### The user is not authorized to add applications in the directory

You must have permission to add integrated applications in the directory.
The directory administrator has permission to change this setting, as follows:

1. Choose **Azure Active Directory** in the left navigation bar.

1. Ensure you are editing the appropriate directory corresponding to the user subscription. If not, select **Switch directory** and log in using the appropriate credentials if required.

1. In the **MANAGE** section choose **Users**.

1. Choose **User settings**.

1. In the **App registrations** section, change **Users can register applications** to **Yes**.

<a name="sessionexpired"></a>
### Failed to obtain an access token or A valid refresh token was not found

These errors typically occur when your session has expired.

![Errors when the users session has expired](_img/azure-rm-endpoint/azure-rm-endpoint-08.png)

To resolve these issues:

* Sign out of VSTS or TFS.
* Open an InPrivate or incognito browser window and navigate to [https://visualstudio.microsoft.com/team-services/](https://visualstudio.microsoft.com/team-services/).
* If you are prompted to sign out, do so.
* Sign in using the appropriate credentials.
* Choose the account you want to use from the list.
* Select the project you want to add the service connection to.
* Create the service connection you need by opening the **Settings** page, selecting the **Services** tab,
  choosing **New service connection**, and selecting **Azure Resource Manager**.

<a name="contributorrole"></a>
### Failed to assign Contributor role

This error typically occurs when you do not have **Write** permission
for the selected Azure subscription when the system attempts to assign
the **Contributor** role.

![Failed to assign Contributor role error](_img/azure-rm-endpoint/azure-rm-endpoint-09.png)

To resolve this issue, ask the subscription administrator to
[configure your identity in an **Admin Access** role](https://docs.microsoft.com/en-gb/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

[!INCLUDE [rm-help-support-shared](../_shared/rm-help-support-shared.md)]

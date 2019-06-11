![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png)

<div class="MCWHeader1">
Azure Stack
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
December 2018
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.
© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

- [Azure Stack hands-on lab step-by-step](#azure-stack-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Help references](#help-references)
  - [Requirements](#requirements)
  - [Exercise 1: Create Azure Stack resources](#exercise-1-create-azure-stack-resources)
    - [Task 1: Deploy VMs (portal)](#task-1-deploy-vms-portal)
    - [Task 2: Create a website (powershell)](#task-2-create-a-website-powershell)
    - [Task 3: Use QuickStart templates](#task-3-use-quickstart-templates)
  - [Exercise 2: RBAC and multi-tenancy](#exercise-2-rbac-and-multi-tenancy)
  - [Task 1: Configure Role Based Access Control (RBAC)](#task-1-configure-role-based-access-control-rbac)
  - [Task 4: Enable Multi-tenancy](#task-4-enable-multi-tenancy)
  - [The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.](#the-following-azure-resource-manager-endpoint-is-for-the-asdk-if-you-are-in-a-multinode-environment-contact-your-operator-or-service-provider-to-get-the-endpoint)
  - [Replace the value below with the guest tenant directory.](#replace-the-value-below-with-the-guest-tenant-directory)
  - [Exercise 3: Management and Operations](#exercise-3-management-and-operations)
    - [Task 1: Configure Azure Stack infrastructure backup](#task-1-configure-azure-stack-infrastructure-backup)
    - [Task 2: Log Collection](#task-2-log-collection)
    - [Task 3: Azure Monitor, Update and Configuration Management](#task-3-azure-monitor-update-and-configuration-management)
  - [After the hands-on lab](#after-the-hands-on-lab)


# Azure Stack hands-on lab step-by-step

## Abstract and learning objectives

In this hands-on lab, you will perform some common operator tasks in Azure Stack using PowerShell, portal, and ARM templates.

At the end of this hands-on lab, you will be better able to deploy and manage solutions running on Azure Stack.


## Help references
|    |            |
|----------|:-------------:|
| **Description** | **Links** |
| Azure Stack overview  | <https://azure.microsoft.com/en-us/overview/azure-stack/> |
| Azure Stack use cases | <https://azure.microsoft.com/en-us/overview/azure-stack/use-cases/> |
| Azure Stack features | <https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-key-features> |
| Azure Stack planning considerations | <https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-planning-considerations> |
| Azure Stack documentation | <https://docs.microsoft.com/en-us/azure/azure-stack/> |
| Azure Stack Operator documentation | <https://docs.microsoft.com/en-us/azure/azure-stack/> |
| Azure Stack networking | <https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-network-overview/> |
| Azure Stack to Azure Global VPN | <https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-connect-vpn> |
| Register Azure Stack with your subscription | <https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-register> |
| Deploy the Azure Stack App Service resource provider | <https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-app-service-deploy> |
| Deploy the Azure Stack SQL resource provider | <https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-sql-resource-provider-deploy#deploy-the-resource-provider> |
| Deploy apps to Azure and Azure Stack | <https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-solution-pipeline> |
| White paper | <https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/> |
| PowerShell for Azure Stack | <https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-powershell-install> |
| Azure Stack marketplace | <https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-marketplace-azure-items> |


## Requirements

-   Microsoft Azure subscription 

## Exercise 1: Create Azure Stack resources

### Task 1: Deploy VMs (portal)

1. Connect to the Azure Stack user-portal -> https://portal.local.azurestack.external/
   >Note: if you've followed the steps in the "Before the HOL" document, login with the users that already have subscriptions assigned. Otherwise, you'll need to create new user-subscriptions.

2. Click "Create new resource" and deploy these VMs from the Marketplace:
   - Windows Server 20016
   - SQL Server 2017 on WS2016
  ![](media/../../OldVer%20/media/HOL%20step-by-step%20-%20Azure%20Stack-11-36-57.png)

  > Note: In the "Before the HOL" document we've configured the marketplace and downloaded the required images. If they do not appear here, please go back and follow the steps described in the "Before the HOL" document.

### Task 2: Create a website (powershell)

In this exercise, you must connect to the USER (tenant) endpoint, since you will be creating a web app as if you were a user of Azure Stack, not an operator.

1. Connect to the tenant endpoint in PowerShell (either ISE or VSCode) - https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-configure-user 
2. Download appservice_Install01.json and appservice_Install_parameter.json files from address: https://github.com/JurislavP/Azure_AzureStack_Dev/tree/master/App01 

2.	Inspect the appservice_Install_parameter.json file, so you can see what adjustments you could make to fit your environment

3.	Open PowerShell command prompt to the USER tenant of Azure Stack and run following commands. You do not do this as an ADMIN becasue you want the resource group to be available to the user:

    ```Powershell
    New-AzureRmResourceGroup -Name "name of resource group" -Location local

    New-AzureRmResourceGroupDeployment -Name WebApp01 -ResourceGroupName "name of resource group" -TemplateFile Location of ARM template file\appservice_Install01.json -TemplateParameterFile Location of ARM template parameter file\appservice_Install_parameter.json
    ```

### Task 3: Use QuickStart templates
Azure Stack QuickStart Templates (https://github.com/Azure/AzureStack-QuickStart-Templates) is a GitHub repo which contains a number of templates which can be used a starting point.

There are several options for using these templates:
- use GitHub, navigate to the right template, copy the code in the "azuredeploy.json" file and use that to create a new "Template deployment" in the Azure Stack portal
- in the Azure Stack user portal, click on new "Template Deployment", edit the template and click on "Quickstart template" to select the GitHub QuickStart template
  ![](media/../../OldVer%20/media/HOL%20step-by-step%20-%20Azure%20Stack-12-57-14.png)
- you can also download the "azuredeploy.json", edit it, and upload it in the "load file" option of the "Template deployment"

Using the Azure Stack QuickStart templates deploy a VM Scale Set of Windows VMs.

> slightly challenging: fork the Azure Stack QuickStart repo and create an "Deploy to Azure" button which actually deploys the a VM Scale Set of Windows VMs to Azure Stack.
> More advanced: use the Azure QuickStart templates to deploy an IIS server (VM-DSC-Extension-IIS-Server) on Azure Stack.

## Exercise 2: RBAC and multi-tenancy

### Task 1: Configure Role Based Access Control (RBAC)

1.  Go to your respective AAD that is used for installation of ASDK environment

2.  Add following users: **Admin01, Admin02, Admin03**

3.  Additionally, create following users: **User01, User02, User03**

4.  Create a Group in Azure Active Directory called **ASTACK2018Admins**

    a.  Group Type -- Security

    b.  Group Name - **ASTACK2018Admins**

    c.  Group Description - **ASTACK2018Admins**

    d.  Membership Type - Assigned

5.  Add the users **Admin01, Admin02, Admin03** to the
    **ASTACK2018Admins** group

6.  Browse to the Admin portal for Azure Stack.

7.  In the Azure Stack Administrative Portal, browse to "Subscriptions"

8.  Select the **Default Provider Subscription**

9.  Add additional administrators by selecting the **Access control (IAM)** blade and select the **Add** button.

10. In the **Add Permissions** blade, use the **Role** drop-down menu to
    select **Owner**.

11. In the **Select** textbox, type Azure Active Directory group **"ASTACK2018Admins"** to search for the group.  Select the **"ASTACK2018Admins"** group from the search results and click **Save** to add the co-administrators.

### Optional Task 2: Enable Multi-tenancy

**Requirements**

1. Guest Azure Active Directory that is not used for Azure Stack installation (this AADis not part of the lab environment prepared for you - you must create or bring your own AAD tenant if you want to do this exercise)
2. Global Admin account for guest AAD
3. Azure Stack PowerShell Tools for configuration of Guest AAD

Enabling multi tenancy on Azure Stack is joint effort between Azure Stack Operator and Global Admin of guest AAD. 

**Azure Stack Operator Actions**

1. Import the Connect and Identity modules from Azure Stack Tools:
```Powershell
Import-Module .\Connect\AzureStack.Connect.psm1
Import-Module .\Identity\AzureStack.Identity.psm1
```
2. Configure Azure Stack directory
```Powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
 ```

**Global Admin of guest AAD Actions**

1. Import the Connect and Identity modules from Azure Stack Tools:
```Powershell
Import-Module .\Connect\AzureStack.Connect.psm1
Import-Module .\Identity\AzureStack.Identity.psm1
```
2. Registering Azure Stack with the guest directory
```Powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
 ```

## Exercise 3: Management and Operations
### Task 1: Configure Azure Stack infrastructure backup

- Enable backup through Portal -> https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-backup-enable-backup-console
- Enable Backup for Azure Stack with PowerShell -> https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-backup-enable-backup-powershell

> note: for time and space considerations, you shouldn't start this backup in the lab, but can observe the steps required to start a job immediatly here: https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-backup-back-up-azure-stack and the recovery process here: https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-backup-recover-data



### Task 2: Log Collection

Whenever there is an issue, the Azure Stack Administration portal displays information and potential fixes you can follow. When the problem is complex, you will need to **Connect to PEP** and collect more logs.

- connect to PEP - https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-privileged-endpoint

        ```Powershell
        winrm s winrm/config/client '@{TrustedHosts="AzS-ERCS01"}'
        #use azurestack\cloudadmin for the ASDK
        $cred = Get-Credential
        Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint -Credential $cred

        ```
- Use an Azure Stack Storage Account as a storage location and collect logs for URP,ECE, Seedring and SeedRingServices ->  https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-diagnostics


### Task 3: Azure Monitor, Update and Configuration Management

>Note: for this exercise you will need access to an Azure Subscription - the management of these resources will not create any cost other than Azure Storage.

- Configure Azure Monitor, Update Management, Change Tracking, Inventory, and MAP solutions in Azure for your Azure Stack VMs - https://aka.ms/azstackupdatemgmt 




## After the hands-on lab 

Duration: 10 minutes

In this final task you will clean up the Azure Resources that you have create for the hands-on lab. This task is optional.

1.  If provisioned using the Azure Stack Developer Kit in an Azure VM, delete the resource group your Azure Stack Host VM is running in.

2.  If running on your own Developer Kit, delete all the resource groups from the Azure Stack portal that you created during the execution of this lab.

You should follow all steps provided *after* attending the Hands-on lab.

![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png)

<div class="MCWHeader1">
Azure Stack - Opeartions
</div>

<div class="MCWHeader2">
Before the hands-on lab setup guide
</div>

<div class="MCWHeader3">
June 2019
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.# Azure Stack setup

**Contents**

- [Azure Stack before the hands-on lab setup guide](#azure-stack-before-the-hands-on-lab-setup-guide)
  - [Requirements](#requirements)
  - [Before the hands-on lab](#before-the-hands-on-lab)
    - [Task 1: Prepare the AzStack Operator station](#task-1-prepare-the-azstack-operator-station)
    - [Task 2: Download VM Images to Azure Stack](#task-2-download-vm-images-to-azure-stack)
    - [Task 3: Create a new quota, plan, and offer](#task-3-create-a-new-quota-plan-and-offer)
  - [Task 4: Create a new Quotas / Plans](#task-4-create-a-new-quotas--plans)
  - [Task 5: Create a new offer and make it public](#task-5-create-a-new-offer-and-make-it-public)
  - [Task 2: Create assigned and self-service user subscriptions](#task-2-create-assigned-and-self-service-user-subscriptions)

# Azure Stack before the hands-on lab setup guide 

## Requirements

-   Access to Azure Stack Development Kit
-   AAD tenantID
-   ....

## Before the hands-on lab

Duration: !? hours 

### Task 1: Prepare the AzStack Operator station

1.  Connect to the ASDK VM
2.  Install tools

    > Note: except VScode, most of the next steps should be already configured on your VM - please check that the tools are there and you are able to configure them. VSCode is optional - you can perform all of the activities in the labs using just PowerShell, but you may want to explore the VSCode user interface, becasue it makes interacting with Azure Stack using PowerSHell and ARM templaes more powerful.

- Install VSCode - https://code.visualstudio.com/
- Install the following VSCode extensions
    ```powershell
    code --install-extension msazurermtools.azurerm-vscode-tools
    code --install-extension ms-vscode.powershell
    code --install-extension ms-azuretools.vscode-azurestorage
    ```
- Check if Azure Stack Powershell and the tools are installed (https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-install), if not (make sure you are running as admin):

  ```powershell
  Import-Module -Name PowerShellGet -ErrorAction Stop
  Import-Module -Name PackageManagement -ErrorAction Stop
  Register-PSRepository -Default
  Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper
  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.7.2
  # Change directory to the root directory. 
  cd \
  # Download the tools archive.
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip
  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force
  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```
3. Configure the Azure Stack PowerShell environment (https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-configure-admin)
    ```powershell
    # Register an Azure Resource Manager environment that targets your Azure Stack instance.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
  

> Note: type "Get-AzureRMSubscription" and note down the "TenantID".

### Task 2: Download VM Images to Azure Stack

In this task, you will download the required images and artifacts which are needed for the next lab exercies:

1.  From within the Azure Stack Admin portal, click **Marketplace management** -> **Add from Azure**:

    ![Marketplace management is selected in the Azure Stack Admin portal.](media/Before%20the%20HOL%20-%20Azure%20Stack-17-50-50.png)

2. Download the required images. Some of these images may already be present in your lab environment – you can inspect and download any of the gallery items that are interesting to you:
   -   SQL Server 2017 on Windows Server 2016 Image
   -   SQL IaaS Extension
   -   Windows Server 2016 Data Center Pay for Use
   -   Azure Monitor, Update and Configuration Management (extension)
   -   Azure Monitor Dependency Agent (extension)

3.  Once the products are downloaded, you will receive a notification.

    > **Note:** These downloads will take some time depending upon your Azure Stack connectivity. Once they are downloaded they will appear in your Tenant Marketplace. You can proceed with the next Tasks, if they do not require image creation.

### Task 3: Create a new quota, plan, and offer

In Azure, services are designed, deployed and offered to various regions by Microsoft engineers and operators. In Azure Stack the cloud operator deploys these services and makes them available to subscribed users using [quotas, plans, and offers](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-plan-offer-quota-overview). This needs to be carefully designed in order to take into account capacity planning and growth/usage patterns as each of these elements are connected and define both the capabilities and quantities that users can consume. In these next exercises we will create quotas, plans, and offers:

## Task 4: Create a new Quotas / Plans


1.  In the **Azure Stack -- Administration** portal, select **All Services** and select **Plans** from **Administrative Resources**.

    > Tip: the "Search bar" on the middle of the Administration portal is your key to finding shortcuts to almost anything in the portal:
    
    ![](media/Before%20the%20HOL%20-%20Azure%20Stack-18-13-18.png)

2.  In the **Plans** blade, select the **Add** buttona and in the **New Plan** blade, set the following values and select
    **Create**:

    a.  Display name: **Plan 1**

    b.  Resource name: **new-plan-1**

    c.  Resource Group: Select the **Create new** radio
        button and in the textbox below, specify **NewRG1** as the name
        of the new resource group.

    d.  Services: In the Services blade, select
        **Microsoft.Compute**, **Microsoft.Network,**
        **Microsoft.Storage** and **Microsoft.Web**

    e.  Quotas: In the **Quotas** blade, select each of
        the services outlined above and select their **Default Quota**.

        > Note: Click "Create New" to explore each of the quotas and options available

    ![](media/Before%20the%20HOL%20-%20Azure%20Stack-18-17-31.png)

4.  Once complete, click **Create** and wait for the deployment
    operation to complete.

## Task 5: Create a new offer and make it public

    > Note: if an offer is public, any user with access to the Azure Stack Tenant Portal can create a subscription based on it. This is great for test environemnts, but should be carefully considered in a production environment. 
1.  In the **Azure Stack -- Administration** portal, select **All
    Services** and select **Offers** from **Administrative Resources**.

2.  In the **Offers** blade, select the **Add** button.

3.  In the **New Offer** blade, set the following values and select
    **Create**:

    a.  Display name: **Offer 1**

    b.  Resource name: **offer-1**

    c.  Resource Group: Select the **Use existing** radio
        button and in the textbox below, specify **NewRG1** as the name
        of the new resource group.

    d.  Base plans: In the Plan blade, select the new plan
        created in the steps above (**New Plan 1**).

    e.  Add-on plans: Accept the default value of **None**.

  > note: you can make the offer public when it is created, or after. This can be toggled at any time.

  ![](media/Before%20the%20HOL%20-%20Azure%20Stack-08-50-46.png)

4.  Once complete, click **Create** and wait for the deployment
    operation to complete.

5.  In the **Offers** blade, select the **Refresh** button.

6.  In the **Offers** blade, select the offer you just created
    (**new-offer-1**).

7.  In the **new-offer-1** blade, select the **Overview** blade. In the
    **Change state** drop-down menu select **Public**.

  ![Change state of the offer](media/Before%20the%20HOL%20-%20Azure%20Stack-09-29-14.png)

Once you have completed this, your users can create subscriptions aligned to the offers you have created.


## Task 2: Create assigned and self-service user subscriptions 

Azure Stack user-subscriptions allow users to access the Azure Stack services being offered. (more info here https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-plan-offer-quota-overview#subscriptions).

  > Note: Users can subscribe to "Public Offers", or Azure Stack Operators can create a user-subscription on behalf of a user. When the Azure Stack Operator creates this user-subscription, it can also specify the "owner" of that user-subscription.  

To add a new subscription, perform the following tasks in your Azure Stack environment:

1. As an Azure Stack Operator (serviceadmin@XYZ.onmicrosoft.com), log in to the Azure Stack Admin Portal assign a user user1@XYZ.onmicrosoft.com to your newly created public offer
1.  In the **Azure Stack -- Administration** portal, select **All
    Services** and select **Offers** from **Administrative Resources**.

2.  In the **offer-1** blade, select the **Overview** blade and
    select **Users subscription**.

3.  In the **New user subscription** blade, set the following values and
    select **OK**:

    a.  Display name: **Assigned Subscription 1**

    b.  User: **user1@ XYZ.onmicrosoft.com**

    c.  Directory Tenant: Accept the default value

        i. Notice that you can select new AAD

    d.  Offer: Accept the default value of the offer created in the steps above (**Offer 1**).

 **As an Azure Stack User (user2@XYZ.onmicrosoft.com), log in to the Azure Stack User Portal and get a new subscription for your     newly created public offer.**

   > Note: Login to the Azure Stack user portal and try to "Get a Subscription" - notice the Offer you've made public earlier. If you go back to the Admin portal and toggle the offer to "Private", you can notice how it will dissapear from the user side.





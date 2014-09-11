<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo" />

#How to create a hybrid deployment of RemoteApp

There are two kinds of RemoteApp deployment: 

- Cloud: resides completely in Azure and is created using the **Quick create** option in the Azure management portal.  
- Hybrid: includes a virtual network for on-premises access and is created using the **Create with VPN** option in the management portal.

This tutorial walks you through the process of creating a hybrid deployment. There are seven steps: 

1.	Create a [custom template image for RemoteApp](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-custom-image/).
2.	Create a RemoteApp service.
2.	Link to a virtual network.
3.	Link a template image.
4.	Configure directory synchronization. RemoteApp requires this to synchronize users, groups, contacts, and passwords from your on-premises Active Directory to your Azure Active Directory tenant.
5.	Publish RemoteApp programs.
6.	Configure user access.

**Before you begin**

You need to do the following before creating the service:

- Sign up for the preview of RemoteApp. You can do that at [http://azure.microsoft.com/en-us/services/remoteapp/](http://azure.microsoft.com/en-us/services/remoteapp/).
- Create a user account in Active Directory to use as the RemoteApp service account. Restrict the permissions for this account so that it can only join machines to the domain.
- Gather information about your on-premises network: IP address information and VPN device details.
- Install the [Azure PowerShell](http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/) module.
- Gather information about the users and groups that you want to grant access to. This can be either Microsoft account information or Active Directory work account information for users or groups.
- Create your template image. A RemoteApp template image contains the apps and programs that you want to publish for your users. See [How to create a custom template image for RemoteApp](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-custom-image/) for detailed steps. 






## **Step 1: Create a RemoteApp service** ##



1. In the [Azure Management Portal](http://manage.windowsazure.com), go to the RemoteApp page.
2. Click **New > Create with VPN**.
3. Enter a name for your service, and click **Create RemoteApp service**.

After your RemoteApp service has been created, go to the RemoteApp **Quick Start** page to continue with the set up steps.

## **Step 2: Link to a virtual network** ##

A virtual network lets your users access data on your local network through RemoteApp remote resources. There are four steps to configure your virtual network link:

1. On the Quick Start page, click **link a remoteapp virtual network**. 
2. Choose whether to create a new virtual network or use an existing. For this tutorial, we'll create a new network.
3. Provide the following information for your new network:  
      - Name
      - Virtual network address space
      - Local address space
      - DNS server IP address
      - VPN IP address

	See [Configure a Site-to-Site VPN in the the Management Portal](http://msdn.microsoft.com/library/azure/dn133795.aspx) for more information.

4. Next, back on the Quick Start page, click **get script** to download a script to configure your VPN device to connect to the virtual network you just created. You'll need the following information about the VPN device: 
	- Vendor
	- Platform
	- Operating system

	Save the script and run it on the VPN device. 

	**Note:** After you run this script, the virtual network will move to the Ready state - this may take 5-7 minutes. Until the network is ready, you won't be able to use it.

5. Finally, again on the Quick Start page, click **join local domain**. Add the RemoteApp service account to your local Active Directory domain. You will need the domain name, organizational unit, service account user name and password.


## **Step 3: Link to a RemoteApp template image** ##

A RemoteApp template image contains the programs that you want to share with users. You can either upload the new template image you created (from the instructions in [How to create a custom template image for RemoteApp](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-custom-image/)) or link to an existing image (one already uploaded to Azure).

If you are uploading the new image, you need to enter the name and choose the location for the image. On the next page of the wizard, you'll see a set of PowerShell cmdlets - copy and run these cmdlets from an elevated Azure PowerShell prompt to upload the specified image.

If you are linking to an existing template image, simply specify the image name, location, and associated Azure subscription.



## **Step 4: Configure Active Directory directory synchronization** ##

RemoteApp requires directory synchronization between Azure Active Directory and your on-premise Active Directory to synchronize users, groups, contacts, and passwords to your Azure Active Directory tenant. See [Directory synchronization roadmap](http://msdn.microsoft.com//library/azure/hh967642.aspx) for planning information and detailed steps.

## **Step 5: Publish RemoteApp programs** ##

A RemoteApp program is the app or program that you provide to your users. It is located in the template image you uploaded for the service. When a user accesses a RemoteApp program, the program appears to run in their local environment, but it is really running in Azure. 

Before your users can access RemoteApp programs, you need to publish them to the end-user feed – a list of available programs that your users access through the Azure portal.
 
You can publish multiple programs to your RemoteApp service. From the RemoteApp programs page, click **Publish** to add a program. You can either publish from the Start menu of the template image or by specifying the path on the template image for the program. If you choose to add from the Start menu, choose the program to publish. If you choose to provide the path to the program, provide a name for the program and the path to where the program is installed on the template image.

## **Step 6: Configure user access** ##

Now that you have created your RemoteApp service, you need to add the users and groups that you want to be able to use your remote resources. The users or groups that you provide access to need to exist in the Active Directory tenant associated with the subscription you used to create this RemoteApp service.

1.	From the Quick Start page, click **Configure user access**. 
2.	Enter the work account or group name (from Active Directory) or Microsoft account that you want to grant access for.

	For users, make sure that you use the “user@domain.com” format. For groups, enter the group name.

3.	Once the users or groups are validated, click **Save**.


## Next steps ##
That's it - you have successfully created and deployed your RemoteApp hybrid deployment. The next step is to have your users download and install the Remote Desktop client. You can find the URL for the client on the RemoteApp Quick Start page. Then, have users log into the client and access the RemoteApp programs you published.



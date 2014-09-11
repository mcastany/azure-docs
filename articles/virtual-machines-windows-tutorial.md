<properties linkid="manage-windows-tutorial-virtual-machine-gallery" urlDisplayName="Create a virtual machine" pageTitle="Create a virtual machine running Windows Server in Azure" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server. " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />



# Create a Virtual Machine Running Windows Server #

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Azure Portal</a><a href="/en-us/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Azure Preview Portal</a></div>

This tutorial shows you how easy it is to create an Azure virtual machine (VM) running Windows Server, using the Image Gallery in the Azure Management Portal. The Image Gallery offers a variety of images, including Windows operating systems, Linux-based operating systems, and application images. 

> [WACOM.NOTE] You don't need any experience with Azure VMs to finish this tutorial. However, you do need an Azure account. You can create a free trial account in just a couple of minutes. For details, see [Create an Azure account](http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/). 

This tutorial shows you:

- [How to create the virtual machine](#createvirtualmachine)
- [How to log on to the virtual machine after you create it](#logon)
- [How to attach a data disk to the new virtual machine](#attachdisk)

If you'd like to know more, see [Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>How to create the virtual machine##

This section shows you how to use the **From Gallery** option in the Management Portal to create the virtual machine. This option provides more configuration choices than the **Quick Create** option. For example, if you want to join a virtual machine to a virtual network, you'll need to use the **From Gallery** option.

> [WACOM.NOTE] You can also try the richer, customizable [Azure Preview Portal](https://portal.azure.com) to create a virtual machine, automate the deployment of multi-machine application templates, use enhanced VM monitoring and diagnostics features, and more. The available VM configuration options in the two Portals overlap substantially but aren't identical.  

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>How to log on to the virtual machine after you create it ##

This section shows you how to log on to the virtual machine so you can manage its settings and the applications that you'll run on it.

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <a id="attachdisk"> </a>How to attach a data disk to the new virtual machine ##

This section shows you how to attach an empty data disk to the virtual machine. See the [Attach a Data Disk Tutorial](http://www.windowsazure.com/en-us/documentation/articles/storage-windows-attach-disk/) for more information on attaching empty disks as well as how to attach existing disks.

1. Sign in to the Azure [Management Portal](http://manage.windowsazure.com).

2. Click **Virtual Machines**, and then select the **MyTestVM** virtual machine.

	![Select MyTestVM](./media/virtual-machines-windows-tutorial/selectvm.png)
	
3. You may be taken to the Quick Start page first. If so, select **Dashboard** from the top.

	![Select Dashboard](./media/virtual-machines-windows-tutorial/dashboard.png)

4. On the command bar, click **Attach**, and then click **Attach Empty Disk** when it pops up.

	![Select Attach from the command bar](./media/virtual-machines-windows-tutorial/commandbarattach.png)	

5. The **Virtual Machine Name**, **Storage Location**, **File Name**, and **Host Cache Preference** are already defined for you. All you have to do is enter the size that you want for the disk. Type **5** in the **Size** field. Then click the check mark to attach the empty disk to the virtual machine.

	![Specify the size of the empty disk](./media/virtual-machines-windows-tutorial/emptydisksize.png)	
	
	>[WACOM.NOTE] All disks are created from a VHD file in Windows Azure storage. Under **File Name**, you can provide the name for the VHD file that is added to storage, but Azure generates the name of the disk automatically.

6. Return to the dashboard to verify that the empty data disk was successfully attached to the virtual machine. It will be listed as a second disk in the **Disks** list along with the OS Disk.

	![Attach empty disk](./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png)

	After you attach the data disk to the virtual machine, the disk is offline and not initialized. Before you can use it to store data, you'll need to log on to the virtual machine and initialize the disk.

7. Connect to the virtual machine by using the steps in the previous section, [How to log on to the virtual machine after you create it] (#logon).

8. After you log on to the virtual machine, open **Server Manager**. In the left pane, select **File and Storage Services**.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/fileandstorageservices.png)

9. Select **Disks** from the expanded menu.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/selectdisks.png)	
	
10. In the **Disks** section, there are three disks in the list: disk 0, disk 1, and disk 2. Disk 0 is the OS disk, disk 1 is a temporary resource disk (which should not be used for data storage), and disk 2 is the data disk you have attached to the virtual machine. Note that the data disk has a capacity of 5 GB as specified earlier. Right-click disk 2 and then select **Initialize**.

	![Start initialization](./media/virtual-machines-windows-tutorial/initializedisk.png)

11. Click **Yes** to start the initialization process.

	![Continue initialization](./media/virtual-machines-windows-tutorial/yesinitialize.png)

12. Right-click disk 2 again and select **New Volume**. 

	![Create the volume](./media/virtual-machines-windows-tutorial/initializediskvolume.png)

13. Complete the wizard using the default values provided. When the wizard is done, a new volume is listed in the **Volumes** section. 

	![Create the volume](./media/virtual-machines-windows-tutorial/newvolumecreated.png)

	The disk is now online and ready to use with a new drive letter. 
	
##Next Steps 

To learn more about configuring Windows virtual machines on Azure, see these articles:

[How to Connect Virtual Machines in a Cloud Service](http://www.windowsazure.com/en-us/documentation/articles/cloud-services-connect-virtual-machine/)

[How to Create and Upload your own Virtual Hard Disk containing the Windows Server Operating System](http://www.windowsazure.com/en-us/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Attach Data Disks to a Virtual Machine](http://www.windowsazure.com/en-us/documentation/articles/storage-windows-attach-disk/)

[Manage the Availability of Virtual Machines](http://www.windowsazure.com/en-us/documentation/articles/manage-availability-virtual-machines/)

[About virtual machines in Azure]: #virtualmachine
[How to create the virtual machine]: #custommachine
[How to log on to the virtual machine after you create it]: #logon
[How to attach a data disk to the new virtual machine]: #attachdisk
[How to set up communication with the virtual machine]: #endpoints

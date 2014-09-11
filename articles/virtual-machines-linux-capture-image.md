<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />


# How to Capture a Linux Virtual Machine to Use as a Template##

This article shows you how to capture a Linux virtual machine so you can use it as a template to create other virtual machines. This virtual machine template includes the OS disk and any data disks attached the virtual machine. It doesn't include networking configuration, so you'll need to configure that when you create the other virtual machines that use the template.

After you capture the virtual machine, it's available under **My Images** when you create a virtual machine. The image file is stored as a VHD in your storage account. For more information about images, see [Manage Disks and Images] [].

##Before You Begin##

These steps assume that you've already created an Azure virtual machine and configured the operating system, including attaching any data disks. If you haven't done this yet, see these instructions:

- [How to Create a Custom Virtual Machine] []
- [How to Attach a Data Disk to a Virtual Machine] []

##Capture the Virtual Machine##

1. Connect to the virtual machine by clicking **Connect** on the command bar. For details, see [How to Log on to a Virtual Machine Running Linux][].

2. In the SSH window, type the following command and then enter the password for the account that you created on the virtual machine.  Note that the output from `waagent` may vary slightly depending on the version of this utility:

	`sudo waagent -deprovision`

	![Deprovision the virtual machine](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)


3. Type **y** to continue.

	![Deprovision of virtual machine successful](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4. Type **Exit** to close the SSH client.

5. In the [Management Portal](http://manage.windowsazure.com), select the virtual machine, and then click **Shut down**.

6. When the virtual machine is stopped, on the command bar, click **Capture** to open the **Capture the Virtual Machine** dialog box.

7.	In **Image Name**, type a name for the new image.

8.	All Linux images must be *deprovisioned* by running the `waagent` command with the `-deprovision` option. Click **I have run waagent-deprovision on the virtual machine** to indicate that the operating system is prepared to be an image.

9.	Click the check mark to capture the image.

	The new image is now available under **Images**. The virtual machine is deleted after the image is captured.

	![Image capture successful](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)

##Next Steps##
The image is ready to be used as a template to create virtual machines. To do this, you'll create a custom virtual machine by using the **From Gallery** method and select the image you just created. For instructions, see [How to Create a Custom Virtual Machine] [].
	
[How to Log on to a Virtual Machine Running Linux]: ../virtual-machines-linux-how-to-log-on
[Manage Disks and Images]:http://go.microsoft.com/fwlink/p/?LinkId=397536
[How to Create a Custom Virtual Machine]: ../virtual-machines-create-custom/
[How to Attach a Data Disk to a Virtual Machine]: ../storage-windows-attach-disk/


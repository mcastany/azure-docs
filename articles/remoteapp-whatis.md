<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo" />

#What is Azure RemoteApp?

Azure RemoteApp brings the functionality of the on-premises Microsoft RemoteApp program, backed by Remote Desktop Services, to Azure. Azure RemoteApp lets you provide secure, remote access to applications from many different user devices.

When you move RemoteApp to Azure, you get to take advantage of the storage, scalability, and global reach of Azure without having to worry about a complex on-premises configuration. Microsoft provides maintenance of Azure, ensuring its reliability, freeing you up to focus on more important issues, like creating the best apps for your business to use. Another advantage of Azure RemoteApp is the accessibility - your users can access RemoteApp programs from Windows, iOS, Mac OS X, and Android devices. They can use your apps in the environment they prefer, while you use the Azure management portal to manage those apps. 

Read on for more information about RemoteApp, or, if we have already convinced you, [try it out now](http://azure.microsoft.com/en-us/services/remoteapp/).

Azure RemoteApp is part of the [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/en-us/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).


##RemoteApp deployment options
There are two kinds of RemoteApp deployment:


- A **cloud deployment** is hosted in and stores all data for programs in the Azure cloud. Users can access apps by logging in with their Microsoft account or corporate credentials synchronized or federated with Azure Active Directory.
- A **hybrid deployment** is hosted in and stores data in the Azure cloud but also lets users access data and resources stored on your local network. Users can access apps by logging in with their corporate credentials synchronized or federated with Azure Active Directory.

###Cloud deployment

The [cloud RemoteApp deployment](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-cloud-deployment/) offers a standalone way to host applications in the cloud. A cloud deployment exists only in the Azure cloud, as opposed to connecting to your local network.

As part of the RemoteApp preview, we provide you with the Office 2013 apps preinstalled and ready to share with your users. If you choose to leverage the available software, you can provision your service quickly.

An additional advantage of using the cloud deployment with the Office 2013 apps is that the apps and operating system (upon which your service is built) are always kept up to date through regular updates, and Microsoft Anti-Malware endpoint protection provides continuous defense. Your end users use their Microsoft accounts or corporate credentials to access the apps. All that you, the administrator, need to worry about is figuring out who should have access to which apps.

You can also create a cloud deployment to share a custom application or set of applications for your users. To do this, you need to [create a custom template image](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-custom-image/) (which is how we publish apps to RemoteApp) and simply choose that image (instead of the Office 2013 image) when you create your deployment. 

###Hybrid deployment
The [hybrid RemoteApp deployment](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-hybrid-deployment/) lets you provide both a custom set of applications to your users and access to the data and resources in your local network. Unlike a custom image used with the cloud deployment, the image you create for a hybrid deployment runs apps in a domain-joined environment, granting full access to your local network and data.

By integrating Active Directory with Azure Active Directory (using DirSync), your users can use their corporate credentials to access apps and data. When you use a work account in Active Directory, you can take your corporate policies into the cloud to control the apps you offer through RemoteApp.

As long as you build your template image on Windows Server 2012 R2 with the RD Session Host role service, there are few limits on the apps you can publish for your users. If the apps function properly in that template image environment, your end users can access them through RemoteApp. 

###Updating your deployment
One of the key differences between the hybrid and cloud deployments is how software updates are handled. With a cloud deployment that uses the preinstalled Office 2013 image, you do not have to worry about any updates. The service maintains itself and rolls out updates on an ongoing basis, to both apps and the operating system.

For hybrid deployments, as well as cloud deployments that use a custom template image, you are in charge of maintaining the image and apps. For domain-joined images, you can control updates by using tools such as Windows Update, Group Policy, or System Center.

After you update your custom template image, you upload the new image to the Azure cloud and then update the deployment to use the new image. (You can do this from the RemoteApp Quick Start page or the Dashboard.)

##Supported RemoteApp clients
As part of the Preview release of Azure RemoteApp, we released a new Microsoft RemoteApp client app for Windows and Windows RT, as well as updates to the Microsoft Remote Desktop apps for iOS and Android. Your users can use these apps on their mobile or compute devices to access the new RemoteApp programs.

##Next steps
Go! Try it out! These articles help get you started with RemoteApp:

- [How to create a custom template image for RemoteApp](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-custom-image/)
- [How to create a cloud deployment of RemoteApp](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-cloud-deployment/)
- [How to create a hybrid deployment of RemoteApp](http://azure.microsoft.com/en-us/documentation/articles/remoteapp-create-hybrid-deployment/)

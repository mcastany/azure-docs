<properties linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create websites - Azure service management" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="timamm" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

#How to Create a Website

This topic shows how to create a website from the gallery or by using the management portal.

For information about how to deploy your content to an Azure Website that you have created, see the **Deploy** section in [Azure Web Sites](/en-us/documentation/services/web-sites/).

## Table of Contents ##

- [How to: Create a Web Site Using the Management Portal](#createawebsiteportal)
- [How to: Create a Web Site from the Gallery](#howtocreatefromgallery)
- [How to: Delete a Web Site](#deleteawebsite)
- [Next Steps](#nextsteps)

##<a name="createawebsiteportal"></a>How to: Create a Website Using the Management Portal

Follow these steps to create a website in Azure.
	
1. Login to the [Azure Management Portal](http://manage.windowsazure.com/).

2. Click the **Create New** icon on the bottom left of the Management Portal.

3. Click the **Website** icon, click the **Quick Create** icon, enter a value for URL and then click the check mark next to **Create Website** on the bottom right corner of the page.

4. When the website has been created, you will see the text **Creating website <*website name*>  succeeded**. You can browse to the website by clicking **Browse** at the bottom of the portal page.

5. In the portal, click the name of the website displayed in the list of websites to open the website's **Quick Start** management page.

6. On the **Quick Start** page, you are provided with options to get website development tools, set up publishing for your website, or set up deployment from a source control provider like TFS or Git. FTP publishing is set up by default for websites and the FTP Host name is displayed in the **Quick Glance** section of the **Dashboard** page under **FTP Host Name**. Before publishing with FTP or Git, choose the option to **Reset deployment credentials** on the **Dashboard** page so that you can authenticate against the FTP Host or the Git Repository when deploying content to your website.

7. The **Configure** management page exposes settings for your website in the following categories:

 - **General**: Set the version of .NET framework or PHP required by your web application. For sites in Standard mode, the **Platform** option lets you choose either a 32-bit or 64-bit environment.

- **Certificates**: In Standard mode, you can upload SSL certificates for custom domains. 

- **Domain Names**: In Shared and Standard modes, you can view and manage custom domain names for your website.

- **SSL Bindings**: In Standard mode, you can assign SSL certificates to your custom domain names using either IP-based or SNI-based SSL.

 - **Deployments**: When you set up deployment from source control, you can configure your deployments here.

 - **Application Diagnostics**:  Set options for gathering diagnostic traces from a web application that has been instrumented with traces. 

- **Site Diagnostics**: Set logging options for gathering diagnostic information for your website. Options include Web Server Logging, Detailed Error Messages, and Failed Request Tracing.

- **Monitoring**: For websites in Standard mode, you can use this feature to test the availability of HTTP or HTTPS endpoints. 

- **App Settings**: Specify name/value pairs that will be loaded by your web application on start up. For .NET sites, these settings will be injected into your .NET configuration **AppSettings** at runtime, overriding existing settings. For PHP and Node sites these settings will be available as environment variables at runtime.

 - **Connection Strings**: View and edit connection strings. For .NET sites, these connection strings will be injected into your .NET configuration **connectionStrings** settings at runtime, overriding existing entries where the key equals the linked database name. For PHP and Node sites, these settings will be available as environment variables at runtime.

 - **Default Documents**: A website's default document is the page that is displayed by default when a user navigates to a website. Add your web application's default document to this list if it is not already present.  Your website's default document should be at the top of the list.

- **Handler Mappings**: Specify script processors that will handle requests for specific file extensions like *.php.

##<a name="howtocreatefromgallery"></a>How to: Create a Website from the Gallery

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>How to: Delete a Website
Websites are deleted using the **Delete** icon in the Azure Management Portal. The **Delete** icon is available in the Azure Portal when you click **Websites** to list all of your websites and at the bottom of each of the website management pages.

##<a name="nextsteps"></a>Next Steps

For more information, see [Azure Web Sites](/en-us/documentation/services/web-sites/).

<properties linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="juneb" title="How to install and configure Azure PowerShell" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juneb" />

# How to install and configure Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/en-us/manage/install-and-configure-cli/" title="Cross-Platform CLI">Cross-Platform CLI</a></div>

You can use Windows PowerShell to perform a variety of tasks in Azure, either interactively at a command prompt or automatically through scripts. Azure PowerShell is a module that provides cmdlets to manage Azure through Windows PowerShell. You can use the cmdlets to create, test, deploy, and manage solutions and services delivered through the Azure platform. In most cases, you can use the cmdlets to perform the same tasks that you can perform through the Azure Management Portal. For example, you can create and configure cloud services, virtual machines, virtual networks, and websites. 

The module is distributed as a downloadable file and the source code is managed through a publicly available repository. A link to the downloadable files is provided in the installation instructions later in this topic. For information about the source code, see [Azure PowerShell code repository](https://github.com/WindowsAzure/azure-sdk-tools).

This guide provides basic information about installing and setting up Azure PowerShell to manage the Azure platform.

## Table of contents
  
 * [Prerequisites for using Azure PowerShell](#Prereq)
 * [How to: Install Azure PowerShell](#Install)  
 * [How to: Connect to your subscription](#Connect)
 * [How to use the cmdlets: An example](#Ex)
 * [Getting Help](#Help)
 * [Additional Resources](#Resources)  


### <a id="Prereq"></a>Prerequisites for using Azure PowerShell

Azure is a subscription-based platform. This means that a subscription is required to use the platform. In most cases, it also means that the cmdlets require subscription information to perform the tasks with your subscription. (Some of the storage-related cmdlets can be used without this information.) You provide this by configuring your computer to connect to your subscription. Instructions are provided in this article, under "How to: Connect to your subscription."

> [WACOM.NOTE] Beginning in version 0.8.5, the Azure PowerShell modules require Microsoft .NET Framework 4.5.

When you install the module, the installer checks your system for the required software and installs all dependencies, such as the correct version of Windows PowerShell and .NET Framework.

<h2> <a id="Install"></a>How to: Install Azure PowerShell</h2>

You can download and install the Azure PowerShell modules by running the [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?LinkId=320376). When prompted, click **Run**. The Web Platform Installer installs the Azure PowerShell modules and all dependencies. Follow the prompts to complete the installation.

For more information about the command-line tools available for Azure, see [Command-line tools]( http://go.microsoft.com/fwlink/?LinkId=320796).

Installing the module also installs a customized console for Azure PowerShell. You can run the cmdlets from either the standard Windows PowerShell console or the Azure PowerShell console.

The method you use to open either console depends on the version of Windows you're running:

- On a computer running at least Windows 8 or Windows Server 2012, you can use the built-in Search. From the Start screen, begin typing **power**. This returns a scoped list of apps that includes Windows PowerShell and Azure PowerShell. To open the console, click either app. (To pin the app to the Start screen, right-click the icon.)

- On a computer running a version earlier than Windows 8 or Windows Server 2012, use the Start menu. From the Start menu, click **All Programs**, click **Azure**, and then click **Azure PowerShell**.

<h2><a id="Connect"></a>How to: Connect to your subscription</h2>

Use of Azure requires a subscription. If you don't have a subscription, see [Get Started with Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

The cmdlets need your subscription so they can manage your services. There are two ways to provide your subscription information to Windows PowerShell. You can use a management certificate that contains the information or you can sign in to Azure using your Microsoft account or an organizational account. When you sign in, Azure Active Directory (Azure AD) authenticates the credentials and returns an access token that lets Azure PowerShell manage your account. 

To help you choose the authentication method that's appropriate for your needs, consider the following:

- Azure AD is the recommended authentication method since it makes it easier to manage access to a subscription. With the update in version 0.8.6, it enables automation scenario with Azure AD authentication as well if Organizational account is used. It works with Azure Resource Manager API as well.
- When you use the certificate method, the subscription information is available as long as the subscription and the certificate are valid. However, this method makes it harder to manage access to a shared subscription, such as when more than one person is authorized to access the account. Also, Azure Resource Manager API doesn't accept certificate authentication.

For more information about authentication and subscription management in Azure, see [Manage Accounts, Subscriptions, and Administrative Roles](http://go.microsoft.com/fwlink/?LinkId=324796).

<h3>Use the Azure AD method</h3>

1. Open the Azure PowerShell console, as instructed in [How to: Install Azure PowerShell](#Install).

2. Type the following command:

    `Add-AzureAccount`

3. In the window, type the email address and password associated with your account.

4. Azure authenticates and saves the credential information, and then closes the window.

5. Staring from 0.8.6, if you have an organizational account, you can type the following command to bypass the pop up window. This will pop up the standard Windows PowerShell credential window for you to enter your organizational account user name and password.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6. If you are using this in an automation script and want to avoid any pop up window, use the following snippet

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

	> [WACOM.NOTE] This non-interactive login method only works with organizational account.  An organizational account is a user that is managed by your organization, and defined in your organizations Azure Active Directory tenant. If you do not currently have an organizational account, and are using a Microsoft account to log in to your Azure subscription, you can easily create one using the following steps.
	> 
	> 1. Login to the [Azure Management Portal](https://manage.windowsazure.com), and click on **Active Directory**.
	> 
	> 2. If no directory exists, select **Create your directory** and provide the requested information.
	> 
	> 3. Select your directory and add a new user. This new user is an organizational account.
	> 
	>     During the creation of the user, you will be supplied with both an e-mail address for the user and a temporary password. Save this  information as it is used in another step.
	> 
	> 4. From the management portal, select **Settings** and then select **Administrators**. Select **Add**, and add the new user as a co-administrator. This allows the organizational account to manage your Azure subscription.
	> 
	> 5. Finally, log out of the Azure portal and then log back in using the new organizational account. If this is the first time logging in with this account, you will be prompted to change the password.
	>
	>For more information on organizational account with Microsoft Azure, see [Sign up for Microsoft Azure as an Organization](http://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/).

<h3>Use the certificate method</h3>

The Azure module includes cmdlets that help you download and import the certificate.

- The **Get-AzurePublishSettingsFile** cmdlet opens a web page on the
Azure Management Portal, from which you can
download the subscription information. The information is contained in a .publishsettings file.

- The **Import-AzurePublishSettingsFile** imports the .publishsettings file for use by the module. This file includes a management certificate that has security credentials. 

<div class="dev-callout"> 
<b>Note</b>
<p>The cmdlets in the AzureResourceManager module require the Azure AD method (Add-AzureAccount). These cmdlets do not support publish settings files. For more information about the cmdlets in the AzureResourceManager module, see <a href="http://go.microsoft.com/fwlink/?LinkID=394765">Azure Resource Manager Cmdlets</a>.</p> 
</div>

<div class="dev-callout"> 
<b>Important</b> 
<p>We recommend that you delete the publishing profile that you
downloaded using <b>Get-AzurePublishSettingsFile</b> after you import those
settings. Because the management certificate includes security credentials, it
should not be accessed by unauthorized users. If you need information
about your subscriptions, you can get it from the <a href="http://manage.windowsazure.com/">Azure Management Portal</a> or the <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Microsoft Online Services Customer Portal</a>.</p> 
</div>

1. Sign in to the [Azure Management Portal](http://manage.windowsazure.com) using the credentials for your Azure account.

2. Open the Azure PowerShell console, as instructed in [How to: Install Azure PowerShell](#Install).

3. Type the following command:

    `Get-AzurePublishSettingsFile`

4. When prompted, download and save the publishing profile and note the path and name of
the .publishsettings file. This information is required when you run the
**Import-AzurePublishSettingsFile** cmdlet to import the settings. The default
location and file name format is:

	`C:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`

5. Type a command similar to the following, substituting your Windows account name and the path and file name for the placeholders:

    `Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`

> [WACOM.NOTE] If you are added to other subscriptions as a co-administrator after you import your publish settings, you'll need to repeat this
process to download a new .publishsettings file, and then import those
settings. For information about adding co-administrators to help manage
services for a subscription, see [Add and Remove Co-Administrators for Your Azure Subscriptions](http://msdn.microsoft.com/en-us/library/windowsazure/gg456328.aspx).

<h3> View account and subscription details</h3>
You can have multiple accounts and subscriptions available for use by Azure PowerShell. You can add multiple accounts by running Add-AzureAccount more than once. 

To get the available Azure accounts, type:

	Get-AzureAccount

To get your Azure subscriptions, type:

	Get-AzureSubscription

## <a id="Ex"></a>How to use the cmdlets: An example ##

After you've installed the module and configured your computer to connect to your subscription, you can create an Azure website. This example will get you started using the Azure cmdlets.

1. Start the Azure PowerShell console.

2. Choose a name for your website. Pick a name that conforms to DNS naming conventions. Valid names can contain only letters 'a' through 'z', numbers '0' through '9', and a hyphen ('-'). 

	The website name must be unique in Azure. We'll use "mySite" in this example, but be sure to choose a different name, such as your account name followed by a number.  

	After you pick a name, type a command similar to the following. Substitute your website name for "mySite".

	`New-AzureWebsite mySite`

	The cmdlet creates the website and returns an object that represents the new website. The object properties include useful information about the website.

3. To get information about the website, type this command. It returns a bit of information about all websites in the subscription, including the one that you just created.

	`Get-AzureWebsite`

4. To get more information about your website, include the website name in the command. Be sure to substitute the name of your website for "mySite".

	`Get-AzureWebsite -Name mySite`

5. Websites are started after they are created. To stop the website, type this command, including the name of your website.

	`Stop-AzureWebsite -Name mySite`

6. To verify that the site's state is 'stopped', run the Get-AzureWebsite command again.

	`Get-AzureWebsite`
  
7. To complete this test, delete the website. Type:  

	`Remove-AzureWebsite -Name mySite`

7. To complete the task, confirm that the website is deleted.

	`Get-AzureWebsite -Name mySite`

##<a id="Help"></a>Getting Help##

These resources provide help for specific cmdlets: 


-   From within the console, you can use the built-in Help system. The **Get-Help** cmdlet provides access to this system. The following table provides some examples of commands you can use to get Help. You can get more information from within the console by typing **help**.

    <table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Command</b></td>
		<td><b>Result</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help</td>
		<td>Describes how to use the Help system. <p><b>Note</b>: The description includes some information about Help files that does not apply to the Azure module. Specifically, Help files are installed when the module is installed. They are not available for download separately.</p>
</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help Azure</td>
		<td>Gets all cmdlets in the Azure module.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Get-Help &lt;<b>language</b>&gt;-dev</td>
		<td>Gets cmdlets for developing and managing applications in a specific language. For example, help node-dev, help php-dev, or help python-dev.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt;</td>
		<td>Gets help about a Windows PowerShell cmdlet. Replace <cmdlet> with the cmdlet name.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Parameter *</td>
		<td>Gets descriptions of the cmdlet parameters. The asterisk (*) means "all".</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Examples</td>
		<td>Gets the syntax and examples of using the cmdlet.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Full</td>
		<td>Gets all help for a cmdlet, including technical details.</td>
    </tr>
    </tbody>
    </table>



- Reference information about the cmdlets in the Azure PowerShell modules is also available in the Azure library. For information, see [Azure Cmdlet Reference](http://msdn.microsoft.com/en-us/library/windowsazure/jj554330.aspx).

For help from the community, try these popular forums:

- [Azure forum on MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212) 
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>Additional Resources ##

These are some of the resources available that you can use to learn to use Azure and Windows PowerShell. 

- To provide feedback about the cmdlets, report issues, or access the source code, see [Azure PowerShell code repository](https://github.com/WindowsAzure/azure-sdk-tools).

- To learn about the Windows PowerShell command line and scripting environment, see the [TechNet Script Center](http://go.microsoft.com/fwlink/p/?LinkId=320211).

- For information about installing, learning, using, and customizing Windows PowerShell, see [Scripting with Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210).

- For information about what scripts are and how to run them in Windows PowerShell, see [Running Scripts](http://go.microsoft.com/fwlink/p/?LinkId=320627). This article includes basic information about creating scripts and configuring your computer to run scripts. 

- For information about cmdlets for Azure AD, see [Manage Azure AD using Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320628).



  
  
  [Microsoft Online Services Customer Portal]: https://mocp.microsoftonline.com/site/default.aspx
 
  


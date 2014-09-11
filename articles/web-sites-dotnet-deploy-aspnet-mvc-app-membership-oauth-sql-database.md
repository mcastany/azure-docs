<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Website with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande" />



# Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website

***Updated 2 April 2014.***

This tutorial shows you how to build a secure ASP.NET MVC 5 web app that enables users to log in with credentials from Facebook or Google. You will also deploy the application to Azure.

You can open an Azure account for free, and if you don't already have Visual Studio 2013, the SDK automatically installs Visual Studio 2013 for Web Express. You can start developing for Azure for free. If you want to use Visual Studio 2012, see the [previous tutorial](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/). This version of the tutorial is far simpler than the previous version.

This tutorial assumes that you have no prior experience using Azure. On completing this tutorial, you'll have a secure data-driven web application up and running in the cloud and using a cloud database.

You'll learn:

* How to create a secure ASP.NET MVC 5 project and publish it to an Azure Website.
* How to use [OAuth](http://oauth.net/ "http://oauth.net/"), [OpenID](http://openid.net/) and the ASP.NET membership database to secure your application.
* How to use the new membership API to add users and roles.
* How to use a SQL database to store data in Azure.

You'll build a simple contact list web application that is built on ASP.NET MVC 5 and uses the ADO.NET Entity Framework for database access. The following illustration shows the login page for the completed application:

![login page][rxb]

>[WACOM.NOTE] To complete this tutorial, you need a Microsoft Azure account. If you don't have an account, you can <a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activate your MSDN subscriber benefits</a> or <a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">sign up for a free trial</a>


In this tutorial:

- [Set up the development environment](#setupdevenv)
- [Set up the Azure environment][setupwindowsazureenv]
- [Create an ASP.NET MVC 5 application][createapplication]
- [Deploy the application to Azure][deployapp1]
- [Add a database to the application][adddb]
- [Add an OAuth Provider][]
- [Using the Membership API][]
- [Deploy the app to Azure][deployapp11]
- [Next steps][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

To use the new SSL certificate for localhost, you will need to install [Visual Studio 2013 Update 2 RC](http://go.microsoft.com/fwlink/?LinkId=390521) or higher.

<h2><a name="bkmk_setupwindowsazure"></a>Set up the Azure environment</h2>

Next, set up the Azure environment by creating an Azure Website and a SQL database.

### Create a website and a SQL database in Azure

Your Azure Website will run in a shared hosting environment, which means it runs on virtual machines (VMs) that are shared with other Azure clients. A shared hosting environment is a low-cost way to get started in the cloud. Later, if your web traffic increases, the application can scale to meet the need by running on dedicated VMs. If you need a more complex architecture, you can migrate to an Azure Cloud Service. Cloud services run on dedicated VMs that you can configure according to your needs.

Azure SQL Database is a cloud-based relational database service that is built on SQL Server technologies. The tools and applications that work with SQL Server also work with SQL Database.

1. In the [Azure Management Portal](https://manage.windowsazure.com), click **Websites** in the left tab, and then click  **New**.

	![New button in Management Portal][rxWSnew]

1. Click **Website**, and then click **Custom Create**.

	![Create with Database link in Management Portal][rxCreateWSwithDB] 

	The **New Website - Custom Create** wizard opens. 

1. In the **Create Website** step of the wizard, enter a string in the **URL** box to use as the unique URL for your application. The complete URL will consist of what you enter here plus the suffix that you see next to the text box. The illustration shows a URL is that probably taken so you will have to choose a different one.

	![Create with Database link in Management Portal][rr1]

1. In the **Database** drop-down list, choose **Create a free SQL database**.

1. In the **Region** drop-down list, choose the same region you selected for the Website.
This setting specifies which data center your VM will run in. 
1. In the **DB Connection String Name** box, leave the default value of *DefaultConnection*.
1. Click the arrow that points to the right at the bottom of the box.
The wizard advances to the **Specify database settings** step.

1. In the **Name** box, enter *ContactDB*. (see the image below). 
1. In the **Server** box, select **New SQL Database server**. (see the image below). Alternatively, if you previously created a SQL Server database, you can select that SQL Server from the dropdown control.
1. Set the **Region** to the same area you created the Website.
1. Enter an administrator **Login Name** and **Password**. If you selected **New SQL Database server** you aren't entering an existing name and password here, you're entering a new name and password that you're defining now to use later when you access the database. If you selected a SQL Server you've created previously, you'll be prompted for the password to the previous SQL Server account name you created. For this tutorial, we won't check the **Advanced** box.  For a free DB, you can only set the collation.
1. Click the check mark at the bottom right of the box to indicate you're finished.

	![Database Settings step of New Website - Create with Database wizard][setup007]
	
	The following image shows using an existing SQL Server and Login.

	![Database Settings step of New Website - Create with Database wizard][rxPrevDB]

	The Management Portal returns to the Websites page, and the **Status** column shows that the site is being created. After a while (typically less than a minute), the **Status** column shows that the site was successfully created. In the navigation bar at the left, the number of sites you have in your account appears next to the **Websites** icon, and the number of databases appears next to the **SQL Databases** icon.

<h2><a name="bkmk_createmvc4app"></a>Create an ASP.NET MVC 5 application</h2>

You have created an Azure Website, but there is no content in it yet. Your next step is to create the Visual Studio web app that you'll publish to Azure.

### Create the project

2. From the **File** menu, click **New Project**.

   ![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

3. In the **New Project** dialog box, expand **C#** and select **Web** under **Installed Templates**, and then select **ASP.NET Web Application**.


4. Name the application **ContactManager** and click **OK**.

   ![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
   **Note:** The image shows "MyExample" as the name, but make sure you enter "ContactManager". Code blocks that you'll be copying later assume that the project name is ContactManager. 

5. In the **New ASP.NET Project** dialog box, select the **MVC** template, **uncheck** the **Create remote resources** check box and then click **OK**. (The check box might be labeled **Host in the cloud** instead of **Create remote resources**.)

   ![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)


### Set the page header and footer


1. In **Solution Explorer** open the *Layout.cshtml* file in the *Views\Shared* folder.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. Replace the two occurrences of "My ASP.NET MVC Application" with "Contact Manager".
1. Replace "Application name" with "CM Demo". 

2. Update the first Action link and replace *Home* with *Cm* to use the *Cm* Controller.

	![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Run the application locally

1. Press CTRL+F5 to run the application.

	The application home page appears in the default browser.

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

This is all you need to do for now to create the application that you'll deploy to Azure. Later you'll add database functionality.

<h2><a name="bkmk_deploytowindowsazure1"></a>Deploy the application to Azure</h2>

1. In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.

   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   The **Publish Web** wizard opens.

1. In the **Profile** tab of the **Publish Web** wizard, click **Azure Websites**.

   ![Import publish settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG)

1. Click the **Sign In** button and log into the Azure portal.

 ![sign in](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG)


   After logging in, the **Select Existing Website** dialog box appears.

1. Select the website you created in the first part of this tutorial, and then click **OK**.

 ![select web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png)


1. In the **Publish Web** dialog box, click **Publish**.

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	The application you created is now running in the cloud. The next time you deploy the application, only the changed (or new) files will be deployed.

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Add a database to the application</h2>

Next, you'll update the MVC application to add the ability to display and update contacts and store the data in a database. The application will use the Entity Framework to create the database and to read and update data in the database.

### Add data model classes for the contacts

You begin by creating a simple data model in code.

1. In **Solution Explorer**, right-click the Models folder, click **Add**, and then **Class**.

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. In the **Add New Item** dialog box, name the new class file *Contact.cs*, and then click **Add**.

	![Add New Item dialog box][adddb002]

3. Replace the contents of the Contacts.cs file with the following code.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
The **Contacts** class defines the data that you will store for each contact, plus a primary key, *ContactID*, that is needed by the database.

### Create web pages that enable app users to work with the contacts

The ASP.NET MVC scaffolding feature can automatically generate code that performs create, read, update, and delete (CRUD) actions.

<h2><a name="bkmk_addcontroller"></a>Add a Controller and a view for the data</h2>

1. Build the project **(Ctrl+Shift+B)**. (You must build the project before using the scaffolding mechanism.) 
1. In **Solution Explorer**, right-click the Controllers folder and click **Add**, and then click **Controller**.

	![Add Controller in Controllers folder context menu][addcode001]

5. In the **Add Scaffold** dialog box, select **MVC 5 Controller with views, using EF** and then click **Add**.
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. In the **Model class** dropdown box, select **Contact (ContactManager.Models)**. (See the image below.)
1. In the **Data context class**, select **ApplicationDbContext (ContactManager.Models)**. The **ApplicationDbContext** will be used for both the membership DB and our contact data.
1. In the **Controller name** text entry box, enter "CmController" for the controller name. 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Click **Add**.

   Visual Studio creates a controller methods and views for CRUD database operations for **Contact** objects.

## Enable Migrations, create the database, add sample data and a data initializer ##

The next task is to enable the [Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) feature in order to create the database based on the data model you created.

1. In the **Tools** menu, select **NuGet Package Manager** and then **Package Manager Console**.
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. In the **Package Manager Console** window, enter the following command:

		enable-migrations
	The **enable-migrations** command creates a *Migrations* folder, and it puts in that folder a *Configuration.cs* file that you can edit to seed the database and configure Migrations. 

2. In the **Package Manager Console** window, enter the following command:

		add-migration Initial


	The **add-migration Initial** command generates a file named **&lt;date_stamp&gt;Initial** in the *Migrations* folder that creates the database. The first parameter ( **Initial** ) is arbitrary and is used to create the name of the file. You can see the new class files in **Solution Explorer**.
	In the **Initial** class, the **Up** method creates the Contacts table, and the **Down** method (used when you want to return to the previous state) drops it.
3. Open the *Migrations\Configuration.cs* file. 
4. Add the following namespace. 

    	 using ContactManager.Models;



5. Replace the *Seed* method with the following code:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	This code initializes (seeds) the database with the contact information. For more information on seeding the database, see [Seeding and Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. In the **Package Manager Console** enter the command:

		update-database

	![Package Manager Console commands][addcode009]

	The **update-database** runs the first migration which creates the database. By default, the database is created as a SQL Server Express LocalDB database. 

7. Press CTRL+F5 to run the application, and then click the **CM Demo** link; or navigate to http://localhost:(port#)/Cm. 

	The application shows the seed data and provides edit, details and delete links. You can create, edit, delete and view data.

	![MVC view of data][rx2]

<h2><a name="addOauth"></a><span class="short-header">OAuth</span>Add an OAuth2 And OpenID Provider</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") is an open protocol that allows secure authorization in a simple and standard method from web, mobile, and desktop applications. The ASP.NET MVC internet template uses OAuth and [OpenID](http://openid.net/) to expose Facebook, Twitter, Google and Microsoft as authentication providers. Although this tutorial uses only Google as the authentication provider, you can easily modify the code to use any of the providers. The steps to implement other providers are very similar to the steps you will see in this tutorial. To use Facebook as an authentication provider, see my tutorial [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

In addition to authentication, the tutorial will also use roles to implement authorization. Only those users you add to the *canEdit* role will be able to change data (that is, create, edit, or delete contacts).

1. Follow the instructions in my tutorial [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) under **Creating a Google app for OAuth 2 to set up a Google app for OAuth2**.
2. Open the *App_Start\Startup.Auth.cs* file. Remove the comment characters from the *app.UseGoogleAuthentication()* method and enter the **clientId** and **clientSecret**.

<pre>
         public void ConfigureAuth(IAppBuilder app)
         {
            // Enable the application to use a cookie to store information for the signed in user
            app.UseCookieAuthentication(new CookieAuthenticationOptions
            {
               AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
               LoginPath = new PathString("/Account/Login")
            });
            // Use a cookie to temporarily store information about a user logging in with a third party login provider
            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

            <mark>app.UseGoogleAuthentication(
               clientId: "000-000.apps.googleusercontent.com",
               clientSecret: "00000000000");</mark>
         } 
</pre>

1. Run the application and click  the **Log In** link. 
1. Under **Use another service to log in**, click the **Google** button. 

	![Goog login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG)

1. Enter your  credentials.
1. The Google authentication server will ask you for permission for the app to view your email address and basic information about your account. Click **Accept**.
![GOOG asking for permission](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG)
1. You are redirected to the Register page. The user name defaults to the email alias you used to register, you can change it if you like. Click **Register**.

	![register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG)

<h2><a name="mbrDB"></a><span class="short-header">Membership DB</span>Using the Membership API</h2>
In this section you will add a local user and the *canEdit* role to the membership database. Only those users in the *canEdit* role will be able to edit data. A best practice is to name roles by the actions they can perform, so *canEdit* is preferred over a role called *admin*. When your application evolves you can add new roles such as *canDeleteMembers* rather than the less descriptive *superAdmin*.

1. Open the *migrations\configuration.cs* file and add the following `using` statements:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Add the following **AddUserAndRole** method to the class:

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. Call the new method from the **Seed** method:
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
	</pre>  
<span></span>
	The following images shows the changes to *Seed* method:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   This code creates a new role called *canEdit*, creates a new local user *user1@contoso.com*, and adds *user1@contoso.com* to the *canEdit* role. For more information, see the [ASP.NET Identity resource page](http://curah.microsoft.com/55636/aspnet-identity).

## Use Temporary Code to Add New Social Login Users to the canEdit Role  ##
In this section you will temporarily modify the **ExternalLoginConfirmation** method in the Account controller to add new users registering with an OAuth or OpenID provider to the *canEdit* role. We will temporarily modify the **ExternalLoginConfirmation** method to automatically add new users to an administrative role. Until we provide a tool to add and manage roles, we'll use the temporary automatic registration code below. We hope to provide a tool similar to [WSAT](http://msdn.microsoft.com/en-us/library/ms228053.aspx) in the future which allow you to create and edit user accounts and roles. Later in the tutorial I'll show how you can use **Server Explorer** to add users to roles.  

1. Open the **Controllers\AccountController.cs** file and navigate to the **ExternalLoginConfirmation** method.
1. Add the following call to **AddToRoleAsync** just before the **SignInAsync** call.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   The code above adds the newly registered user to the "canEdit" role, which gives them access to action methods that change (edit) data. An image of the code change is shown below:

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Later in the tutorial you will deploy the application to Azure, where you will log-on with Google or another third party authentication provider. This will add your newly registered account to the *canEdit* role. Anyone who finds your site's URL and has a Google ID can then register and update your database. To prevent other people from doing that, you can stop the site. You'll be able to verify who is in the *canEdit* role by examining the database.

In the **Package Manager Console** hit the up arrow key to bring up the following command:

		Update-Database

Run the  **Update-Database** command which will run the **Seed** method, and that will run the **AddUserAndRole** you just added. The **AddUserAndRole** will create the user *user1@contoso.com* and add her to the *canEdit* role.

## Protect the Application with SSL and the Authorize Attribute ##

In this section you will apply the [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx) attribute to restrict access to the action methods. Anonymous users will be able to view the **Index** action method of the home controller only. Registered users will be able to see contact data (The **Index** and **Details** pages of the Cm controller), the About, and the Contact pages. Only users in the *canEdit* role will be able to access action methods that change data.

1. Add the [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx) filter and the [RequireHttps](http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application. An alternative approach is to add the [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx) attribute and the [RequireHttps](http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) attribute to each controller, but it's considered a security best practice to apply them to the entire application. By adding them globally, every new controller and action method you add will automatically be protected, you won't need to remember to apply them. For more information see [Securing your ASP.NET MVC  App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Open the *App_Start\FilterConfig.cs* file and replace the *RegisterGlobalFilters* method with the following (which adds the two filters):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>
<span></span>

	The following image shows the changed code:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG)

	The [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx) filter applied in the code above will prevent anonymous users from accessing any methods in the application. You will use the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to opt out of the authorization requirement in a couple methods, so anonymous users can log in and can view the home page. The  [RequireHttps](http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) will require all access to the web app be through HTTPS.

1. Add the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to the **Index** method of the Home controller. The [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute enables you to white-list the methods you want to opt out of authorization. An image of a portion of the HomeController is shown below:	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Do a global search for *AllowAnonymous*, you can see it is used in the log in and registration methods of the Account controller.
1. In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. A portion of the completed code is shown below: 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## Enable SSL for the Project ##

1. Enable SSL. In Solution Explorer, click the **ContactManager** project, then click F4 to bring up the properties dialog. Change **SSL Enabled** to true. Copy the **SSL URL**. The SSL URL will be https://localhost:44300/ unless you've previously created SSL Websites.

	![enable SSL][rxSSL]
 
1. In Solution Explorer, right click the **Contact Manager** project and click **Properties**.
1. In the left tab, click **Web**.
1. Change the **Project Url** to use the **SSL URL** and save the page (Control S).

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Verify Internet Explorer is the browser Visual Studio launches as shown in the image below:

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	The browser selector lets you specify the browser Visual Studio launches.

 ![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	You can select multiple browsers and have Visual Studio update each browser when you make changes. For more information see [Using Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Press CTRL+F5 to run the application. Follow the instructions to trust the self-signed certificate that IIS Express has generated.

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Read the **Security Warning** dialog and then click **Yes** if you want to install the certificate representing  **localhost**.

 ![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE shows the *Home* page and there are no SSL warnings.

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome also accepts the certificate and will show HTTPS content without a warning. Firefox uses its own certificate store, so it will display a warning. For our application you can safely click **I Understand the Risks**. 

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)
 

1. If you are still logged in from a previous session, hit the **Log out** link.
1. Click on the **About** or **Contact** links. You will be redirected to the log in page because anonymous users cannot view those pages. 
1. Click the **Register as a new user** link and add a local user with email *joe@contoso.com*. Verify *Joe* can view the Home, About and Contact pages. 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Click the *CM Demo* link and verify you see the data. 
1. Click an edit link on the page, you will be redirected to the log in page (because a new local user is not added to the *canEdit* role).
1. Log in as *user1@contoso.com* with password of "P_assw0rd1" (the "0" in "word" is a zero). You will be redirected to the edit page you previously selected. 

   If you can't log in with that account and password, try copying the password from the source code and pasting it. If you still can't log in, check the **UserName** column of the **AspNetUsers** table to verify *user1@contoso.com* was added. 
1. Verify you can make data changes.

<h2><a name="bkmk_deploytowindowsazure11"></a>Deploy the app to Azure</h2>

1. In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.

	![Publish in project context menu][firsdeploy003]

	The **Publish Web** wizard opens.

1. Click the **Settings** tab on the left side of the **Publish Web** dialog box. Click the **v** icon to select the **Remote connection string** for **ApplicationDbContext** and select **ContactDB**.

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. Under **ContactManagerContext**, select **Execute Code First Migrations**.

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Click **Publish**.

1. Log in as *user1@contoso.com* and verify you can edit data.

1. Log out.

2. Log in using Google or Facebook. That will add the Google or Facebook account to the **canEdit** role.

### Stop the website to prevent other people from registering  

1. In **Server Explorer**, navigate to **Websites**.
4. Right click on each Website instance and select **Stop Website**. 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	Alternatively, from the Azure management portal, you can select the website, then click the **stop** icon at the bottom of the page.

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Remove AddToRoleAsync, Publish, and Test

1. Comment out or remove the following code from the **ExternalLoginConfirmation** method in the Account controller: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Build the project (which saves the file changes and verify you don't have any compile errors).
5. Right-click the project in **Solution Explorer** and select **Publish**.

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Click the **Start Preview** button. Only the files that need to be updated are deployed.
5. Start the website from Visual Studio or from the Portal. **You won't be able to publish while the website is stopped**.

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Go back to Visual Studio and click **Publish**.
3. Your Azure App opens up in your default browser. If you are logged in, log out so you can view the home page as an anonymous user.  
4. Click the **About** link. You'll be redirected to the Log in page.
5. Click the **Register** link on the Log in page and create local account. We will use this local account to verify you can access the read only pages but you cannot access pages that change data (which are protected by the *canEdit* role). Later on in the tutorial we will remove local account access. 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Verify you can navigate to the *About* and *Contact* pages.

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Click the **CM Demo** link to navigate to the **Cm** controller. Alternatively, you can append *Cm* to the URL. 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Click an Edit link. You will be redirected to the log in page. Under **Use another service to log in**, Click Google or Facebook and log in with the account you previously registered. (If you're working quickly and your session cookie has not timed out, you will be automatically logged in with the Google or Facebook account you previously used.)
2. Verify you can edit data while logged into that account.
 	**Note:** You cannot log out of Google from this app and log into a different google account with the same browser. If you are using one browser, you will have to navigate to Google and log out. You can log on with another account from the same third party authenticator (such as Google) by using a different browser.

If you have not filled out the first and last name of your Google account information, a NullReferenceException will occur.


## Examine the SQL Azure DB ##

1. In **Server Explorer**, navigate to the **ContactDB**
2. Right click on **ContactDB** and select **Open in SQL Server Object Explorer**.
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Note:** If you can't expand **SQL Databases** and *can't* see the **ContactDB** from Visual Studio, you will have to follow the instructions below to open a firewall port or a range of ports. Follow the instructions under **Set up Azure firewall rules**. You may have to wait for a few minutes to access the database after adding the firewall rule.
 
1. Right click on the **AspNetUsers** table and select **View Data**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Note the Id from the Google account you registered with to be in the **canEdit** role, and the Id of *user1@contoso.com*. These should be the only users in the **canEdit** role. (You'll verify that in the next step.)

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. In **SQL Server Object Explorer**, right click on **AspNetUserRoles** and select **View Data**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Verify the **UserId**s are from *user1@contoso.com* and the Google account you registered. 


## Set up Azure firewall rules ##

Follow the steps in this section if you can't connect to SQL Azure from Visual Studio or if you get an error dialog stating "Cannot open server".

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

You will need to add your IP address to the allowed IPs.

1. In the Azure Portal, Select **SQL Databases** in the left tab.

	![Select SQL][rx6]

1. Click on the **ContactDB**.

1. Click the **Set up Azure firewall rules for this IP address** link.

	![firewall rules][rx7]

1. When you are prompted with "The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?", click **Yes**. Adding this address is often not enough behind some corporate firewalls, you will need to add a range of IP addresses.

The next step is to add a range of allowed IP addresses.

1. In the Azure Portal, Click **SQL Databases**.
1. Select the **Servers** tab, and then click on the server you wish to configure.

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Click the **Configure** tab.

1. Add a rule name, starting and ending IP addresses.

	![ip range][rx9]

1. At the bottom of the page, click **Save**.
1. Please leave feedback and let me know if you needed to add a range of IP address to connect.

Finally, you can connect to the SQL Database instance from SQL Server Object Explorer (SSOX)

1. From the View menu, click **SQL Server Object Explorer**.
1. Right click **SQL Server** and select **Add SQL Server**.
1. In the **Connect to Server** dialog box, set the **Authentication** to **SQL Server Authentication**. You will get the **Server name** and **Login** from the Azure Portal.
1. In your browser, navigate to the portal and select **SQL Databases**.
1. Select the **ContactDB**, and then click **View SQL Database connection strings**.
1. From the **Connection Strings** page, copy the **Server**  and **User ID**.
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Past the **Server** and **User ID** values into the **Connect to Server** dialog in Visual Studio. The **User ID** value goes into the **Login** entry. Enter the password you used to create the SQL DB.

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

You can now navigate to the Contact DB using the instructions given earlier.


## To Add a User to the canEdit Role by editing database tables

Earlier in the tutorial you used code to add users to the canEdit role. An alternative method is to directly manipulate the data in the membership tables. The following steps show how to use this alternate method to add a user to a role.

2. In **SQL Server Object Explorer**, right click on **AspNetUserRoles** and select **View Data**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copy the *RoleId* and paste it into the empty (new) row.
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. In the **AspNetUsers** table find the user you want to put in the role, and copy the  user's *Id*, and then paste it into the **UserId** column of the **AspNetUserRoles** table.

We are working on a tool that will make managing users and roles much easier.

## Local Registration Considerations ##

The current  ASP.NET membership registration in the project does not provide support for password resets and it does not verify that a human is registering (for example with a [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership)). Once a user is authenticated using one of the third party providers, they can register. If you choose to disable local registration, follow these steps:


1. In the AccountController, remove the *[AllowAnonymous]* attribute from the GET and POST *Register* methods. This will prevent bots and anonymous users from registering.
1. In the *Views\Shared* folder, *_LoginPartial.cshtml* file, remove the Register action link.
2. In the *Views\Account\Login.cshtml* file, remove the Register action link.
2. Deploy the app.


<h2><a name="nextsteps"></a><span class="short-header">Next steps</span>Next steps</h2>

Follow my tutorial [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) for instructions on how to add profile data to the user registration DB and for detailed instructions on using Facebook as an authentication provider.

To enable the social login buttons shown at the top of this tutorial, see [Pretty social login buttons for ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

A good place to learn more about ASP.NET MVC is my [Getting Started with ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) tutorial. Tom Dykstra's excellent [Getting Started with EF and MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) will show you more advanced MVC and EF programming.

This tutorial and the sample application was written by [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) with assistance from Tom Dykstra and Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Please leave feedback on what you liked or what you would like to see improved, not only about the tutorial itself but also about the products that it demonstrates. Your feedback will help us prioritize improvements. You can also request and vote on new topics at [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]:#mbrDB
[Create a Data Deployment Script]:#ppd
[Update the Membership Database]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure Web Sites]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























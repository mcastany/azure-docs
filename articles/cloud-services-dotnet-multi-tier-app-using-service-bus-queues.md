<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Multi-Tier Application" pageTitle=".NET Multi-Tier Application - Azure Tutorial" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />





# .NET Multi-Tier Application Using Service Bus Queues

Developing for Azure is easy using Visual Studio 2013 and the
free Azure SDK for .NET. If you do not already have Visual
Studio 2013, the SDK will automatically install Visual Studio Express 2013, so you can start developing for Azure entirely for
free. This guide assumes you have no prior experience using Windows
Azure. On completing this guide, you will have an application that uses
multiple Azure resources running in your local environment and
demonstrating how a multi-tier application works.

You will learn:

-   How to enable your computer for Azure development with a
    single download and install.
-   How to use Visual Studio to develop for Azure.
-   How to create a multi-tier application in Azure using web
    and worker roles.
-   How to communicate between tiers using Service Bus Queues.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

In this tutorial you'll build and run the multi-tier application in an Azure Cloud Service. The front end will be an ASP.NET MVC web role and the back end will be a worker-role. You could create the same multi-tier application with the front end as a web project that would be deployed to an Azure Website instead of a cloud service. For instructions about what to do differently an Azure Website front end, see the [Next steps](#nextsteps) section.

A screenshot of the completed application is shown below:

![][0]

**Note** Azure also provides storage queue functionality. For more information about Azure storage queues and Service Bus queues, see [Azure Queues and Azure Service Bus Queues - Compared and Contrasted][sbqueuecomparison].  

<h2><span class="short-header">Inter-role communication</span>Scenario overview: inter-role communication</h2>

To submit an order for processing, the front end UI component, running
in the web role, needs to interact with the middle tier logic running in
the worker role. This example uses Service Bus brokered messaging for
the communication between the tiers.

Using brokered messaging between the web and middle tiers decouples the
two components. In contrast to direct messaging (that is, TCP or HTTP),
the web tier does not connect to the middle tier directly; instead it
pushes units of work, as messages, into the Service Bus, which reliably
retains them until the middle tier is ready to consume and process them.

The Service Bus provides two entities to support brokered messaging:
queues and topics. With queues, each message sent to the queue is
consumed by a single receiver. Topics support the publish/subscribe
pattern in which each published message is made available to each
subscription registered with the topic. Each subscription logically
maintains its own queue of messages. Subscriptions can also be
configured with filter rules that restrict the set of messages passed to
the subscription queue to those that match the filter. This example uses
Service Bus queues.

![][1]

This communication mechanism has several advantages over direct
messaging, namely:

-   **Temporal decoupling.** With the asynchronous messaging pattern,
    producers and consumers need not be online at the same time. Service
    Bus reliably stores messages until the consuming party is ready to
    receive them. This allows the components of the distributed
    application to be disconnected, either voluntarily, for example, for
    maintenance, or due to a component crash, without impacting the
    system as a whole. Furthermore, the consuming application may only
    need to come online during certain times of the day.

-   **Load leveling**. In many applications, system load varies over
    time whereas the processing time required for each unit of work is
    typically constant. Intermediating message producers and consumers
    with a queue means that the consuming application (the worker) only
    needs to be provisioned to accommodate average load rather than peak
    load. The depth of the queue will grow and contract as the incoming
    load varies. This directly saves money in terms of the amount of
    infrastructure required to service the application load.

-   **Load balancing.** As load increases, more worker processes can be
    added to read from the queue. Each message is processed by only one
    of the worker processes. Furthermore, this pull-based load balancing
    allows for optimum utilization of the worker machines even if the
    worker machines differ in terms of processing power as they will
    pull messages at their own maximum rate. This pattern is often
    termed the competing consumer pattern.

    ![][2]

The following sections discuss the code that implements this
architecture.

<h2><span class="short-header">Set up environment</span>Set up the development environment</h2>

Before you can begin developing your Azure application, you need
to get the tools and set-up your development environment.

1.  To install the Azure SDK for .NET, click the button below:

    [Get Tools and SDK][]

2. 	Click **install the SDK**.

3. 	Choose the link for the version of Visual Studio you are using. The steps in this tutorial use Visual Studio 2013:

	![][32]

4. 	When prompted to run or save the installation file, click
    **Run**:

    ![][3]

5.  In the Web Platform Installer, click **Install** and proceed with the installation:

    ![][33]

6.  Once the installation is complete, you will have everything
    necessary to start developing. The SDK includes tools that let you
    easily develop Azure applications in Visual Studio. If you
    do not have Visual Studio installed, it also installs the free
    Visual Studio Express for Web.

<h2><span class="short-header">Set up the namespace</span>Set up the Service Bus namespace</h2>

The next step is to create a service namespace, and to obtain a shared
secret key. A service namespace provides an application boundary for
each application exposed through Service Bus. A shared secret key is
automatically generated by the system when a service namespace is
created. The combination of service namespace and shared secret key
provides a credential for Service Bus to authenticate access to an
application.

Note that you can also manage namespaces and Service Bus messaging entities using the Visual Studio Server Explorer, but you can only create new namespaces from within the portal. 

###Set up the namespace using the Management Portal

1.  Log into the [Azure Management Portal][].

2.  In the left navigation pane of the Management Portal, click
    **Service Bus**.

3.  In the lower pane of the Management Portal, click **Create**.

    ![][6]

4.  In the **Add a new namespace** dialog, enter a namespace name.
    The system immediately checks to see if the name is available.   
    ![][7]

5.  After making sure the namespace name is available, choose the
    country or region in which your namespace should be hosted (make
    sure you use the same country/region in which you are deploying your
    compute resources).

    IMPORTANT: Pick the **same region** that you intend to choose for
    deploying your application. This will give you the best performance.

6.  Click the check mark. The system now creates your service
    namespace and enables it. You might have to wait several minutes as
    the system provisions resources for your account.

	![][27]

7.  In the main window, click the name of your service namespace.

	![][30]

8. Click **Connection Information**.

	![][31]

9.  In the **Access connection information** pane, find the **Default Issuer** and **Default Key** values.

10.  Make a note of the key, or copy it to the clipboard.

###Manage namespaces and messaging entities using the Visual Studio Server Explorer

To manage a namespace and obtain connection information using Visual Studio instead of the Management Portal, follow the procedure described [here](http://http://msdn.microsoft.com/en-us/library/windowsazure/ff687127.aspx), in the section titled **To connect to Azure from Visual Studio**. When you sign in to Azure, the **Service Bus** node under the **Microsoft Azure** tree in Server Explorer is automatically populated with any namespaces you've already created. Right-click any namespace, and then click **Properties** to see the connection string and other metadata associated with this namespace displayed in the Visual Studio **Properties** pane. 

Make a note of the **SharedAccessKey** value, or copy it to the clipboard:

![][34]

<h2><span class="short-header">Create a web role</span>Create a web role</h2>

In this section, you will build the front end of your application. You
will first create the various pages that your application displays.
After that, you will add the code for submitting items to a Service Bus
Queue and displaying status information about the queue.

### Create the project

1.  Using administrator privileges, start either Microsoft Visual Studio
    2013 or Microsoft Visual Studio Express. To start Visual
    Studio with administrator privileges, right-click **Microsoft Visual
    Studio 2013 (or Microsoft Visual Studio Express)** and
    then click **Run as administrator**. The Azure compute emulator,
    discussed later in this guide, requires that Visual Studio be
    launched with administrator privileges.

    In Visual Studio, on the **File** menu, click **New**, and then
    click **Project**.

    ![][8]


2.  From **Installed Templates**, under **Visual C#**, click **Cloud** and
    then click **Azure Cloud Service**. Name the project
    **MultiTierApp**. Then click **OK**.

    ![][9]

3.  From **.NET Framework 4.5** roles, double-click **ASP.NET Web
    Role**.

    ![][10]

4.  Hover over **WebRole1** under **Azure Cloud Service solution**, click
    the pencil icon, and rename the web role to **FrontendWebRole**. Then Click **OK**. (Make sure you enter "Frontend" with a lower-case "e", not "FrontEnd".)

    ![][11]

5.  From the **New ASP.NET Project** dialog, in the **Select a template** list, click **MVC**,
    then click **OK**.

    ![][12]

6.  In **Solution Explorer**, right-click **References**, then click
    **Manage NuGet Packages...** or **Add Library Package Reference**.

7.  Select **Online** on the left-hand side of the dialog. Search for
    "**WindowsAzure**" and select the **Azure Service
    Bus** item. Then complete the installation and close this dialog.

    ![][13]

8.  Note that the required client assemblies are now referenced and some
    new code files have been added.

9.  In **Solution Explorer**, right click **Models** and click **Add**,
    then click **Class**. In the Name box, type the name
    **OnlineOrder.cs**. Then click **Add**.

### Write the code for your web role

In this section, you will create the various pages that your application
displays.

1.  In the **OnlineOrder.cs** file in Visual Studio, replace the
    existing namespace definition with the following code:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  In **Solution Explorer**, double-click
    **Controllers\HomeController.cs**. Add the following **using**
    statements at the top of the file to include the namespaces for the
    model you just created, as well as Service Bus:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Also in the **HomeController.cs** file in Visual Studio, replace the
    existing namespace definition with the following code. This code
    contains methods for handling the submission of items to the queue:

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form 
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and 
				// cross-site request forgery  
    			[ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.
                    
                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  From the **Build** menu, click **Build Solution**.

5.  Now, you will create the view for the **Submit()** method you
    created above. Right-click within the Submit() method, and choose
    **Add View**

    ![][14]

6.  A dialog appears for creating the view. Select the
    **OnlineOrder** class in the **Model class** dropdown, and choose
    **Create** in the **Template** dropdown.

    ![][15]

7.  Click **Add**.

8.  Now, you will change the displayed name of your application. In the
    **Solution Explorer**, double-click the
    **Views\Shared\\_Layout.cshtml** file to open it in the Visual
    Studio editor.

9.  Replace all occurrences of **My ASP.NET MVC Application** with
    **LITWARE'S Awesome Products**.

10.	Replace **"your logo here"** with **LITWARE'S Awesome Products**:

	![][16]

11. Remove the **Home**, **About**, and **Contact** links. Delete the highlighted code:

	![][28]
  

12. Finally, modify the submission page to include some information about
    the queue. In **Solution Explorer**, double-click the
    **Views\Home\Submit.cshtml** file to open it in the Visual Studio
    editor. Add the following line after **&lt;h2>Submit&lt;/h2>**. For now,
    the **ViewBag.MessageCount** is empty. You will populate it later.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>
             

13. You now have implemented your UI. You can press **F5** to run your
    application and confirm that it looks as expected.

    ![][17]

### Write the code for submitting items to a Service Bus queue

Now, you will add code for submitting items to a queue. You will first
create a class that contains your Service Bus Queue connection
information. Then, you will initialize your connection from
**Global.aspx.cs**. Finally, you will update the submission code you
created earlier in **HomeController.cs** to actually submit items to a
Service Bus Queue.

1.  In Solution Explorer, right-click **FrontendWebRole** (right-click the project, not the role). Click **Add**, and then click **Class**.

2.  Name the class **QueueConnector.cs**. Click **Add** to create the class.

3.  You will now paste in code that encapsulates your connection
    information and contains methods for initializing the connection to
    a Service Bus Queue. In QueueConnector.cs, paste in the following code, and enter in
    values for **Namespace**, **IssuerName**, and **IssuerKey**. You can
    obtain these values either from the [Management Portal][Azure Management Portal], or from the Visual Studio Server Explorer under the **Service Bus** node.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to 
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  Now, you will ensure your **Initialize** method gets called. In **Solution Explorer**, double-click **Global.asax\Global.asax.cs**.

5.  Add the following line to the bottom of the **Application_Start**
    method:

        FrontendWebRole.QueueConnector.Initialize();

6.  Finally, you will update your web code you created earlier, to
    submit items to the queue. In **Solution Explorer**,
    double-click **Controllers\HomeController.cs** that you created
    earlier.

7.  Update the **Submit()** method as follows to get the message count
    for the queue:

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Update the **Submit(OnlineOrder order)** method as follows to submit
    order information to the queue:

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);
                
                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  You can now run your application again. Each time you submit an
    order, the message count increases.

    ![][18]

<h2><span class="short-header">Configuration manager</span>Cloud configuration manager</h2>

Azure supports a set of managed APIs that provides a consistent way to create new instances of Azure service clients (such as the Service Bus) across Microsoft cloud services. These APIs enable you to instantiate these clients (for example, **CloudBlobClient**, **QueueClient**, **TopicClient**) regardless of where the application is hosted -- on-premises, in a Microsoft cloud service, in websites, or in a persistent VM Role. You can also use these APIs to retrieve the configuration information necessary for instantiating these clients, and to change the configuration without having to redeploy the calling application. The APIs are located in the [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] class. There are also APIs on the client side.

### Connection string

To instantiate a client (for example, a Service Bus **QueueClient**), you can represent the configuration information as a connection string. On the client side, there is a **CreateFromConnectionString()** method that instantiates that client type by using that connection string. For example, given the following configuration section:

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
	</ConfigurationSettings>

The following code retrieves the connection string, creates a queue, and initializes the connection to the queue:

	QueueClient Client; 

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

The code in the following section uses these configuration management APIs.

<h2><span class="short-header">Create worker role</span>Create the worker role</h2>

You will now create the worker role that processes the order
submissions. This example uses the **Worker Role with Service Bus Queue** Visual Studio project template. First, you will use Server Explorer in Visual Studio to obtain the required credentials.

1. If you've already connected Visual Studio to your Azure account, as described in the section **Set up the namespace using the Visual Studio Server Explorer,** skip ahead to step 5. 

3. From the menu bar in Visual Studio, choose **View**, and then click **Server Explorer**. A **Service Bus** node appears under **Azure** within the Server Explorer hierarchy, as in the following figure.

	![][21]

2. In Server Explorer, expand **Azure**, then right-click **Service Bus**, then click **Add New Connection**.

3. In the **Add Connection** dialog, type the name of the service namespace, the issuer name, and the issuer key. Then click **OK** to connect.

	![][22]

4.  In Visual Studio, in **Solution Explorer** right-click the
    **Roles** folder under the **MultiTierApp** project.

5.  Click **Add**, and then click **New Worker Role Project**. The **Add New Role Project** dialog appears.

	![][26]

6.  In the **Add New Role Project dialog**, click **Worker Role with Service Bus Queue**, as in the following figure:

	![][23]

7.  In the **Name** box, name the project **OrderProcessingRole**. Then click **Add**.

8.  In Server Explorer, right-click the name of your service namespace, then click **Properties**. In the Visual Studio **Properties** pane, the first entry contains a connection string that is populated with the service namespace endpoint containing the required authorization credentials. For example, see the following figure. Double-click **ConnectionString**, and then press **Ctrl+C** to copy this string to the clipboard.

	![][24]

9.  In Solution Explorer, right-click the **OrderProcessingRole** you created in step 7 (make sure that you right-click **OrderProcessingRole** under **Roles**, and not the class). Then click **Properties**.

10.  In the **Settings** tab of the **Properties** dialog, click inside the **Value** box for **Microsoft.ServiceBus.ConnectionString**, and then paste the endpoint value you copied in step 8.

	![][25]

11.  Create an **OnlineOrder** class to represent the orders as you process them from the queue. You can reuse a class you have already created. In Solution Explorer, right-click the **OrderProcessingRole** project (right-click the project, not the role). Click **Add**, then click **Existing Item**.

12. Browse to the subfolder for **FrontendWebRole\Models**, and double-click **OnlineOrder.cs** to add it to this project.

13. In WorkerRole.cs, replace the value of the **QueueName** variable in **WorkerRole.cs** from `"ProcessingQueue"` to `"OrdersQueue"` as in the following code:

		// The name of your queue
		const string QueueName = "OrdersQueue";

14. Add the following using statement at the top of the WorkerRole.cs file:

		using FrontendWebRole.Models;

15. In the `Run()` function, inside the `OnMessage` call, add the following code inside the `try` clause:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  You have completed the application. You can test the full
    application as you did earlier, by pressing F5. Note that the message count does not increment, because the worker role processes items from the queue and marks them as complete. You can see the trace output of your
    worker role by viewing the Azure Compute Emulator UI. You
    can do this by right-clicking the emulator icon in the notification
    area of your taskbar and selecting **Show Compute Emulator UI**.

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a><span class="short-header">Next steps</span>Next steps</h2>  

To learn more about Service Bus, see the following resources:  
  
* [Azure Service Bus][sbmsdn]  
* [Service Bus How To's][sbwacom]  
* [How to Use Service Bus Queues][sbwacomqhowto]  

To learn more about multi-tier scenarios, or to learn how to deploy an application to a cloud service, see:  

* [.NET Multi-Tier Application Using Storage Tables, Queues, and Blobs][mutitierstorage]  

You might want to implement the front-end of a multi-tier application in an Azure Website instead of an Azure Cloud Service. To learn more about the difference between websites and cloud services, see [Azure Execution Models][executionmodels].

To implement the application you create in this tutorial as a standard web project instead of as a cloud service web role, follow the steps in this tutorial with the following differences:

1. When you create the project, choose the **ASP.NET MVC 4 Web Application** project template in the **Web** category instead of the **Cloud Service** template in the **Cloud** category. Then follow the same directions for creating the MVC application, until you get to the **Cloud configuration manager** section.

2. When you create the worker role, create it in a new, separate solution, similar to the original instructions for the web role. Now however, you're creating just the worker role in the cloud service project. Then follow the same directions for creating the worker role.

3. You can test the front-end and back-end separately, or you can run both simultaneously in separate Visual Studio instances.

To learn how to deploy the front end to an Azure Website, see [Deploying an ASP.NET Web Application to an Azure Website](http://www.windowsazure.com/en-us/develop/net/tutorials/get-started/). To learn how to deploy the back end to an Azure Cloud Service, see [.NET Multi-Tier Application Using Storage Tables, Queues, and Blobs][mutitierstorage].


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Azure Management Portal]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [sbmsdn]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732537.aspx  
  [sbwacom]: /en-us/documentation/services/service-bus/  
  [sbwacomqhowto]: /en-us/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://www.windowsazure.com/en-us/develop/net/fundamentals/compute/

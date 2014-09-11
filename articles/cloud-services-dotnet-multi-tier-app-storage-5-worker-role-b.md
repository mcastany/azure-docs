<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Building worker role B (email sender) for the Azure Email Service application - 5 of 5. 

This is the fifth tutorial in a series of five that show how to build and deploy the Azure Email Service sample application.  For information about the application and the tutorial series, see the [first tutorial in the series][firsttutorial].

In this tutorial you'll learn:

* How to add a worker role to a cloud service project.
* How to poll a queue and process work items from the queue.
* How to send emails by using SendGrid.
* How to handle planned shut-downs by overriding the `OnStop` method.
* How to handle unplanned shut-downs by making sure that no duplicate emails are sent.
 
## Segments of this tutorial

- [Add worker role B project to the solution](#addworkerrole)
- [Add a reference to the web project](#addreference)
- [Add the SendGrid NuGet package to the project](#addsendgrid)
- [Add project settings](#addsettings)
- [Add code that runs when the worker role starts](#addcode)
- [Test worker role B](#testing)
- [Next steps](#nextsteps)

<h2><a name="addworkerrole"></a><span class="short-header">Add worker role B</span>Add worker role B project to the solution</h2>

1. In Solution Explorer, right-click the cloud service project, and choose **New Worker Role Project**.

	![New worker role project menu][mtas-new-worker-role-project]

3. In the **Add New Role Project** dialog box, select **C#**, select **Worker Role**, name the project WorkerRoleB, and click **Add**. 

	![New role project dialog box][mtas-add-new-role-project-dialog]

<h2><a name="addreference"></a>Add a reference to the web project</h2>

You need a reference to the web project because that is where the entity classes are defined. You'll use the entity classes in worker role B to read and write data in the Azure tables that the application uses.

4. Right-click the WorkerRoleB project, and choose **Add - Reference**.

4. In **Reference Manager**, add a reference to the MvcWebRole project.

	![Add reference to MvcWebRole][mtas-worker-b-reference-manager]




<h2><a name="addsendgrid"></a>Add the SendGrid NuGet package to the project</h2>

To send email by using SendGrid, you need to install the SendGrid NuGet package.

1. In **Solution Explorer**, right-click the WorkerRoleB project and choose **Manage NuGet Packages**.

	![Manage NuGet Packages][mtas-worker-b-manage-nuget]

2. In the **Manage NuGet Packages** dialog box, select the **Online** tab, enter "sendgrid" in the search box, and press Enter.

3. Click **Install** on the **SendGrid** package.

	![Install the Sendgrid package][mtas-worker-b-install-sendgrid]

4. Close the dialog box.








<h2><a name="addsettings"></a>Add project settings</h2>

Like worker role A, worker role B needs storage account credentials to work with tables, queues, and blobs. In addition, in order to send email, the worker role needs to have credentials to embed in calls to the SendGrid service. And in order to construct an unsubscribe link to include in emails that it sends, the worker role needs to know the URL of the application. These values are stored in project settings.

For storage account credentials, the procedure is the same as what you saw in [the third tutorial][tut3configstorage].

1. In **Solution Explorer**, under **Roles** in the cloud project, right-click **WorkerRoleB** and choose **Properties**.
 
2. Select the **Settings** tab.

2. Make sure that **All Configurations** is selected in the **Service Configuration** drop-down list.

2. Select the **Settings** tab and then click **Add Setting**.

3. Enter "StorageConnectionString" in the **Name** column.

4. Select **Connection String** in the **Type** drop-down list.  

6. Click the ellipsis (**...**) button at the right end of the line to open the **Storage Account Connection String** dialog box.

7. In the **Create Storage Connection String** dialog, click the **Azure storage emulator** radio button, and then click **OK**.

Next, you create and configure the three new settings that are only used by worker role B.

3. In the **Settings** tab of the **Properties** window, Click **Add Setting**, and then add three new settings of type **String**:

	* **Name**: SendGridUserName, **Value**: the SendGrid user name that you established in [the second tutorial][tut2].

	* **Name**: SendGridPassword, **Value**: the SendGrid password.

	* **Name**: AzureMailServiceURL, **Value**: the base URL that the application will have when you deploy it, for example:  http://sampleurl.cloudapp.net.

	![New settings in WorkerRoleB project][mtas-worker-b-settings]




<h2><a name="addcode"></a>Add code that runs when the worker role starts</h2>

4. In the WorkerRoleB project, delete WorkerRole.cs.

5. In the WorkerRoleB project, add the WorkerRoleB.cs file from the downloaded project.

4. Build the solution. 
 
	If you get a build error, you might have ended up with multiple versions of the Azure Storage NuGet package. In that case, to resolve the problem, go into the NuGet Package Manager for the solution, select Installed packages, and scroll down to see if there are two instances of the Azure Storage package. Select the entry for Azure Storage 4.0.0, and delete it from the projects it's installed in. Then select the entry for Azure Storage 3.0.x and install it in the projects it's missing from. Close and restart Visual Studio, and then build the solution again.

5. Make sure that the cloud service project is still the startup project for the solution.

### The Onstart method

As you already saw in worker role A, the `OnStart` method initializes the context classes that you need in order to work with Azure storage entities. It also makes sure that all of the tables, queues, and blob containers you need in the `Run` method exist.  

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

The difference compared to worker role A is the addition of the blob container and the subscribe queue among the resources to create if they don't already exist. You'll use the blob container to get the files that contain the HTML and plain text for the email body. The subscribe queue is used for sending subscription confirmation emails.

### The Run method

The `Run` method processes work items from two queues: the queue used for messages sent to email lists (work items created by worker role A), and the queue used for subscription confirmation emails (work items created by the subscribe API method in the MvcWebRole project).

       
        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

This code runs in an infinite loop until the worker role is shut down. If a work item is found in the main queue, the code processes it and then checks the subscribe queue. 

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

If nothing is waiting in either queue, the code sleeps 60 seconds before continuing with the loop. 

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

The purpose of the sleep time is to minimize Azure Storage transaction costs, as explained in [the previous tutorial][tut4]. 

When a queue item is pulled from the queue by the [GetMessage][]  method, that queue item becomes invisible for 30 seconds to all other worker and web roles accessing the queue. This is what ensures that only one worker role instance will pick up any given queue message for processing. You can explicitly set this *exclusive lease* time (the time the queue item is invisible) by passing a  [visibility timeout](http://msdn.microsoft.com/en-us/library/windowsazure/ee758454.aspx) parameter to the  `GetMessage` method. If the worker role could take more than 30 seconds to process a queue message, you should increase the exclusive lease time to prevent other role instances from processing the same message. 

On the other hand, you don't want to set the exclusive lease time to an excessively large value. For example, if the exclusive lease time is set to 48 hours and your worker role unexpectedly shuts down after dequeuing a message, another worker role would not be able to process the message for 48 hours. The exclusive lease maximum is 7 days.

The  [GetMessages](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx)  method (notice the "s" at the end of the name) can be used to pull up to 32 messages from the queue in one call. Each queue access incurs a small transaction cost, and the transaction cost is the same whether 32 messages are returned or zero messages are returned. The following code fetches up to 32 messages in one call and then processes them.

    	foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
	    {
	        ProcessQueueMessage(msg);
	        messageFound = true;
	    }

When using `GetMessages` to remove multiple messages, be sure the visibility timeout gives your application enough time to process all the messages. Once the visibility timeout expires, other role instances can access the message, and once they do, the first instance will not be able to delete the message when it finishes processing the work item.

### The ProcessQueueMessage method

The `Run` method calls `ProcessQueueMessage` when it finds a work item in the main queue:
       
        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

Poison messages are those that cause the application to throw an exception when they are processed.  If a message has been pulled from the queue more than five times, we assume that it cannot be processed and remove it from the queue so that we don't keep trying to process it. Production applications should consider moving the poison message to a "dead message" queue for analysis rather than deleting the message.

The code parses the queue message into the partition key and row key needed to retrieve the SendEmail row, and a restart flag.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

If processing for this message has been restarted after an unexpected shut down, the code checks the `messagearchive` table to determine if this email has already been sent. If it has already been sent, the code deletes the `SendEmail` row if it exists and deletes the queue message.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

Next, we get the `SendEmail` row from the `message` table. This row has all of the information needed to send the email, except for the blobs that contain the HTML and plain text body of the email.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

Then the code sends the email and archives the `SendEmail` row.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

Moving the row to the messagearchive table can't be done in a transaction because it affects multiple tables.

Finally, if everything else is successful, the queue message is deleted.

            sendEmailQueue.DeleteMessage(msg);

### The SendEmailToList method

The actual work of sending the email by using SendGrid is done by the `SendEmailToList` method. If you want to use a different service than SendGrid, all you have to do is change the code in this method.

**Note:** If you have invalid credentials in the project settings, the call to SendGrid will fail but the application will not get any indication of the failure.  If you use SendGrid in a production application, consider setting up separate credentials for the web API in order to avoid causing silent failures when an administrator changes his or her SendGrid user account password. For more information, see [SendGrid MultiAuth - Multiple Account Credentials](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials). You can set up credentials at [https://sendgrid.com/credentials](https://sendgrid.com/credentials). 

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

In the `GetBlobText` method, the code gets the blob size and then uses that value to initialize the `MemoryStream` object for performance reasons. If you don't provide the size, what the `MemoryStream` does is allocate 256 bytes, then when the download exceeds that, it allocates 512 more bytes, and so on, doubling the amount allocated each time. For a large blob this process would be inefficient compared to allocating the correct amount at the start of the download.

### The ProcessSubscribeQueueMessage method

The `Run` method calls `ProcessSubscribeQueueMessage` when it finds a work item in the subscribe queue:
 
        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

This method performs the following tasks:

* If the message is a "poison" message, logs and deletes it.
* Gets the subscriber GUID from the queue message.
* Uses the GUID to get subscriber information from the MailingList table.
* Sends a confirmation email to the new subscriber.
* Deletes the queue message.

As with emails sent to lists, the actual sending of the email is in a separate method, making it easy for you to change to a different email service if you want to do that.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }




<h2><a name="testing"></a>Test Worker Role B</h2>

1. Run the application by pressing F5.

2. Go to the **Messages** page to see the message you created to test worker role A. After a minute or so, refresh the web page and you will see that the row has disappeared from the list because it has been archived.

4. Check the email inbox where you expect to get the email. Note that there might be delays in the sending of emails by SendGrid or delivery to your email client, so you might have to wait a while to see the email. You might need to check your junk mail folder also.




<h2><a name="nextsteps"></a>Next steps</h2>

You have now built the Azure Email Service application from scratch, and what you have is the same as the completed project that you downloaded.  To deploy to the cloud, test in the cloud, and promote to production, you can use the same procedures that you saw in [the second tutorial][tut2].

For a sample application that shows how to use LINQ in Azure Storage table service queries, see [PhluffyFotos](http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31).

To learn more about Azure storage, see the following resource:

* [Essential Knowledge for Windows Azure Storage](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx) (Bruno Terkaly's blog)

To learn more about the Azure Table service, see the following resources:

* [Essential Knowledge for Azure Table Storage](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx) (Bruno Terkaly's blog)
* [How to get the most out of Windows Azure Tables](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (Azure Storage team blog)
* [How to use the Table Storage Service in .NET](http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/) 
* [Windows Azure Storage Client Library 2.0 Tables Deep Dive](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (Azure Storage team blog)
* [Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage](http://msdn.microsoft.com/en-us/library/windowsazure/hh508997.aspx)

To learn more about the Azure Queue service and Azure Service Bus queues, see the following resources:

* [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
* [Azure Queues and Azure Service Bus Queues - Compared and Contrasted][sbqueuecomparison]
* [How to use the Queue Storage Service in .NET][queuehowto]

To learn more about the Azure Blob service, see the following resources:

* [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
* [How to use the Azure Blob Storage Service in .NET][blobhowto]

To learn more about autoscaling Azure Cloud Service roles, see the following resources:

* [How to Use the Autoscaling Application Block][autoscalingappblock]
* [Autoscaling and Azure][autoscaling-and-windows-azure]
* [Building Elastic, Autoscalable Solutions with Azure](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04) (MSDN channel 9 video)


<h2><a name="Acknowledgments"></a><span class="short-header">Acknowledgments</span>Acknowledgments</h2>

These tutorials and the sample application were written by [Rick Anderson](http://blogs.msdn.com/b/rickandy/) and Tom Dykstra. We would like to thank the following people for their assistance:

* Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 
* [Cory Fowler](http://blog.syntaxc4.net/) (Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) ) 
* [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
* Don Glover 
* Jai Haridas
* [Scott Hunter](http://blogs.msdn.com/b/scothu/) (Twitter: [@coolcsh](http://twitter.com/coolcsh))
* [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
* [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
* The members of the Developer Advisory Council who provided feedback:
   * Damir Arh
   * Jean-Luc Boucho
   * Carlos dos Santos
   * Mike Douglas
   * Robert Fite
   * Gianni Rosa Gallina
   * Fabien Lavocat
   * Karl Ots
   * Carlos-Alejandro Perez
   * Sunao Tomita
   * Perez Jones Tsisah
   * Michiel van Otegem

 







[firsttutorial]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
[tut2]: /en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[tut3configstorage]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
[tut4]: /en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
[queuehowto]: /en-us/develop/net/how-to-guides/queue-service/

[blobhowto]: /en-us/develop/net/how-to-guides/blob-storage/
[GetMessage]: http://msdn.microsoft.com/en-us/library/windowsazure/ee741827.aspx
[getstartedtutorial]: /en-us/develop/net/tutorials/get-started
[sbqueuecomparison]: http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx
[autoscalingappblock]: /en-us/develop/net/how-to-guides/autoscaling/


[mtas-new-worker-role-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
[mtas-add-new-role-project-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
[mtas-worker-b-add-existing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png
[mtas-worker-b-add-reference-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png
[mtas-worker-b-reference-manager]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
[mtas-worker-b-manage-nuget]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
[mtas-worker-b-install-sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png


[mtas-worker-b-settings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
[autoscaling-and-windows-azure]: http://msdn.microsoft.com/en-us/library/hh680945(v=PandP.50).aspx

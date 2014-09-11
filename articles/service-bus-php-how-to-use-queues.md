<properties linkid="develop-php-how-to-guides-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (PHP) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# How to Use Service Bus Queues

This guide will show you how to use Service Bus queues with PHP. The samples are
written in PHP and use the [Azure SDK for PHP][download-sdk]. The
scenarios covered include **creating queues**, **sending and receiving
messages**, and **deleting queues**.

## Table of Contents

-   [What are Service Bus queues?](#what-are-service-bus-queues)
-   [Create a service namespace](#create-a-service-namespace)
-   [Obtain the default management credentials for the namespace](#obtain-default-credentials)
- 	[Create a PHP application](#CreateApplication)
-	[Get the Azure Client Libraries](#GetClientLibrary)
-   [Configure your application to use Service Bus](#ConfigureApp)
-   [How to: Create a queue](#CreateQueue)
-   [How to: Send messages to a queue](#SendMessages)
-   [How to: Receive messages from a queue](#ReceiveMessages)
-   [How to: Handle application crashes and unreadable messages](#HandleCrashes)
-   [Next steps](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##<a id="CreateApplication"></a>Create a PHP application

The only requirement for creating a PHP application that accesses the Azure Blob service is the referencing of classes in the [Azure SDK for PHP][download-sdk] from within your code. You can use any development tools to create your application, including Notepad.

> [WACOM.NOTE]
> Your PHP installation must also have the <a href="http://php.net/openssl">OpenSSL extension</a> installed and enabled.

In this guide, you will use service features which can be called within a PHP application locally, or in code running within an Azure web role, worker role, or website.

##<a id="GetClientLibrary"></a>Get the Azure Client Libraries

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Configure your application to use Service Bus

To use the Azure Servise Bus queue APIs, you need to:

1. Reference the autoloader file using the [require_once][require_once] statement, and
2. Reference any classes you might use.

The following example shows how to include the autoloader file and reference the **ServicesBuilder** class.

> [WACOM.NOTE] 
> This example (and other examples in this article) assume you have installed the PHP Client Libraries for Azure via Composer. If you installed the libraries manually or as a PEAR package, you will need to reference the <code>WindowsAzure.php</code> autoloader file.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In the examples below, the `require_once` statement will be shown always, but only the classes necessary for the example to execute will be referenced.

##<a id="ConnectionString"></a>Setup an Azure Service Bus connection

To instantiate an Azure Service Bus client you must first have a valid connection string following this format:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Where the Endpoint is typically of the format `https://[yourNamespace].servicebus.windows.net`.

To create any Azure service client you need to use the **ServicesBuilder** class. You can:

* pass the connection string directly to it or
* use the **CloudConfigurationManager (CCM)** to check multiple external sources for the connection string:
	* by default it comes with support for one external source - environmental variables
	* you can add new sources by extending the **ConnectionStringSource** class

For the examples outlined here, the connection string will be passed directly.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


##<a id="CreateQueue"></a>How to: Create a queue

Management operations for Service Bus queues can be performed via the
**ServiceBusRestProxy** class. A **ServiceBusRestProxy** object is
constructed via the **ServicesBuilder::createServiceBusService** factory method with an appropriate connection string that encapsulates the token permissions to manage it.

The example below shows how to instantiate a **ServiceBusRestProxy** and call **ServiceBusRestProxy->createQueue** to create a queue named `myqueue` within a `MySBNamespace` service namespace:

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [WACOM.NOTE]
> You can use the <b>listQueues</b> method on <b>ServiceBusRestProxy</b> objects to check if a queue with a specified name already exists within a service namespace.

##<a id="SendMessages"></a>How to: Send messages to a queue

To send a message to a Service Bus queue, your application will call the **ServiceBusRestProxy->sendQueueMessage** method. The code below demonstrates how to send a message to the `myqueue` queue we created above within the
`MySBNamespace` service namespace.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Messages sent to (and received from ) Service Bus queues are instances
of the **BrokeredMessage** class. **BrokeredMessage** objects have a set
of standard methods (such as **getLabel**, **getTimeToLive**,
**setLabel**, and **setTimeToLive**) and properties that are used to hold
custom application-specific properties, and a body of arbitrary
application data.

Service Bus queues support a maximum message size of 256 KB (the header,
which includes the standard and custom application properties, can have
a maximum size of 64 KB). There is no limit on the number of messages
held in a queue but there is a cap on the total size of the messages
held by a queue. This upper limit on queue size is 5 GB.

##<a id="ReceiveMessages"></a>How to: Receive messages from a queue

The primary way to receive messages from a queue is to use a **ServiceBusRestProxy->receiveQueueMessage** method. Messages can be received in two different modes: **ReceiveAndDelete** (the default) and **PeekLock**.

When using the **ReceiveAndDelete** mode, receive is a single-shot operation - that is, when Service Bus receives a read request for a message in a queue, it marks the message as being consumed and returns it to the application. **ReceiveAndDelete** mode is the simplest model and works best for scenarios in which an
application can tolerate not processing a message in the event of a failure. To understand this, consider a scenario in which the consumer issues the receive request and then crashes before processing it. Because Service Bus will have marked the message as being consumed, then when the application restarts and begins consuming messages again, it will have missed the message that was consumed prior to the crash.

In **PeekLock** mode, receiving a message becomes a two stage operation, which makes it possible to support applications that cannot tolerate missing messages. When Service Bus receives a request, it finds the next message to be consumed, locks it to prevent other consumers from receiving it, and then returns it to the application. After the application finishes processing the message (or stores it reliably for future processing), it completes the second stage of the receive process by passing the received message to **ServiceBusRestProxy->deleteMessage**. When Service Bus sees the **deleteMessage** call, it will mark the message as being consumed and remove it from the queue.

The example below demonstrates how a message can be received and processed using **PeekLock** mode (not the default mode).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/en-us/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>How to: Handle application crashes and unreadable messages

Service Bus provides functionality to help you gracefully recover from
errors in your application or difficulties processing a message. If a
receiver application is unable to process the message for some reason,
then it can call the **unlockMessage** method on the received message
(instead of the **deleteMessage** method). This will cause Service Bus
to unlock the message within the queue and make it available to be
received again, either by the same consuming application or by another
consuming application.

There is also a timeout associated with a message locked within the
queue, and if the application fails to process the message before the
lock timeout expires (e.g., if the application crashes), then Service
Bus will unlock the message automatically and make it available to be
received again.

In the event that the application crashes after processing the message
but before the **deleteMessage** request is issued, then the message
will be redelivered to the application when it restarts. This is often
called **At Least Once Processing**, that is, each message will be
processed at least once but in certain situations the same message may
be redelivered. If the scenario cannot tolerate duplicate processing,
then adding additional logic to your application to handle duplicate message delivery is recommended. This is often achieved
using the **getMessageId** method of the message, which will remain
constant across delivery attempts.

##<a id="NextSteps"></a>Next steps

Now that you've learned the basics of Service Bus queues, see the MSDN
topic [Queues, Topics, and Subscriptions][] for more information.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Service Bus Queue Diagram]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
[Azure Management Portal]: http://manage.windowsazure.com/
[Service Bus Node screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
[Create a New Namespace screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[Available Namespaces screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[Namespace List screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
[Properties Pane screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
[Default Key screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
[Queues, Topics, and Subscriptions]: http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx
[require_once]: http://php.net/require_once


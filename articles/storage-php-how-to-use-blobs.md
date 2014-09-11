<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="How to use blob storage (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#How to use the Blob service from PHP

This guide will show you how to perform common scenarios using the Azure Blob service. The samples are written in PHP and use the [Azure SDK for PHP] [download]. The scenarios covered include **uploading**, **listing**, **downloading**, and **deleting** blobs. For more information on blobs, see the [Next Steps](#NextSteps) section.

##Table of Contents

* [What is Blob Storage](#what-is)
* [Concepts](#concepts)
* [Create an Azure storage account](#CreateAccount)
* [Create a PHP application](#CreateApplication)
* [Configure your application to access the Blob Service](#ConfigureStorage)
* [Setup an Azure storage connection](#ConnectionString)
* [How to: Create a container](#CreateContainer)
* [How to: Upload a Blob into a Container](#UploadBlob)
* [How to: List the Blobs in a container](#ListBlobs)
* [How to: Download a Blob](#DownloadBlob)
* [How to: Delete a Blob](#DeleteBlob)
* [How to: Delete a Blob container](#DeleteContainer)
* [Next steps](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Create an Azure storage account</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>Create a PHP application</h2>

The only requirement for creating a PHP application that accesses the Azure Blob service is the referencing of classes in the Azure SDK for PHP from within your code. You can use any development tools to create your application, including Notepad.

In this guide, you will use service features which can be called within a PHP application locally, or in code running within an Azure web role, worker role, or website.

<h2><a id="GetClientLibrary"></a>Get the Azure Client Libraries</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>Configure your application to access the Blob service</h2>

To use the Azure Blob service APIs, you need to:

1. Reference the autoloader file using the [require_once][require_once] statement, and
2. Reference any classes you might use.

The following example shows how to include the autoloader file and reference the **ServicesBuilder** class.

> [WACOM.NOTE]
> This example (and other examples in this article) assume you have installed the PHP Client Libraries for Azure via Composer. If you installed the libraries manually or as a PEAR package, you will need to reference the `WindowsAzure.php` autoloader file.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In the examples below, the `require_once` statement will be shown always, but only the classes necessary for the example to execute will be referenced.

<h2><a id="ConnectionString"></a>Setup an Azure storage connection</h2>

To instantiate an Azure Blob service client you must first have a valid connection string. The format for the blob service connection string is:

For accessing a live service:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

For accessing the emulator storage:

	UseDevelopmentStorage=true


To create any Azure service client you need to use the **ServicesBuilder** class. You can:

* pass the connection string directly to it or
* use the **CloudConfigurationManager (CCM)** to check multiple external sources for the connection string:
	* by default it comes with support for one external source - environmental variables
	* you can add new sources by extending the **ConnectionStringSource** class

For the examples outlined here, the connection string will be passed directly.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>How to: Create a container</h2>

A **BlobRestProxy** object lets you create a blob container with the **createContainer** method. When creating a container, you can set options on the container, but doing so is not required. (The example below shows how to set the container ACL and container metadata.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();	

	// Set public access policy. Possible values are 
	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	
	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
	// request, but cannot enumerate containers within the storage account.
	//
	// BLOBS_ONLY:
	// Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
	// anonymous request.
	// If this value is not specified in the request, container data is 
	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
	
	// Set container metadata
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Calling **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** makes the container and blob data accessible via anonymous requests. Calling **setPublicAccess(PublicAccessType::BLOBS_ONLY)** makes only blob data accessible via anonymous requests. For more information about container ACLs, see [Set Container ACL (REST API)][container-acl].

For more information about Blob service error codes, see [Blob Service Error Codes][error-codes].

<h2><a id="UploadBlob"></a>How to: Upload a Blob into a container</h2>

To upload a file as a blob, use the **BlobRestProxy->createBlockBlob** method. This operation will create the blob if it doesn't exist, or overwrite it if it does. The code example below assumes that the container has already been created and uses [fopen][fopen] to open the file as a stream.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Note that the example above uploads a blob as a stream. However, a blob can also be uploaded as a string using, for example, the [file\_get\_contents][file_get_contents] function. To do this, change `$content = fopen("c:\myfile.txt", "r");` in the example above to `$content = file_get_contents("c:\myfile.txt");`.

<h2><a id="ListBlobs"></a>How to: List the Blobs in a container</h2>

To list the blobs in a container, use the **BlobRestProxy->listBlobs** method with a **foreach** loop to loop through the result. The following code outputs the name of each blob in a container and its URI to the browser.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();
		
		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>How to: download a Blob</h2>

To download a blob, call the **BlobRestProxy->getBlob** method, then call the **getContentStream** method on the resulting **GetBlobResult** object.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Note that the example above gets a blob as a stream resource (the default behavior). However, you can use the [stream\_get\_contents][stream-get-contents] function to convert the returned stream to a string.

<h2><a id="DeleteBlob"></a>How to: Delete a Blob</h2>

To delete a blob, pass the container name and blob name to **BlobRestProxy->deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="DeleteContainer"></a>How to: Delete a Blob container</h2>

Finally, to delete a blob container, pass the container name to **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="NextSteps"></a>Next steps</h2>

Now that you've learned the basics of the Azure Blob service, follow these links to learn how to do more complex storage tasks.

- See the MSDN Reference: [Storing and Accessing Data in Azure] []
- Visit the Azure Storage Team Blog: <http://blogs.msdn.com/b/windowsazurestorage/>
- See the PHP block blob example at <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- See the PHP page blob example at <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

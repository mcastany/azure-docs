<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Table Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# How to Use the Table Service from Node.js

This guide shows you how to perform common scenarios using the Windows
Azure Table service. The samples are written written using the
Node.js API. The scenarios covered include **creating and deleting a
table, inserting and querying entities in a table**. For more
information on tables, see the [Next Steps][] section.

## Table of Contents

* [What is the Table Service?][]   
* [Concepts][]   
* [Create an Azure Storage Account](#create-account)
* [Create a Node.js Application](#create-app)
* [Configure your Application to Access Storage](#configure-access)
* [Setup an Azure Storage Connection](#setup-connection-string)  
* [How To: Create a Table](#create-table)
* [How To: Add an Entity to a Table](#add-entity)
* [How To: Update an Entity](#update-entity)
* [How to: Work with Groups of Entities](#change-entities)
* [How to: Retrieve an Entity](#query-for-entity)
* [How to: Query a Set of Entities](#query-set-entities)
* [How To: Delete an Entity](#delete-entity)
* [How To: Delete a Table](#delete-table)   
* [How to: Work with Shared Access Signatures](#sas)
* [Next Steps][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a>Create an Azure Storage account</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Create a Node.js Application

Create a blank Node.js application. For instructions creating a Node.js application, see [Create and deploy a Node.js application to an Azure Web Site], [Node.js Cloud Service][Node.js Cloud Service] (using Windows PowerShell), or [Web Site with WebMatrix].

## <a name="configure-access"> </a>Configure Your Application to Access Storage

To use Azure storage, you need the Azure Storage SDK for Node.js, which includes a set of convenience libraries that
communicate with the storage REST services.

### Use Node Package Manager (NPM) to obtain the package

1.  Use a command-line interface such as **PowerShell** (Windows,) **Terminal** (Mac,) or **Bash** (Unix), navigate to the folder where you created your sample application.

2.  Type **npm install azure-storage** in the command window, which should result in the following output:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  You can manually run the **ls** command to verify that a
    **node\_modules** folder was created. Inside that folder you will
    find the **azure-storage** package, which contains the libraries you need to
    access storage.

### Import the package

Using Notepad or another text editor, add the following to the top the
**server.js** file of the application where you intend to use storage:

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Setup an Azure Storage Connection

The azure module will read the environment variables AZURE\_STORAGE\_ACCOUNT and AZURE\_STORAGE\_ACCESS\_KEY, or AZURE\_STORAGE\_CONNECTION\_STRING for information required to connect to your Azure storage account. If these environment variables are not set, you must specify the account information when calling **TableService**.

For an example of setting the environment variables in the management portal for an Azure Website, see [Node.js Web Application with Storage]

## <a name="create-table"> </a>How to Create a Table

The following code creates a **TableService** object and uses it to
create a new table. Add the following near the top of **server.js**.

    var tableSvc = azure.createTableService();

The call to **createTableIfNotExists** will create a new table with the specified name if it does
not already exist. The following example creates a new table named 'mytable' if it does not already exist:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

The `result` will be `true` if a new table is created, and `false` if the table already exists. `response` will contain information about the request.

###Filters

Optional filtering operations can be applied to operations performed using **TableService**. Filtering operations can include logging, automatically retrying, etc. Filters are objects that implement a method with the signature:

		function handle (requestOptions, next)

After doing its preprocessing on the request options, the method needs to call "next" passing a callback with the following signature:

		function (returnObject, finalCallback, next)

In this callback, and after processing the returnObject (the response from the request to the server), the callback needs to either invoke next if it exists to continue processing other filters or simply invoke finalCallback otherwise to end up the service invocation.

Two filters that implement retry logic are included with the Azure SDK for Node.js, **ExponentialRetryPolicyFilter** and **LinearRetryPolicyFilter**. The following creates a **TableService** object that uses the **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>How to Add an Entity to a Table

To add an entity, first create an object that defines your entity
properties. All entities must contain a **PartitionKey** and **RowKey**, which are unique identifiers for the entity.

* **PartitionKey** - Determines the partition that the entity is stored in.

* **RowKey** - Uniquely identifies the entity within the partition.

Both **PartitionKey** and **RowKey** must be string values. For more information, see [Understanding the Table Service data model](http://msdn.microsoft.com/library/azure/dd179338.aspx).

The following is an example of defining an entity. Note that **dueDate** is defined as a type of **Edm.DateTime**. Specifying the type is optional, and types will be inferred if not specified.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [WACOM.NOTE] There is also a **Timestamp** field for each record, which is set by Azure when an entity is inserted or updated.

You can also use the **entityGenerator** to create entities. The following example creates the same task entity using the **entityGenerator**.

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

To add an entity to your table, pass the entity object to
the **insertEntity** method.

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

If the operation is successful, `result` will contain the [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) of the inserted record and `response` will contain information about the operation.

> [WACOM.NOTE] By default, **insertEntity** does not return the inserted entity as part of the `response` information. If you plan on performing other operations on this entity, or wish to cache the information, it can be useful to have it returned as part of the `result`. You can do this by enabling **echoContent** as follows:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>How to Update an Entity

There are multiple methods available to update an existing entity:

* **updateEntity** - Updates an existing entity by replacing it.

* **mergeEntity** - Updates an existing entity by merging new property values into the existing entity.

* **insertOrReplaceEntity** - Updates an existing entity by replacing it. If no entity exists, a new one will be inserted.

* **insertOrMergeEntity** - Updates an existing entity by merging new property values into the existing. If no entity exists, a new one will be inserted.

The following example demonstrates updating an entity using **updateEntity**:

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [WACOM.NOTE] By default, updating an entity does not check to see if the data being updated has previously been modified by another process. To support concurrent updates:
> 
> 1. Get the ETag of the object being updated. This is returned as part of the `response` for any entity related operation and can be retrieved through `response['.metadata'].etag`.
> 
> 2. When performing an update operation on an entity, add the ETag information previously retrieved to the new entity. For example:
> 
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3. Perform the update operation. If the entity has been modified since you retrieved the ETag value, such as another instance of your application, an `error` will be returned stating that the update condition specified in the request was not satisfied.
    
With **updateEntity** and **mergeEntity**, if the entity that is being updated doesn't exist then the update operation will fail. Therefore if you wish to store an entity regardless of whether it already exists, you should instead use **insertOrReplaceEntity** or **insertOrMergeEntity**.

The `result` for successful update operations will contain the **Etag** of the updated entity.

## <a name="change-entities"> </a>How to Work with Groups of Entities

Sometimes it makes sense to submit multiple operations together in a
batch to ensure atomic processing by the server. To accomplish that, you
use the **TableBatch** class to create a batch, and then use the **executeBatch** method of **TableService** to perform the batched operations.

 The following example demonstrates submitting two entities in a batch:

    var task1 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();
	
	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

For successful batch operations, `result` will contain information for each operation in the batch.

###Working with batched operations

Operations added to a batch can be inspected by viewing the `operations` property. You can also use the following methods to work with operations.

* **clear** - clears all operations from a batch.

* **getOperations** - gets an operation from the batch.

* **hasOperations** - returns true if the batch contains operations.

* **removeOperations** - removes an operation.

* **size** - returns the number of operations in the batch.

## <a name="query-for-entity"> </a>How to retrieve an Entity

If you wish to return a specific entity based on the **PartitionKey** and **RowKey**, use the **retrieveEntity** method.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

Once this operation completes, `result` will contain the entity.

## <a name="query-set-entities"> </a>How to Query a Set of Entities

To query a table, use the **TableQuery** object to build up a query
expression using the following clauses:

* **select** - The fields to be returned from the query.

* **where** - The where clause.

	* **and** - An `and` where condition.

	* **or** - An `or` where condition.

* **top** - The number of items to fetch.


The following example builds a query that will return the top 5 items with a PartitionKey of 'hometasks'.

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

Since **select** is not used, all fields will be returned. To perform the query against a table, use **queryEntities**. The following example uses this query to return entities from 'mytable'.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

If successful, `result.entries` will contain an array of entities that match the query. If the query was unable to return all entities, `result.continuationToken` can be used as the third parameter of **queryEntities** to retrieve more results. For the initial query, the second parameter should be *null*.

###How to Query a Subset of Entity Properties

A query to a table can retrieve just a few fields from an entity.
This reduces bandwidth and can improve query performance, especially for large entities. Use the **select** clause and pass the names of the fields to be returned. For example, the following query will only return the **description** and **dueDate** fields.

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>How to Delete an Entity

You can delete an entity using its partition and row keys. In this
example, the **task1** object contains the **RowKey** and
**PartitionKey** values of the entity to be deleted. Then the object is
passed to the **deleteEntity** method.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [WACOM.NOTE] You should consider using ETags when deleting items, to ensure that the item hasn't been modified by another process. See [How To: Update an Entity][] for information in using ETags.

## <a name="delete-table"> </a>How to Delete a Table

The following code deletes a table from a storage account.

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

If you are uncertain whether the table exists, use **deleteTableIfExists**.

## <a name="sas"></a>How to: Work with Shared Access Signatures

Shared Access Signatures (SAS) are a secure way to provide granular access to tables without providing your storage account name or keys. SAS are often used to provide limited access to your data, such as allowing a mobile app to query records.

A trusted application such as a cloud-based service generates a SAS using the **generateSharedAccessSignature** of the **TableService**, and provides it to an untrusted or semi-trusted application. For example, a mobile app. The SAS is generated using a policy, which describes the start and end dates during which the SAS is valid, as well as the access level granted to the SAS holder.

The following example generates a new shared access policy that will allow the SAS holder to query ('r') the table, and expires 100 minutes after the time it is created.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
		
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

Note that the host information must be provided also, as it is required when the SAS holder attempts to access the table.

The client application then uses the SAS with **TableServiceWithSAS** to perform operations against the table. The following example connects to the table and performs a query.

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');
		
	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

Since the SAS was generated with only query access, if an attempt were made to insert, update, or delete entities, an error would be returned.

###Access control lists

You can also use an Access Control List (ACL) to set the access policy for a SAS. This is useful if you wish to allow multiple clients to access the table, but provide different access policies for each client.

An ACL is implemented using an array of access policies, with an ID associated with each policy. The  following example defines two policies; one for 'user1' and one for 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

The following example gets the current ACL for the **hometasks** table, then adds the new policies using **setTableAcl**. This approach allows:

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Once the ACL has been set, you can then create a SAS based on the ID for a policy. The following example creates a new SAS for 'user2':

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>Next Steps

Now that you've learned the basics of table storage, follow these links
to learn how to do more complex storage tasks.

-   See the MSDN Reference: [Storing and Accessing Data in Azure][].
-   [Visit the Azure Storage Team Blog][].
-   Visit the [Azure Storage SDK for Node][] repository on GitHub.

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [Next Steps]: #next-steps
  [What is the Table Service?]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Create a Node.js Application]: #create-app
  [Configure your Application to Access Storage]: #configure-access
  [Setup an Azure Storage Connection]: #setup-connection-string
  [How To: Create a Table]: #create-table
  [How To: Add an Entity to a Table]: #add-entity
  [How To: Update an Entity]: #update-entity
  [How to: Work with Groups of Entities]: #change-entities
  [How to: Query for an Entity]: #query-for-entity
  [How to: Query a Set of Entities]: #query-set-entities
  [How To: Query a Subset of Entity Properties]: #query-entity-properties
  [How To: Delete an Entity]: #delete-entity
  [How To: Delete a Table]: #delete-table

  [OData.org]: http://www.odata.org/
  [using the REST API]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com

  [Node.js Cloud Service]: /en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Visit the Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Web Site with WebMatrix]: /en-us/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Node.js Cloud Service with Storage]: /en-us/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Node.js Web Application with Storage]: /en-us/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Create and deploy a Node.js application to an Azure Web Site]: /en-us/documentation/articles/web-sites-nodejs-develop-deploy-mac/

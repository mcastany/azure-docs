<properties title="Manage DocumentDB capacity and performance" pageTitle="Manage DocumentDB capacity and performance | Azure" description="Learn how you can elastically scale DocumentDB to meet the performance and storage needs of your application." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

#Manage DocumentDB capacity and performance
DocumentDB is a fully managed, massively scalable document oriented NoSQL database service.  With DocumentDB, you don’t have to rent virtual machines, deploy software, monitor databases or worry about disaster recovery. DocumentDB is operated and continuously monitored by Microsoft engineers to deliver world class availability, performance and data protection.  

You can get started with DocumentDB by creating a database account through the Microsoft Azure portal. DocumentDB is offered in stackable capacity units (CUs) of SSD backed storage and throughput. You can elastically scale DocumentDB to meet the performance and storage needs of your application. 

Each capacity unit comes with a quota of elastic collections for storing document data, provisioned document storage and provisioned throughput as request units per second. If the capacity requirements of your application change, you can scale up or scale down the amount of provisioned capacity in your database account. Capacity provisioned under a database account is available for all databases and collections that exist or are created within the Account.  

#Database Account and Administrative Quota
As an Azure subscriber, you can provision one or more DocumentDB database accounts. Each database account comes with a quota of administrative resources including 100 databases, 500,000 users and 2,000,000 permissions.   

#Databases with Unlimited Document Storage
A single DocumentDB database can contain practically an unlimited amount of document storage partitioned by collections. Collections form the transaction domains for the documents contained within them. A DocumentDB database is elastic by default – ranging from a few GB to potentially petabytes of SSD backed document storage and provisioned throughput. Unlike a traditional RDBMS database, a database in DocumentDB is not scoped to a single machine.   

With DocumentDB, as your application’s scale needs grow you can create more collections or databases or both. Indeed, various first party applications within Microsoft have been using DocumentDB at consumer scale by creating extremely large DocumentDB databases each containing thousands of collections with terabytes of document storage. You can grow or shrink a database by adding or removing collections to meet your application’s scale requirements. You can create any number of collections within a database subject to offer availability and the amount of capacity units you purchase. The SSD backed storage and throughput provisioned for you can be spread across the collections under the databases in your database account. 

#Elastic Collections
Each DocumentDB database can contain one or more collections. A collection provides the scope for document storage and query execution. A collection is also a transaction domain for all the documents contained within it. Collections are elastic and can grow and shrink in storage and throughput . You can create any number of collections to meet the scale requirements of your applications. In order to create collections, you first need to buy one or more capacity units (CU). Each capacity unit includes a quota of collections, if you reach the collection quota for your account you can purchase additional capacity units.  

>[AZURE.NOTE] Note that in the Preview release the collections can grow and shrink anywhere between 0-10GB. 

#Provisioned Storage and Throughput as Capacity Units
You can provision stackable units of SSD backed document storage and throughput as capacity units (CU). You can elastically scale DocumentDB with predictable performance by purchasing more capacity units, to meet your application’s needs for read scale, storage and throughput.  
 
Each CU comes with 3 elastic collections, 10GB of SSD backed provisioned document storage and 2000 request units (RU) worth of provisioned throughput. The provisioned storage and the throughput capacity associated with a CU is distributed across the DocumentDB collections you create.   

#Provisioned Throughput, Request Units and Database Operations
Unlike a key-value NoSQL stores which offer simple GET and PUT operations, DocumentDB allows for a richer set of database operations including relational and hierarchical queries with UDFs, stored procedures and triggers – all operating on the documents within a database collection. The cost associated with each of these operations will vary based on the CPU, IO and memory required to complete the operation.  Instead of thinking about and managing hardware resources, you can think of a request unit (RU) as a single measure for the resources required to perform various database operations and service an application request.   

Request units are provisioned for each Database Account based on the number of capacity units that you purchase. Request unit consumption is evaluated as a rate per second. Applications that exceed the provisioned request unit rate for their account will be throttled until the rate drops below the reserved level for the Account. If your application requires a higher level of throughput, you can purchase additional capacity units.  

The following table provides a list of various database operations and the request units available per CU. The table helps you plan how a given database operation consumes the provisioned throughput associated with a CU.  We assume that the document size is of 1KB consisting of 10 unique property values with the default consistency level is set to “Session” and all of the documents automatically indexed by DocumentDB. 

|Database Operations|Number of operations per second per CU|
|-------------------|--------------------------------------|
|Reading a single document	|2000
|Inserting/Replacing/Deleting a single document	|500
|Query a collection with a simple predicate and returning a single document	|1000
|Stored Procedure with 50 document inserts	|20

Notice from the table that the request units consumed by insert/replace/delete operations for automatically indexed documents is approximately 4x that of read of a document. The table above is simply a reference. In practice however,  

-	Your documents sizes may vary (and not correspond to 1KB), 
-	Your documents may have more than 10 properties.
-	You may choose to set the default consistency level may be set to Strong, Bounded Staleness or Eventual
-	You may choose not to index only some of your documents 
-	You may choose to index only some of your properties and not let DocumentDB automatically index everything
-	Your queries and stored procedures may be far more complex.  

For getting an accurate number of request units for your application, you can use the request unit calculator for calculating the request units consumed for various operations. You can also inspect the x-ms-request-charge response header which includes the number of request units consumed for a given request.  

[Request Unit Calculator](http://go.microsoft.com/fwlink/?LinkID=510088)  

#Collections and Provisioned Throughput
Provisioned throughput for your database account is allocated uniformly across all collections up to the maximum throughput level (Request Units) for a single collection. For example, if you purchase a single capacity unit and create a single collection, all of the provisioned throughput for the CU will be available to the collection. If an additional collection is created the provisioned throughput will be allocated evenly with each collection receiving half of all provisioned throughput.  

#Choice of Consistency Level and Throughput
The choice of default consistency level has an impact on the throughput and latency.  You can set the default consistency level both programmatically and through the Azure portal. You can also override the consistency level on a per request basis. By default, the consistency level is that of session which provides monotonic read/writes and read your write guarantees. Session consistency is great for user centric applications and provides an ideal balance of consistency and performance tradeoffs.   

-	Session and Eventual consistency levels provide approximately 2000 read requests of documents and 500 insert/replace/delete of documents.
-	Strong and Bounded staleness consistency levels provide approximately 1200 read requests of documents approximately and 500 insert/replace/delete of documents. The inserts/replace/deletes with Bounded Staleness are significantly lower in latency than Strong.  

[Request Unit Calculator](http://go.microsoft.com/fwlink/?LinkID=510088)

#Provisioned Document Storage and Index Overhead
With each CU purchased your account is provisioned with 10GB of SSD backed document storage. The 10GB of document storage includes the documents plus storage for the index. By default, a DocumentDB collection is configured to automatically index all of the documents without explicitly requiring any secondary indices or schema. Based production usage in consumer scale first party applications using DocumentDB, the typical index overhead is between 2-20%. The indexing technology used by DocumentDB ensures that regardless of the values of the properties, the index overhead does not exceed more than 80% of the size of the documents with default settings.  

By default all documents are indexed by DocumentDB automatically. However, in case you want to fine tune the index overhead, you can chose to remove certain documents from being indexed at the time of inserting or replacing a document. You can configure a DocumentDB collection to exclude all documents within the collection from being indexed. You can also configure a DocumentDB collection to selectively index only a certain properties or paths with wildcards of your JSON documents.  Excluding properties or documents also improves the write throughput – which means you will consume fewer request units.   
 
[Request Unit Calculator](http://go.microsoft.com/fwlink/?LinkID=510088)



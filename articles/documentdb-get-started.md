<properties title="Get started with a DocumentDB account" pageTitle="Get started with a DocumentDB account | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within the account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="paulettm" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

#Get started with a DocumentDB account  

This guide shows you how to get started using **Azure DocumentDB (Preview)**.  The samples are written in C# code and use the DocumentDB .NET SDK.  The scenarios covered include creating and configuring a DocumentDB account, creating databases, creating collections and storing JSON documents within the account.  For more information on using Azure DocumentDB, refer to the Next Steps section.  

In order to use this getting started guide, you must have a DocumentDB account and the access key (either primary or secondary) of the account. For more information, see:  

-	[Create a Document DB Account][documentdb-create-account]

##Table of contents
-	[Connect to a DocumentDB Account][]
-	[Create a database][]
-	[Create a collection][]
-	[Create documents][]
-	[Query DocumentDB Resources][]
-	[Next steps][]  

##<a id="Connect"></a>Connect to a DocumentDB Account
There are various SDKs and APIs available to programmatically work with DocumentDB.  The samples below are shown in C# code and use the DocumentDB .NET SDK.  

We'll start by creating a DocumentClient in order to establish a connection to our DocumentDB account.   We'll need the following references in our C# application:  

    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Microsoft.Azure.Documents;  
 
A DocumentClient can be instantiated using the DocumentDB account endpoint and either the primary or secondary access key associated with the account.  

The DocumentDB account endpoint and keys can be obtained from the Azure management preview portal blade for your DocumentDB account. 

![][1]
 
>Note that the DocumentDB access keys available from the Keys blade grant administrative access to your DocumentDB account and of the resources in it.  DocumentDB also supports the use of resource keys that allow clients to read, write and delete resources in the DocumentDB account according to the permissions you've granted, without the need for an account key.    

Create the client using code like the following example.  

    private static string endpointUrl = "<your endpoint URI>";
    private static string authorizationKey = "<your key>";
    
    //Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(endpointUrl), authorizationKey);  

**Warning:** Never store credentials in source code. To keep this sample simple, they are shown in the source code. See [Windows Azure Web Sites: How Application Strings and Connection Strings Work](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) for information on how to store credentials.  

Now that you know how to connect to a DocumentDB account and create an instance of DocumentClient, let's take a look at working with DocumentDB resources.  

##<a id="CreateDB"></a>Create a database
Using the .NET SDK, a DocumentDB database can be created via the CreateDatabaseAsync method of a DocumentClient.  

    //Create a Database
	 Database database = await client.CreateDatabaseAsync(
 		new Database
 		{
 		Id = "FamilyRegistry"
 		});



##<a id="CreateColl"></a>Create a collection  

Using the .NET SDK, a DocumentDB collection can be created via the CreateDocumentCollectionAsync method of a DocumentClient.  The database created in the previous step has a number of properties, one of which is the SelfLink property.  With that information, we can now create a collection.  

    	//Create a document collection 
	documentCollection = new DocumentCollection
		{
			Id = "FamilyCollection"
		};
	
		documentCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,documentCollection); 

    
##<a id="CreateDoc"></a>Create documents	
Using the .NET SDK, a DocumentDB document can be created via the CreateDocumentAsync method of a DocumentClient.  The collection created in the previous step has a number of properties, one of which is the DocumentsLink property.  With that information, we can now insert 1 or more documents.  For the purposes of this example, we'll assume that we have a Family class that describes the attributes of a family such as name, gender and age.  

	private static async Task CreateDocuments(string 	colSelfLink)
	{
		Family AndersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents =  new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new [] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };

        await client.CreateDocumentAsync(colSelfLink, AndersonFamily);

        Family WakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new [] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child
                {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

         await client.CreateDocumentAsync(colSelfLink, WakefieldFamily);
	}   
 

##<a id="Query"></a>Query DocumentDB Resources
DocumentDB supports rich queries against the JSON documents stored in each collection.  The sample code below shows various queries - using both DocumentDB SQL syntax as well as LINQ - that we can run against the documents we inserted in the previous step.  

	//
	//Querying the documents using DocumentDB SQL for the Andersen family
	//
	foreach (var family in client.CreateDocumentQuery(collectionLink, 
    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
	{
    Console.WriteLine("\tRead {0} from SQL", family);
	}

	//
	//Querying the documents using LINQ for the Andersen family
	//
	foreach (var family in (
    	from f in client.CreateDocumentQuery(collectionLink)
    	where f.Id == "AndersenFamily"
    	select f))
	{
   	 Console.WriteLine("\tRead {0} from LINQ", family);
	}

	//
	//Querying the documents using LINQ lambdas for the Andersen family
	//
	foreach (var family in client.CreateDocumentQuery(collectionLink)
    .Where(f => f.Id == "AndersenFamily")
    .Select(f => f))
	{
    	Console.WriteLine("\tRead {0} from LINQ query", family);
	}

	//
	//DocumentDB SQL -  using <> interchangably with != for "not equals"
	//
	families = client.CreateDocumentQuery<Family>(colSelfLink, "SELECT * FROM Families f WHERE f.id <> 'AndersenFamily'");

	//   
	// LINQ - combine equality and inequality
	//
	families = from f in client.CreateDocumentQuery<Family>(colSelfLink)
           where f.Id == "Wakefield" && f.Address.City != "NY"
           select f; 
	


##<a id="NextSteps"></a>Next steps
-	Learn how to [monitor a DocumentDB account](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	For details on the programming model, see the Development section on the [DocumentDB documentation page](http://go.microsoft.com/fwlink/p/?LinkID=402319 )


[Connect to a DocumentDB Account]: #Connect
[Create a database]: #CreateDB
[Create a collection]: #CreateColl
[Create documents]: #CreateDoc
[Query DocumentDB Resources]: #Query
[Next steps]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

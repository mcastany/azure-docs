<properties pageTitle="How to Use Azure Redis Cache" metaKeywords="" description="Learn how to create a use a cache in Azure Redis Cache" metaCanonical="" services="" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# How to Use Azure Redis Cache

This guide shows you how to get started using 
**Azure Redis Cache (Preview)**. The samples are written in C\# code and
use the .NET API. The scenarios covered include **creating and configuring a cache**, **configuring cache clients**, **adding and removing objects from the cache**, and **storing ASP.NET session state in the cache**. For more
information on using Azure Redis Cache, refer to the [Next Steps][] section.

## Table of Contents

-   [What is Azure Redis Cache?][]
-	[Get started with Azure Redis Cache][]
	-	[Create the cache][]
	-	[Configure the cache clients][]
-	[Working with caches][]
	-	[Connect to the cache][]
	-   [Add and retrieve objects from the cache][]
	-   [Specify the expiration of an object in the cache][]
	-   [Store ASP.NET session state in the cache][]
-   [Next Steps][]

<a name="what-is"></a>
## What is Azure Redis Cache?

Microsoft Azure Redis Cache, currently in preview, is based on the popular open source Redis Cache. It gives you access to a secure, dedicated Redis cache, managed by Microsoft. A cache created using Azure Redis Cache is accessible from any application within Microsoft Azure.

Microsoft Azure Redis Cache is available in two tiers:

-	**Basic** – Single node. Multiple sizes up to 26 GB.
-	**Standard** – Two-node Master/Slave. Multiple sizes up to 26 GB.

Each tier differs in terms of features and pricing. The features are covered later in this guide, and for more information on pricing, see [Cache Pricing Details][].

This guide provides an overview of getting started with Azure Redis Cache. For more detailed information on these features that are beyond the scope of this getting started guide, see [Overview of Azure Redis Cache][].

<a name="getting-started-cache-service"></a>
## Get Started with Azure Redis Cache

Getting started with Azure Redis Cache is easy. To get started, you provision and configure a cache. Next, you configure the cache clients so they can access the cache. Once the cache clients are configured, you can begin working with them.

-	[Create the cache][]
-	[Configure the cache][]
-	[Configure the cache clients][]

<a name="create-cache"></a>
## Create a cache

To create a cache, first sign in to the Azure management preview portal, and click **New**, **Redis Cache (Preview)**.

![New cache][NewCacheMenu]

In the **New Redis Cache (Preview)** blade, specify the desired configuration for the cache.

![Create cache][CacheCreate]

In **Dns name**, enter a subdomain name to use for the cache endpoint. The endpoint must be a string between six and twenty characters, contain only lowercase numbers and letters, and must start with a letter.

Use **Pricing Tier** to select the desired cache size and features. Redis Cache is available in the following two tiers.

-	**Basic** - Single node, multiple sizes up to 26 GB.
-	**Standard** - Two node master/slave, 99.9% SLA (Post Preview), multiple sizes up to 26 GB.

For **Subscription**, select the Azure subscription that you want to use for the cache.

>If your account has only one subscription, it will be automatically selected and the Subscription drop-down will not be displayed.

In **Resource group**, select or create a resource group for your cache.

>For more information, see [Using Resource groups to manage your Azure resources][]. 

Use **Geolocation** to specify the geographic location in which your cache is hosted. For the best performance, Microsoft strongly recommends that you create the cache in the same region as the cache client application.

Once the new cache options are configured, click **Create**. It can take a few minutes for the cache to be created. To check the status, you can monitor the progress on the startboard. After the cache has been created, your new cache has a **Running** status and is ready for use with default settings.

![Cache created][CacheCreated]

Once your cache is created, you can access it from the **Browse** blade.

![Browse blade][BrowseCaches]

Click **Caches** to view your caches.

![Caches][Caches]

<a name="NuGet"></a>
## Configure the cache clients

A cache created using Azure Redis Cache is accessible from any Azure application. .NET applications developed in Visual Studio can use the **StackExchange.Redis** cache client, which can be configured using a NuGet package that simplifies the configuration of cache client applications. 

>For more information, see the [StackExchange.Redis][] github page and  the[StackExchange.Redis cache client documentation][].

To configure a client application in Visual Studio using the StackExchange.Redis NuGet package, right-click the project in **Solution Explorer** and choose **Manage NuGet Packages**. 

![Manage NuGet packages][NuGetMenu]

Type **StackExchange.Redis** or **StackExchange.Redis.StrongName** into the **Search Online** text box, select the desired version from the results, and click **Install**.

>If you prefer to use a strong-named version of the **StackExchange.Redis** client library, choose **StackExchange.Redis.StrongName**; otherwise choose **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

The NuGet package downloads and adds the required assembly references for your client application to access Azure Redis Cache with the StackExchange.Redis cache client.

Once your client project is configured for caching, you can use the techniques described in the following sections for working with your cache.

<a name="working-with-caches"></a>
## Working with Caches

The steps in this section describe how to perform common tasks with Cache.

-	[Connect to the cache][]
-   [Add and retrieve objects from the cache][]
-   [Store ASP.NET session state in the cache][]

<a name="connect-to-cache"></a>
## Connect to the cache

In order to programatically work with a cache, you need a reference to the cache. Add the following to the top of any file from which you want to use the StackExchange.Redis client to access an Azure Redis Cache:

    using StackExchange.Redis;

>The StackExchange.Redis client requires .NET Framework 4 or higher.

The connection to the Azure Redis Cache is managed by the `ConnectionMultiplexer` class. This class is designed to be shared and reused throughout your client application, and does not need to be created on a per operation basis. 

To connect to an Azure Redis Cache and be returned an instance of a connected `ConnectionMultiplexer`, call the static `Connect` method and pass in the cache endpoint and key like the following example. Use the Azure key generated from the service portal as the password parameter.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>Warning: Never store credentials is source code. To keep this sample simple, I’m showing them in the source code. See [Windows Azure Websites: How Application Strings and Connection Strings Work][] for information on how to store credentials.

If you don't want to use SSL, either set `ssl=false` or just pass in the endpoint and key.

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>For more information on advanced connection configuration options, see [StackExchange.Redis configuration model][].

The cache endpoint and keys can be obtained from the Azure management preview portal blade for your cache instance.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Once the connection is established, return a reference to the redis cache database, by calling the `ConnectionMultiplexer.GetDatabase` method.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>The object returned from the `GetDatabase` method is a lightweight pass-through object and does not need to be stored.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Now that you know how to connect to an Azure Redis Cache instance and return a reference to the cache database, let's take a look at working with the cache.



<a name="add-object"></a>
## Add and retrieve objects from the cache

Items can be stored in and retrieved from a cache by using the `StringSet` and `StringGet` methods.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>Redis stores most data as Redis strings, but these strings can contain many types of data, including serialized binary data, which can be used when storing .NET objects in the cache.

When calling `StringGet`, if the object exists, it is returned, and if it does not, null is returned. In this case you can retrieve the value from the desired data source and store it in the cache for subsequent use. This is known as the cache-aside pattern.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>Azure Redis Cache can cache .NET objects as well as primitive data types, but before a .NET object can be cached it must be serialized. This is the responsibility of the application developer, and gives the developer flexibility in the choice of the serializer. For more information, see [Work with .NET objects in the cache][].

<a name="specify-expiration"></a>
## Specify the expiration of an item in the cache

To specify the expiration of an item in the cache, use the `TimeSpan` parameter of `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Store ASP.NET session state in the cache

Azure Redis Cache provides a session state provider that you can use to store your session state in a cache rather than in-memory or in a SQL Server database. To use the caching session
state provider, first configure your cache, and then configure your ASP.NET application for cache using the Redis Cache Session State NuGet package.

To configure a client application in Visual Studio using the Redis Cache Session State NuGet package, right-click the project in **Solution Explorer** and choose **Manage NuGet Packages**. 

![Manage NuGet packages][NuGetMenu]

Type **RedisSessionStateProvider** into the **Search Online** text box, select it from the results, and click **Install**.

![Redis Cache Session State NuGet Package][SessionStateNuGet]

The NuGet package downloads and adds the required assembly references and adds the following adds the following section into your web.config file that contains the required configuration for your ASP.NET application to use the Redis Cache Session State Provider.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>

The commented section provides an example of the attributes and sample settings for them.

Configure the attributes with the values from your cache blade on the preview portal, and configure the other values as desired.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="false" />
      </providers>
    </sessionState>

>Note that the retry timeout is in milliseconds.

Be sure to comment out the standard **InProc** session state provider.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

For more information about configuring and using the Azure Redis Session State Provider, see [Azure Redis Session State Provider][].

<a name="next-steps"></a>
## Next Steps

Now that you've learned the basics of Azure Redis Cache,
follow these links to learn how to do more complex caching tasks.

-	Learn more about the StackExchange.Redis client: [StackExchange.Redis cache client documentation][]
-	See the [redis][] documentation and read about [redis data types][] and [a fifteen minute introduction to Redis data types][].
-   See the MSDN Reference: [Azure Redis Cache][]


<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[Azure Management Portal]: http://windows.azure.com/
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/en-us/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/en-us/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/en-us/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/en-us/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/en-us/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/en-us/library/z852zf6b.aspx

[StackExchange.Redis configuration model]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/en-us/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/en-us/pricing/details/cache/
[Management Portal]: https://manage.windowsazure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: http://azure.microsoft.com/en-us/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[Windows Azure Websites: How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

## <a name="what-is"> </a>What is Queue Storage?

Azure Queue storage is a service for storing large numbers of
messages that can be accessed from anywhere in the world via
authenticated calls using HTTP or HTTPS. A single queue message can be
up to 64 KB in size, and a queue can contain millions of messages, up to the
total capacity limit of a storage account. A storage account can contain up to 200 TB of blob, queue, and table data. See [Azure Storage Scalability and Performance Targets](http://msdn.microsoft.com/en-us/library/dn249410.aspx) for details about storage account capacity.

Common uses of Queue storage include:

-   <span>Creating a backlog of work to process asynchronously</span>
-   Passing messages from an Azure Web role to an Azure
    Worker role

## <a name="concepts"> </a>Concepts

The Queue service contains the following components:

![Queue1](./media/howto-queue-storage/queue1.png)


- **URL format:** Queues are addressable using the following URL format:   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
The following URL addresses one of the queues in the diagram:  
	http://myaccount.queue.core.windows.net/imagesToDownload

-**Storage Account:** All access to Azure Storage is done through a storage account. See [Azure Storage Scalability and Performance Targets](http://msdn.microsoft.com/en-us/library/dn249410.aspx) for details about storage account capacity.

- **Queue:** A queue contains a set of messages. All messages must be in a queue.

- **Message:** A message, in any format, of up to 64KB.



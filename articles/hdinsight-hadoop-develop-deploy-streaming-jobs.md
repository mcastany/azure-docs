<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Develop C# Hadoop streaming programs for HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Learn how to develop Hadoop streaming MapReduce programs in C#, and how to deploy them to Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Develop C# Hadoop streaming programs for HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />



# Develop C# Hadoop streaming programs for HDInsight

Hadoop provides a streaming API to MapReduce that enables you to write map and reduce functions in languages other than Java. This tutorial walks you through an end-to-end scenario from developing/testing a Hadoop streaming MapReduce program using C# on an HDInsight emulator, to running the MapReduce job on Azure HDInsight, and then to retrieving the results.

**Prerequisites:**

Before you begin this tutorial, you must have the following:

- Install Azure HDInsight Emulator. For instructions, see [Get started using HDInsight Emulator][hdinsight-get-started-emulator].
- Install Azure PowerShell on the emulator computer. For instructions, see [Install and configure Azure PowerShell][powershell-install-configure]
- Obtain an Azure subscription. For instructions, see [Purchase Options][azure-purchase-options], [Member Offers][azure-member-offers], or [Free Trial][azure-free-trial].

##In this article

- [Develop a word count Hadoop streaming program in C#](#develop)
- [Test the program on the emulator](#test)
- [Upload data and the applications to Azure Blob storage](#upload)
- [Run the MapReduce program on Azure HDInsight](#run)
- [Retrieve the MapReduce results](#retrieve)
- [Next steps](#nextsteps)

##<a name="develop"></a>Develop a word count Hadoop streaming program in C&#35;

The word count solution contains two console application projects: mapper and reducer.  The mapper application streams each word into the console and the reducer application counts the number of words that are streamed from a document. Both the mapper and reducer read characters, line by line, from the standard input stream (stdin) and write to the standard output stream (stdout).

**To create C# console application**

1. Open Visual Studio 2013.
2. Click **FILE**, **New**, and then click **Project**.
3. Type or select the following values:

	<table border="1">
	<tr><td>Field</td><td>Value</td></tr>
	<tr><td>Template</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Name</td><td>WordCountMapper</td></tr>
	<tr><td>Location</td><td>C:\Tutorials</td></tr>
	<tr><td>Solution name</td><td>WordCount</td></tr>
	</table>
	
4. Click **OK** to create the project.

**To create the Mapper program**

5. In Solution Explorer, right-click **Program.cs**, and then click **Rename**.
6. Rename the file to **WordCountMapper.cs**, and then press **ENTER**.
7. Click **Yes** to confirm renaming all references.
8. Double-click **WordCountMapper.cs** to open it.
9. Add the following using statement:

		using System.IO;

10. Replace the Main() function with the following:

		static void Main(string[] args)
		{
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    string line;
		    string[] words;
		
		    while ((line = Console.ReadLine()) != null)
		    {
		        words = line.Split(' ');
		
		        foreach (string word in words)
		            Console.WriteLine(word.ToLower());
		    }
		}

11. Click **BUILD**, and then click **Build Solution** to compile the Mapper program.	


**To create the Reducer program**

1. From Visual Studio 2013, click **FILE**, click **Add** and then click **New Project**.
2. Type or select the following values:

	<table border="1">
	<tr><td>Field</td><td>Value</td></tr>
	<tr><td>Template</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Name</td><td>WordCountReducer</td></tr>
	<tr><td>Location</td><td>C:\Tutorials\WordCount</td></tr>
	</table>
3. Click **OK** to create the project.
4. From Solution Explorer, right-click **Program.cs**, and then click **Rename**.
5. Rename the file to **WordCountReducer.cs**, and then press **ENTER**.
7. Click **Yes** to confirm renaming all references.
8. Double-click **WordCountReducer.cs** to open it.
9. Add the following using statement:

		using System.IO;

10. Replace the Main() function with the following:

		static void Main(string[] args)
		{
		    string word, lastWord = null;
		    int count = 0;
		
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    while ((word = Console.ReadLine()) != null)
		    {
		        if (word != lastWord)
		        {
		            if(lastWord != null)
		                Console.WriteLine("{0}[{1}]", lastWord, count);
		
		            count = 1;
		            lastWord = word;
		        }
		        else
		        {
		            count += 1; 
		        }
		    }
		    Console.WriteLine(count);
		}

11. Click **BUILD**, and then click **Build Solution** to compile the solution.	

The Mapper and the Reducer executables are located at:

- C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe
- C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe


##<a name="test"></a>Test the program on the emulator

This section contains the following procedures:

1. upload data to the emulator HDFS
2. upload the Mapper and Reducer to the emulator HDFS
3. submit a word count MapReduce job
4. check the job status
5. retrieve the job results

By default, HDInsight emulator uses HDFS as the default file system.  Optionally, you can configure the HDInsight emulator to use Azure Blob storage. For details, see [Get started with HDInsight Emulator][hdinsight-emulator-wasb]. In this section, you will use the HDFS copyFromLocal command to upload the files. The next section shows you how to upload files using Azure PowerShell. For other methods, see [Upload data to HDInsight][hdinsight-upload-data].

This tutorial uses the following folder structure:

<table border="1">
<tr><td>Folder</td><td>Note</td></tr>
<tr><td>\WordCount</td><td>The root folder for the word count project. </td></tr>
<tr><td>\WordCount\Apps</td><td>The folder for the mapper and reducer executables.</td></tr>
<tr><td>\WordCount\Input</td><td>The MapReduce source file folder.</td></tr>
<tr><td>\WordCount\Output</td><td>The MapReduce output file folder.</td></tr>
<tr><td>\WordCount\MRStatusOutput</td><td>The job output folder.</td></tr>
</table></br>

This tutorial uses the .txt files located in the %hadoop_home% directory.

> [WACOM.NOTE] The Hadoop HDFS commands are case sensitive.

**To copy the text files to the emulator HDFS**

1. From the Hadoop command line window, run the following command to make a directory for the input files:

		hadoop fs -mkdir /WordCount/Input

	The path used here is the relative path. It is equivalent to the following:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Run the following command to copy some text files to the input folder on HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3. Run the following command to list the uploaded files:

		hadoop fs -ls \WordCount\Input

	You shall see about eight .txt files.


**To deploy the Mapper and the Reducer to the emulator HDFS**

1. Open Hadoop command line from your desktop.
2. Run the following commands:

		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3. Run the following command to list the uploaded files

		hadoop fs -lsr /WordCount/Apps

	You shall see the two .exe files.



**To run the MapReduce job using HDInsight PowerShell**

1. Open Azure PowerShell. For instructions, see [Install and configure Azure PowerShell][powershell-install-configure]. 
3. Run the following commands to set variables:

		$clusterName = "http://localhost:50111"
		
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "/WordCount/Input/"
		$mrOutput = "/WordCount/Output"
		$mrStatusOutput = "/WordCount/MRStatusOutput"

	The HDInsight emulator cluster name is "http://localhost:50111".  

4. Run the following commands to define the streaming job:

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

5. Run the following command to create a credential object:

		$creds = Get-Credential -Message "Enter password" -UserName "hadoop"

	You will get a prompt to enter the password.  The password can be any string.  The username must be "hadoop".

6. Run the following commands to submit the MapReduce job and wait for the job to complete:
		
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

	When completed, you will get an output similar to the following:

		StatusDirectory : /WordCount/MRStatusOutput
		ExitCode        : 
		Name            : mrWordCountStreamingJob
		Query           : 
		State           : Completed
		SubmissionTime  : 11/15/2013 7:18:16 PM
		Cluster         : http://localhost:50111
		PercentComplete : map 100%  reduce 100%
		JobId           : job_201311132317_0034
		
	So you know the job ID is, for example, job-201311132317-0034. 

**To check the job status**

1. From the desktop, click **Hadoop MapReduce Status**, or browse to **http://localhost:50030/jobtracker.jsp**.
2. Find the job using the job id under one of the three sections: **Completed Jobs**, **Running Jobs**, **Retired Jobs**. 
3. If a job failed, you can open the job details and find some helpful information for debugging.


**To display the output from HDFS**

1. Open Hadoop command line.
2. Run the following commands to display the output:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	You can append "|more" at the end of the command to get the page view.


##<a id="upload"></a>Upload data to Azure Blob storage
Azure HDInsight uses Azure Blob storage as the default file system. You can configure an HDInsight cluster to use additional Blob storage for the data files. In this section, you will create a storage account and upload the data files to the Blob storage.  The data files are the txt files in the %hadoop_home% directory.


**To create a Blob storage and a container**

1. Open Azure PowerShell.
2. Set the variables, and then run the commands:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Run the following command to create a storage account and a Blob storage container on the account

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Run the following commands to verify the storage account and the container:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**To upload the data files**

1. Open Azure PowerShell.
2. Set the first three variables, and then run the commands:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"

		$localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
		$destFolder = "WordCount/Input"

	Notice the source file folder is **c:\Hadoop\hadoop-1.1.0-SNAPSHOT**, and the destination folder is **WordCount/Input**.

3. Run the following commands to get a list of the txt files in the source file folder:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. Run the following commands to create a storage context object:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5. Run the following commands to copy the files:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Run the following command to list the uploaded files:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder





**To upload the word count applications**

1. Open Azure PowerShell.
2. Set the first three variables, and then run the commands:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Notice the destination folder is **WordCount/Apps**.

4. Run the following commands to create a storage context object:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5. Run the following commands to copy the applications:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

6. Run the following command to list the uploaded files:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	You shall see both file listed there.



##<a name="run"></a>Run the MapReduce job on Azure HDInsight

The following PowerShell script performs the following tasks:

1. provision an HDInsight cluster
	
	1. create a storage account that will be used as the default HDInsight cluster file system
	2. create a Blob storage container 
	3. create an HDInsight cluster

2. submit the MapReduce job

	1. create a streaming MapReduce job definition
	2. submit a MapReduce job
	3. wait for the job to complete
	4. display standard error
	5. display standard output

3. delete the cluster

	1. delete the HDInsight cluster
	2. delete the storage account used as the default HDInsight cluster file system


**To run the PowerShell script**

1. Open Notepad.
2. Copy and paste the following code:
		
		# The storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$serviceNameToken = "<ServiceNameTokenString>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
		$clusterNodes = 1
		
		$clusterName = $serviceNameToken + "hdicluster"
		
		$storageAccountName_Default = $serviceNameToken + "hdistore"
		$containerName_Default =  $serviceNameToken + "hdicluster"

		# The streaming MapReduce job variables
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a storage account
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config
		
		#=============================
		# Create a streaming MapReduce job definition
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green
		
		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)
		
		#=============================
		# Run a streaming MapReduce job
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput
		
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 
		
		# Delete the storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Set the first four variables in the script. $serviceNameToken will be used for the HDInsight cluster name, the storage account name, and the Blob storage container name.  Because the service name must be between 3 and 24 characters, and the script append string with up to 10 character string to the names, you  must limit the string to 14 or less characters. You must use all lower case for the $serviceNameToken. $storageAccountName_Data and $containerName_Data are the storage account and container that are used for storing the data files and the applications. $location must match the data storage account location.
4. Review the rest of the variables.
5. Save the script file.
6. Open Azure PowerShell.
7. Run the following command to set the execution policy to remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. When prompted, enter username and password for the HDInsight cluster. Because you will delete the cluster at the end of the script and you will not need the username and password anymore, the username and password can be any strings. If you don't want to get prompted for the credentials, see [Working with Passwords, Secure Strings and Credentials in Windows PowerShell][powershell-PSCredential]

For a HDInsight .NET SDK sample on submitting Hadoop streaming jobs, see [Submit Hadoop jobs programmatically][hdinsight-submit-jobs].


##<a name="retrieve"></a>Retrieve the MapReduce job output
This section shows you how to download and display the output.  For the information on displaying the results on Excel, see [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][hdinsight-ODBC], and [Connect Excel to HDInsight with Power Query][hdinsight-power-query].


**To retrieve the output**

1. Open Azure PowerShell window.
2. Set the values and then run the commands:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"
	
3. Run the following commands to create an Azure storage context object: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. Run the following commands to download and display the output:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

	

##<a id="nextsteps"></a>Next steps
In this tutorial, you have learned how to develop a Hadoop streaming MapReduce job, how to test the application on HDInsight emulator, and how to write a PowerShell script to provision an HDInsight cluster and run a MapReduce on the cluster. To learn more, see the following articles:

- [Get started with Azure HDInsight](../hdinsight-get-started/)
- [Get started with the HDInsight Emulator][hdinsight-get-started-emulator]
- [Develop Java MapReduce programs for HDInsight][hdinsight-develop-mapreduce]
- [Use Azure Blob storage with HDInsight][hdinsight-storage]
- [Administer HDInsight using PowerShell][hdinsight-admin-powershell]
- [Upload data to HDInsight][hdinsight-upload-data]
- [Use Hive with HDInsight][hdinsight-use-hive]
- [Use Pig with HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator/#blobstorage
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[Powershell-install-configure]: ../install-configure-powershell/







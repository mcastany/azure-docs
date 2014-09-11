<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight-Hadoop" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for Hadoop in HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Develop Java MapReduce programs for Hadoop in HDInsight
This tutorial walks you through an end-to-end scenario for developing and testing a word counting Hadoop MapReduce job in Java on the HDInsight Emulator and then for deploying and running it on Azure HDInsight.

**Prerequisites:**

Before you begin this tutorial, you must have the following:

- Install Azure HDInsight Emulator. For instructions, see [Get started using HDInsight Emulator][hdinsight-emulator].
- Install Azure PowerShell on the emulator computer. For instructions, see [Install and configure Azure PowerShell][powershell-install-configure].
- Obtain an Azure subscription. For instructions, see [Purchase Options][azure-purchase-options], [Member Offers][azure-member-offers], or [Free Trial][azure-free-trial].

##In this article

- [Develop a word counting MapReduce program in Java](#develop)
- [Test the program on the emulator](#test)
- [Upload data files and the application to Azure Blob storage](#upload)
- [Run the MapReduce program on Azure HDInsight](#run)
- [Retrieve the MapReduce results](#retrieve)
- [Next steps](#nextsteps)

##<a name="develop"></a>Develop a word counting MapReduce program in Java

Word counting is a simple application that counts the occurrences of each word in a given input set. 

**To write the word counting MapReduce job in Java**

1. Open Notepad.
2. Copy and paste the following program into notepad.

		package org.apache.hadoop.examples;
		
		import java.io.IOException;
		import java.util.StringTokenizer;
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.fs.Path;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapreduce.Job;
		import org.apache.hadoop.mapreduce.Mapper;
		import org.apache.hadoop.mapreduce.Reducer;
		import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
		import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
		import org.apache.hadoop.util.GenericOptionsParser;
		
		public class WordCount {
		
		  public static class TokenizerMapper 
		       extends Mapper<Object, Text, Text, IntWritable>{
		    
		    private final static IntWritable one = new IntWritable(1);
		    private Text word = new Text();
		      
		    public void map(Object key, Text value, Context context
		                    ) throws IOException, InterruptedException {
		      StringTokenizer itr = new StringTokenizer(value.toString());
		      while (itr.hasMoreTokens()) {
		        word.set(itr.nextToken());
		        context.write(word, one);
		      }
		    }
		  }
		  
		  public static class IntSumReducer 
		       extends Reducer<Text,IntWritable,Text,IntWritable> {
		    private IntWritable result = new IntWritable();
		
		    public void reduce(Text key, Iterable<IntWritable> values, 
		                       Context context
		                       ) throws IOException, InterruptedException {
		      int sum = 0;
		      for (IntWritable val : values) {
		        sum += val.get();
		      }
		      result.set(sum);
		      context.write(key, result);
		    }
		  }
		
		  public static void main(String[] args) throws Exception {
		    Configuration conf = new Configuration();
		    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
		    if (otherArgs.length != 2) {
		      System.err.println("Usage: wordcount <in> <out>");
		      System.exit(2);
		    }
		    Job job = new Job(conf, "word count");
		    job.setJarByClass(WordCount.class);
		    job.setMapperClass(TokenizerMapper.class);
		    job.setCombinerClass(IntSumReducer.class);
		    job.setReducerClass(IntSumReducer.class);
		    job.setOutputKeyClass(Text.class);
		    job.setOutputValueClass(IntWritable.class);
		    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
		    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
		    System.exit(job.waitForCompletion(true) ? 0 : 1);
		  }
		}

	Notice the package name is **org.apache.hadoop.examples** and the class name is **WordCount**. You will use the names when you submit the MapReduce job.
	
3. Save the file as **c:\Tutorials\WordCountJava\WordCount.java**. Create the folder structure if it doesn't exist.

HDInsight emulator comes with the *javac* compiler.

**To compile the MapReduce program**

1. Open command prompt.
2. Change directory to **c:\Tutorials\WordCountJava**.  This is the folder for the word counting MapReduce program.
3. Run the following command to check the existence of the two jar files:

		dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
		dir %hadoop_home%\lib\commons-cli-1.2.jar

4. Run the following command to compile the program:

		C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

	javac is located in the C:\Hadoop\java\bin folder. The last parameter is the java program that is in the current folder. The compiler creates 3 class files in the current folder.

5. Run the following command to create a jar file:

		C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

	The command creates a WordCount.jar file in the current folder.

	![HDI.EMulator.WordCount.Compile][image-emulator-wordcount-compile]
































##<a name="test"></a>Test the program on the emulator

Testing the MapReduce job on the emulator includes the following procedures:

1. upload the data files to the HDFS on the emulator
3. submit a word counting MapReduce job
4. check the job status
5. retrieve the job results

By default, HDInsight emulator uses HDFS as the default file system.  Optionally, you can configure the HDInsight emulator to use Azure Blob storage. For details, see [Get started with HDInsight Emulator][hdinsight-emulator-wasb]. 

In this tutorial, you will use the HDFS *copyFromLocal* command to upload the data files to HDFS. The next section shows you how to upload files using Azure PowerShell to Azure Blob storage. For other methods for uploading files to Azure Blob storage, see [Upload data to HDInsight][hdinsight-upload-data].

This tutorial uses the following HDFS folder structure:

<table border="1">
<tr><td>Folder</td><td>Note</td></tr>
<tr><td>/WordCount</td><td>The root folder for the word counting project. </td></tr>
<tr><td>/WordCount/Apps</td><td>The folder for the mapper and reducer executables.</td></tr>
<tr><td>/WordCount/Input</td><td>The MapReduce source file folder.</td></tr>
<tr><td>/WordCount/Output</td><td>The MapReduce output file folder.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>The job output folder.</td></tr>
</table>

This tutorial uses the .txt files located in the %hadoop_home% directory as the data files.

> [WACOM.NOTE] The Hadoop HDFS commands are case sensitive.

**To copy the data files to the emulator HDFS**

0. Open Hadoop command line from your desktop. Hadoop command line is installed by the emulator installer.
1. From the Hadoop command line window, run the following command to make a directory for the input files:

		hadoop fs -mkdir /WordCount/Input

	The path used here is the relative path. It is equivalent to the following:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Run the following command to copy some text files to the input folder on HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

	The MapReduce job will count the words in these files.

3. Run the following command to list and verify the uploaded files:

		hadoop fs -ls /WordCount/Input

	You shall see about eight .txt files.


**To run the MapReduce job using Hadoop command line**

1. Open Hadoop command line from your desktop.
2. Run the following command to delete the /WordCount/Output folder structure from HDFS.  /WordCount/Output is the output folder of the word counting MapReduce job. The MapReduce job will fail if the folder already exists. This step is necessary if this is the second time you run the job.

		hadoop fs -rmr /WordCount/Output

2. Run the following command:

		hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	If the job completes successfully, you should get an output similar to the following screenshot:

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	From the screenshot, you can see both map and reduce completed 100%. It also lists the job ID, job_201312092021_0002. The same report can be retrieved by opening the **Hadoop MapReduce status** shortcut from your desktop, and looking for the job ID.

The other option for running a MapReduce job is using Azure PowerShell. For instructions, see [Get started with the HDInsight Emulator][hdinsight-emulator].

**To display the output from HDFS**

1. Open Hadoop command line.
2. Run the following commands to display the output:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	You can append "|more" at the end of the command to get the page view. Or use the findstr command to find a string pattern:

		hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

Until now, you have developed a word counting MapReduce job, and tested it successfully on the emulator.  The next step is to deploy and run it on Azure HDInsight.






































##<a id="upload"></a>Upload data to Azure Blob storage
Azure HDInsight uses Azure Blob storage for data storage. When an HDInsight cluster is provisioned, an Azure Blob storage container is used to store the system files. You can use either this default container or a different container (either on the same Azure storage account or a different storage account located on the same data center as the cluster) for storing the data files. 

In this tutorial, you will create a container on a separate storage account for the data files and the MapReduce application. The data files are the text files in the %hadoop_home% directory on your workstation.

**To create a Blob storage and a container**

1. Open Azure PowerShell.
2. Set the variables, and then run the commands:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	The **$subscripionName** is associated with your Azure subscription. You must name the **$storageAccountName_Data** and **$containerName_Data**. For the naming restrictions, see [Naming and Referencing Containers, Blobs, and Metadata](http://msdn.microsoft.com/en-us/library/windowsazure/dd135715.aspx). 

3. Run the following command to create a storage account and a Blob storage container on the account

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. Run the following commands to verify the storage account and the container:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**To upload the data files**

1. Open Azure PowerShell.
2. Set the first three variables, and then run the commands:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
		$destFolder = "WordCount/Input"

	The **$storageAccountName_Data** and **$containerName_Data** are the same as you defined in the last procedure.

	Notice the source file folder is **c:\Hadoop\hadoop-1.1.0-SNAPSHOT**, and the destination folder is **WordCount/Input**.

3. Run the following commands to get a list of the txt files in the source file folder:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. Run the following commands to create a storage context object:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Run the following commands to copy the files:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. Run the following command to list the uploaded files:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	You should see about 8 text data files.

**To upload the word counting application**

1. Open Azure PowerShell.
2. Set the first three variables, and then run the commands:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
		$blobFolder = "WordCount/jars"

	The **$storageAccountName_Data** and **$containerName_Data** are the same as you defined in the last procedure, which means you will upload both the data file and the application to the same container on the same storage account.

	Notice the destination folder is **WordCount/jars**.

4. Run the following commands to create a storage context object:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Run the following commands to copy the applications:

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. Run the following command to list the uploaded files:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	You should see the jar file listed there.

##<a name="run"></a>Run the MapReduce job on Azure HDInsight

The following PowerShell script performs the following tasks:

1. provision an HDInsight cluster
	
	1. create a storage account that will be used as the default HDInsight cluster file system
	2. create a Blob storage container 
	3. create an HDInsight cluster

2. submit the MapReduce job

	1. create a MapReduce job definition
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
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		
		$clusterName = $serviceNameToken + "hdicluster"
		
		$storageAccountName_Default = $serviceNameToken + "hdistore"
		$containerName_Default =  $serviceNameToken + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		# Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container used as teh default file system
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
		
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
		
		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
		
		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
				
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  
		
		# Delete the default file system storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Set the first six variables in the script. **$serviceNameToken** will be used for the HDInsight cluster name, the default storage account name, and the default Blob storage container name.  Because the service name must be between 3 and 24 characters, and the script append string with up to 10 character string to the names, you  must limit the string to 14 or less characters. And the $serviceNameToken must use lower case. **$storageAccountName_Data** and **$containerName_Data** are the storage account and container that are used for storing the data files and the application. **$location** must match the data storage account location.
4. Review the rest of the variables.
5. Save the script file.
6. Open Azure PowerShell.
7. Run the following command to set the execution policy to remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. When prompted, enter username and password for the HDInsight cluster. Because you will delete the cluster at the end of the script and you will not need the username and password anymore, the username and password can be any strings. If you don't want to get prompted for the credentials, see [Working with Passwords, Secure Strings and Credentials in Windows PowerShell][powershell-PSCredential]


##<a name="retrieve"></a>Retrieve the MapReduce job output
This section shows you how to download and display the output.  For the information on displaying the results on Excel, see [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][hdinsight-ODBC], and [Connect Excel to HDInsight with Power Query][hdinsight-power-query].


**To retrieve the output**

1. Open Azure PowerShell window.
2. Change directory to **C:\Tutorials\WordCountJava**. The default Azure PowerShell folder is **C:\Windows\System32\WindowsPowerShell\v1.0**. The cmdlets you will run will download the output file to the current folder.  You don't have permissions to download the files to the system folders.
2. Run the following commands to set the values:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"
	
3. Run the following commands to create an Azure storage contect object: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. Run the following commands to download and display the output:

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

After the job is completed, you have the options to export the data to SQL Server or Azure SQL database using [Sqoop][hdinsight-use-sqoop], or to export the data to Excel.  

##<a id="nextsteps"></a>Next steps
In this tutorial, you have learned how to develop a Java MapReduce job, how to test the application on HDInsight emulator, and how to write a PowerShell script to provision an HDInsight cluster and run a MapReduce on the cluster. To learn more, see the following articles:

- [Develop C# Hadoop streaming MapReduce programs for HDInsight][hdinsight-develop-streaming]
- [Get started with Azure HDInsight][hdinsight-get-started]
- [Get started with the HDInsight Emulator][hdinsight-emulator]
- [Use Azure Blob storage with HDInsight][hdinsight-storage]
- [Administer HDInsight using PowerShell][hdinsight-admin-powershell]
- [Upload data to HDInsight][hdinsight-upload-data]
- [Use Hive with HDInsight][hdinsight-use-hive]
- [Use Pig with HDInsight][hdinsight-use-pig]
- [Connect Excel to HDInsight with Power Query][hdinsight-power-query]
- [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator/#blobstorage
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[Powershell-install-configure]: ../install-configure-powershell/



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png


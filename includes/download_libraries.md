#Download the Azure SDK for Java

##Azure Client Libraries for Java - Manual Download 

The Azure Libraries for Java are distributed under the [Apache License, Version 2.0][license]. Click [here][zip-download] for a ZIP file of the libraries and all dependencies.  This is made available by Microsoft Open Technologies, Inc.  See the license.txt and ThirdPartyNotices.txt file file inside the ZIP for license and other information.

##Azure Libraries for Java - Maven

If your project is already set up to use Maven for build, add the following dependency to your pom.xml file.

	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.windowsazure</groupId>
	    <artifactId>microsoft-azure-api-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>
	


Within the `<version>` element, replace *n.n.n* with a valid version number, which can be obtained from the [Azure Libraries Repository on Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]:  http://go.microsoft.com/fwlink/?LinkId=253887

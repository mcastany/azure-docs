<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Get started with Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

This tutorial shows you how to add a cloud-based backend service to an Android app using Azure Mobile Services. In this tutorial, you will create both a new mobile service and a simple _To do list_ app that stores app data in the new mobile service. The mobile service that you will create uses the supported .NET languages using Visual Studio for server-side business logic and to manage the mobile service. To create a mobile service that lets you write your server-side business logic in JavaScript, see the [JavaScript backend version](/en-us/documentation/articles/mobile-services-android-get-started/) of this topic.

A screenshot from the completed app is below:

![][0]

Completing this tutorial requires the [Android Developer Tools][Android SDK], which includes the Eclipse integrated development environment (IDE), Android Developer Tools (ADT) plugin, and the latest Android platform. Android 4.2 or a later version is required. 

The downloaded quickstart project contains the Mobile Services SDK for Android. While this project requires Android 4.2 or a later version, the Mobile Services SDK requires only Android 2.2 or a later version.

<div class="dev-callout"><strong>Note</strong> <p>To complete this tutorial, you need an Azure account. If you don't have an account, you can sign up for an Azure trial and get up to 10 free mobile services that you can keep using even after your trial ends. For details, see <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure Free Trial</a>.</p></div>

## <a name="create-new-service"> </a>Create a new mobile service

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Download the mobile service to your local computer

Now that you have created the mobile service, download your personalized mobile service project that you can run on your local computer or virtual machine.

1. Click the mobile service that you just created, then in the quickstart tab, click **Android** under **Choose platform** and expand **Create a new Android app**.

	![][1]  

2. If you haven't already done so, download and install [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934), or a later version.

3. Click **Download** under **Download and publish your service to the cloud**.

	This downloads the Visual Studio project that implements your mobile service. Save the compressed project file to your local computer, and make a note of where you saved it.

4. Also, download your publish profile, save the downloaded file to your local computer, and make a note of where you save it.

## Test the mobile service

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publish your mobile service

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Create a new Android app

In this section you will create a new Android app that is connected to your mobile service.

1. In the [Management Portal], click **Mobile Services**, and then click the mobile service that you just created.

2. In the quickstart tab, click **Android** under **Choose platform** and expand **Create a new Android app**. 
 
	![][2]  

3. If you haven't already done so, download and install the [Android Developer Tools][Android SDK] on your local computer or virtual machine.

4. Under **Download and run your app**, click **Download**. 

  	This downloads the project for the sample _To do list_ application that is connected to your mobile service. Save the compressed project file to your local computer, and make a note of where you save it.

## Run your Android app

The final stage of this tutorial is to build and run your new app.

1. Browse to the location where you saved the compressed project files and expand the files on your computer.

2. In Eclipse, click **File** then **Import**, expand **Android**, click **Existing Android Code into Workspace**, and then click **Next.** 

 	![][14]

3. Click **Browse**, browse to the location of the expanded project files, click **OK**, make sure that the TodoActivity project is checked, then click **Finish**. 

 	![][15]

	This imports the project files into the current workspace.

   	![][8]

4. From the **Run** menu, click **Run** to start the project in the Android emulator.

	<div class="dev-callout"><strong>Note</strong> <p>To be able to run the project in the Android emulator, you must define a least one Android Virtual Device (AVD). Use the AVD Manager to create and manage these devices.</p></div>

5. In the app, type meaningful text, such as _Complete the tutorial_, and then click **Add**.

   	![][10]

   	This sends a POST request to the new mobile service hosted in Azure. Data from the request is inserted into the TodoItem table. Items stored in the table are returned by the mobile service, and the data is displayed in the list.

	<div class="dev-callout"><strong>Note</strong> 
   	<p>You can review the code that accesses your mobile service to query and insert data, which is found in the ToDoActivity.java file.</p>
 	</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/en-us/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Next Steps
Now that you have completed the quickstart, learn how to perform additional important tasks in Mobile Services: 

* [Get started with data]
  <br/>Learn more about storing and querying data using Mobile Services.

* [Get started with authentication]
  <br/>Learn how to authenticate users of your app with an identity provider.

* [Get started with push notifications] 
  <br/>Learn how to send a very basic push notification to your app.

* [Troubleshoot a Mobile Services .NET backend]
  <br/> Learn how to diagnose and fix issues that can arise with a Mobile Services .NET backend. 

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Troubleshoot a Mobile Services .NET backend]: /en-us/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Management Portal]: https://manage.windowsazure.com/

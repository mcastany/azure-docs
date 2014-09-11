<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="" solutions="" manager="" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="" />


# Refine Mobile Services queries with paging

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>


This topic shows you how to use paging to manage the amount of data returned to your iOS app from Azure Mobile Services. In this tutorial, you will use the **Skip** and **Take** query properties on the client to request specific "pages" of data.

<div class="dev-callout"><b>Note</b>
<p>To prevent data overflow in mobile device clients, Mobile Services implements an automatic page limit, which defaults to a maximum of 50 items in a response. By specifying the page size, you can explicitly request up to 1,000 items in the response.</p>
</div>

This tutorial builds on the steps and the sample app from the previous tutorial [Get started with data]. Before you begin this tutorial, you must complete at least the first tutorial in the working with data series, [Get started with data]. 

1. In Xamarin Studio, open the project that you modified when you completed the tutorial [Get started with data].

2. Press the **Run** button to build the project and start the app, then enter text into the textbox and click the  plus (**+**) icon.

3. Repeat the previous step at least three times, so that you have more than three items stored in the TodoItem table. 

4. Open the **TodoService.cs** file, locate the **RefreshDataAsync** method, and replace the LINQ query on the <code>todoTable</code> with the following query: 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	This query returns the top three items that are not marked as completed. 

5. Rebuild and start the app. 
   
    Notice that only the first three results from the TodoItem table are displayed. 

7. Update the LINQ query in the **RefreshDataAsync** method once more to the following:

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	This time, set the **Skip** value to 3. 

   	This query skips the first three results and returns the next three after that. This is effectively the second "page" of data, where the page size is three items.

    <div class="dev-callout"><b>Note</b>
    <p>This tutorial uses a simplified scenario by setting hard-coded paging values for the <strong>Skip</strong> and <strong>Take</strong> properties. In a real-world app, you can use queries similar to the above with a pager control or comparable UI to let users navigate to previous and next pages. You can also call the <strong>IncludeTotalCount</strong> method to get the total count of all items available on the server, along with the paged data.</p>
    </div>

## <a name="next-steps"> </a>Next Steps

This concludes the set of tutorials that demonstrate the basics of working with data in Mobile Services. Consider finding out more about the following Mobile Services topic:

* [Get started with authentication]
  <br/>Learn how to authenticate users of your app with Windows Account.
 
<!--
* [Get started with push notifications] 
  <br/>Learn how to send a very basic push notification to your app.
-->

<!-- Anchors. -->

[Next Steps]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Get started with Mobile Services]: ./en-us/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Management Portal]: https://manage.windowsazure.com/

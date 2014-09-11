<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />




# Refine Mobile Services queries with paging

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS" class="current">iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
 
This topic shows you how to use paging to manage the amount of data returned to your iOS app from Azure Mobile Services. In this tutorial, you will use the **fetchLimit** and **fetchOffset** query properties on the client to request specific "pages" of data.

<div class="dev-callout"><b>Note</b>
<p>To prevent data overflow in mobile device clients, Mobile Services implements an automatic page limit, which defaults to a maximum of 50 items in a response. By specifying the page size, you can explicitly request up to 1,000 items in the response.</p>
</div>

This tutorial builds on the steps and the sample app from the previous tutorial [Get started with data]. Before you begin this tutorial, you must complete at least the first tutorial in the working with data series, [Get started with data]. 

1. In Xcode, open the project that you modified when you completed the tutorial [Get started with data].

2. Press the **Run** button (Command + R) to build the project and start the app, then enter text into the textbox and click the  plus (**+**) icon.

3. Repeat the previous step at least three times, so that you have more than three items stored in the TodoItem table. 

4. Open the QSTodoService.m file, and locate the following method:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

   	Replace the body of the entire method with the following code. 

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
    
        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];
        
        query.includeTotalCount = YES; // Request the total item count
    
        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;

        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {
            
            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
            }
        
            items = [results mutableCopy];
        
            // Let the caller know that we finished
            completion();
        }];

   	This query returns the top three items that are not marked as completed. 

5. Rebuild and start the app. 
   
    Notice that only the first three results from the TodoItem table are displayed. 

7. Update the **refreshDataOnSuccess** method once more by locating the following line of code:

        query.fetchOffset = 0;

   	This time, set the **query.fetchOffset** value to 3. 

   	This query skips the first three results and returns the next three after that. This is effectively the second "page" of data, where the page size is three items.

    <div class="dev-callout"><b>Note</b>
    <p>This tutorial uses a simplified scenario by setting hard-coded paging values for the <strong>fetchOffset</strong> and <strong>fetchLimit</strong> properties. In a real-world app, you can use queries similar to the above with a pager control or comparable UI to let users navigate to previous and next pages. You can also set  **query.includeTotalCount = YES** to get the total count of all items available on the server, along with the paged data.</p>
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
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Management Portal]: https://manage.windowsazure.com/

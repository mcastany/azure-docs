<properties pageTitle="Add paging to data (JavaScript) - Azure Mobile Services" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />


# Refine Mobile Services queries with paging

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-add-paging-data" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-add-paging-data" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-add-paging-data" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-add-paging-data" title="HTML">HTML</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data" title="Xamarin.Android">Xamarin.Android</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data"  title="JavaScript backend" class="current">JavaScript backend</a></div>


This topic shows you how to use paging to manage the amount of data returned to your Windows Store app from Azure Mobile Services. In this tutorial, you will use the **Take** and **Skip** query methods on the client to request specific "pages" of data.

>[WACOM.NOTE]To prevent data overflow in mobile device clients, Mobile Services implements an automatic page limit, which defaults to a maximum of 50 items in a response. By specifying the page size, you can explicitly request up to 1,000 items in the response.

This tutorial builds on the steps and the sample app from the previous tutorial [Get started with data]. Before you begin this tutorial, you must complete at least the first tutorial in the working with data series, [Get started with data]. 

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>Next Steps

This concludes the set of tutorials that demonstrate the basics of working with data in Mobile Services. Consider finding out more about the following Mobile Services topics:

* [Get started with authentication]
  <br/>Learn how to authenticate users of your app with Windows Account.

* [Get started with push notifications] 
  <br/>Learn how to send a very basic push notification to your app.

<!-- Anchors. -->

[Next Steps]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Management Portal]: https://manage.windowsazure.com/

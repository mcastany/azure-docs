<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Get started with authentication in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
  <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/" title=".NET backend" class="current">.NET backend</a> | <a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/"  title="JavaScript backend">JavaScript backend</a></div>

This topic shows you how to authenticate users in Azure Mobile Services from your app. In this tutorial, you add authentication to the quickstart project using an identity provider that is supported by Mobile Services. After being successfully authenticated and authorized by Mobile Services, the user ID value is displayed.

This tutorial walks you through these basic steps to enable authentication in your app:

1. [Register your app for authentication and configure Mobile Services]
2. [Restrict table permissions to authenticated users]
3. [Add authentication to the app]

This tutorial is based on the Mobile Services quickstart. You must also first complete the tutorial [Get started with Mobile Services]. 

##<a name="register"></a>Register your app for authentication and configure Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Restrict permissions to authenticated users

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>In Visual Studio or Xamarin Studio, run the client project on a device or simulator. Verify that an unhandled exception with a status code of 401 (Unauthorized) is raised after the app starts.</p>
   
   	<p>This happens because the app attempts to access Mobile Services as an unauthenticated user, but the <em>TodoItem</em> table now requires authentication.</p></li>
</ol>

Next, you will update the app to authenticate users before requesting resources from the mobile service.

##<a name="add-authentication"></a>Add authentication to the app

In this section, you will modify the app to display a login screen before displaying data. When the app starts, it will not not connect to your mobile service and will not display any data. After the first time that the user performs the refresh gesture, the login screen will appear; after successful login the list of todo items will be displayed.

1. In the client project, open the file **QSTodoService.cs** and add the following declarations to QSTodoService:

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Add a new method **Authenticate** to **QSTodoService** with the following definition:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>Note</b>
	<p>If you are using an identity provider other than a Facebook, change the value passed to <strong>LoginAsync</strong> above to one of the following: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i>, or <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. Open **QSTodoListViewController.cs**. Modify the method definition of **ViewDidLoad** to remove the call to **RefreshAsync()** near the end:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;

			todoService.BusyUpdate += (bool busy) => {
				if (busy)
					activityIndicator.StartAnimating ();
				else 
					activityIndicator.StopAnimating ();
			};

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();

			AddRefreshControl ();
		}


4. Modify the method **RefreshAsync** to authenticate and display a login screen if the **User** property is null. At the following code at the top of the method definition:

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. Press the **Run** button to build the project and start the app in the iPhone simulator. Verify that the app displays no data. 

	Perform the refresh gesture by pulling down the list of items, which will cause the login screen to appear. Once you have successfully entered valid credentials, the app will display the list of todo items and you can make updates to the data.

<!-- ## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. 
 -->
 
<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push/
[Authorize users with scripts]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript and HTML]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure Management Portal]: https://manage.windowsazure.com/

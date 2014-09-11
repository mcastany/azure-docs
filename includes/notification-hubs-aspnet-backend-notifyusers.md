## Create the WebAPI Project

The first step is to create an ASP.NET WebAPI project. This is the backend that is used to authenticate clients and to generate notifications.

> [AZURE.NOTE] **Important**: before starting this tutorial, please ensure that you have installed the latest version of the NuGet Package Manager. To check, start Visual Studio. From the **Tools** menu, click **Extensions and Updates**. Search for **NuGet Package Manager for Visual Studio 2013**, and make sure you have version 2.8.50313.46 or later. If not, please uninstall, then reinstall the NuGet Package Manager.
> 
> ![][4]

1. Start Visual Studio with elevated privileges (run as Administrator).
2. In Visual Studio or Visual Studio Express, click **File**, then click **New**, then **Project**, expand **Templates**, **Visual C#**, then click **Web** and **ASP.NET Web Application**, type the name **AppBackend**, and then click **OK**. 
	
	![][1]

2. In the **New ASP.NET Project** dialog, click **Web API**, then click **OK**.

	![][2]

3. In the **Configure Azure Site** dialog, choose a subscription, region, and database to use for this project. Then click **OK** to create the project. 

	![][5]

4. In Solution Explorer, right-click the **AppBackend** project and then click **Manage NuGet Packages**.

5. On the left-hand side, click **Online**.

6. In the **Search** box, type **servicebus**.

7. In the results list, click **Windows Azure Service Bus**, and then click **Install**. Complete the installation, then close the NuGet package manager window.

	![][14]

8. In Solution Explorer, right-click the **Models** folder, then click **Add**, then click **Class**. Name the new class **Notifications.cs**. Click **Add** to generate the class. This module represents the different secure notifications that will be sent. In a complete implementation, the notifications are stored in a database. In this case, for simplicity we store them in memory.

	![][6]

9. Add code to Notifications.cs, replacing the `Notifications` class definition with the following:

    	public class Notifications
    	{
        	public static Notifications Instance = new Notifications();
        
	        public NotificationHubClient Hub { get; set; }

	        private Notifications() {
	            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
	        }
	    }


10. Add the following `using` statement at the top of the file:

		using Microsoft.ServiceBus.Notifications;

11. In the `Notifications()` method, replace the two placeholders in the following line of code with the connection string (with full access) for your notification hub, and the hub name. You can obtain these values from the [Azure Management Portal](http://manage.windowsazure.com):

		Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");

12. In Solution Explorer, right-click the **AppBackend** project, then click **Add**, then click **Class**. Name the new class **AuthenticationTestHandler.cs**. Click **Add** to generate the class. This class is used to authenticate users using *Basic Authentication*. Note that your app can use any authentication scheme.

13. Add code to AuthenticationTestHandler.cs, replacing the `AuthenticationTestHandler` class definition with the following:

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Security Note**: The `AuthenticationTestHandler` class does not provide true authentication. It is used only to mimic basic authentication and return a principle. The user name is required to create Notification Hub registrations. The preceding implementation is not secure. You must implement a secure authentication mechanism in your production applications and services.

14. Add the following `using` statements at the top of the AuthenticationTestHandler.cs file:

		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Threading;
		using System.Text;
		using System.Security.Principal;
		using System.Net;				

14. Add the following code at the end of the `Register` method in the **App_Start/WebApiConfig.cs** class:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. In Solution Explorer, right-click the **Controllers** folder, then click **Add**, then click **Controller**. Click the **Web API 2 Controller -- Empty** item, and then click **Add**. 

	![][7]

16. Name the new class **RegisterController**, and then click **Add** again to generate the controller.

	![][8]
	  
16. Add the following code inside the `RegisterController` class definition:

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

17. Add the following `using` statements at the top of the RegisterController.cs file:

		using Microsoft.ServiceBus.Notifications;
		using SecurePush.Models;
		using System.Threading.Tasks;
		using Microsoft.ServiceBus.Messaging;
		using System.Web;
		
18. Note that in this code, we add the user tag for the user that has been authenticated by the handler. You can also add optional checks to verify that the user has rights to register for the requested tags.

19. In Solution Explorer, right-click the **Controllers** folder, then click **Add**, then click **Controller**. Click the **Web API 2 Controller -- Empty** item, and then click **Add**. Name the new class **NotificationsController**, and then click **Add** again to generate the controller. This component exposes a way for the device to retrieve the notification securely, and also it provides a way (for the purposes of this tutorial) for a user to trigger a secure push to his or her devices. Note that when sending the notification to the Notification Hub, we send a raw notification with only the ID of the notification (no actual message).

20. Add the following code inside the **NotificationsController** class definition:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

			return Request.CreateResponse(HttpStatusCode.OK);

        }

21. Add the following `using` statements at the top of the NotificationsController.cs file:

		using SecurePush.Models;
		using System.Threading.Tasks;
		using System.Web;

22. Press **F5** to run the application and to ensure the accuracy of your work so far. The app should launch a web browser and display the ASP.NET home page. 

23. Now we will deploy this app to an Azure Website in order to make it accessible from all devices. Right-click on the **AppBackend** project and select **Publish**.

24. Select Azure Website as your publish target.

	![][B15]

25. Log in with your Azure account and select an existing or new Website.

	![][B16]

26. Make a note of the **destination URL** property in the **Connection** tab. We will refer to this URL as your *backend endpoint* later in this tutorial. Click **Publish**.

	![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png

[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
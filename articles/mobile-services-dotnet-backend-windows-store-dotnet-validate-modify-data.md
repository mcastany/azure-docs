<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />




# Validate and modify data in Mobile Services using the .Net Backend.

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Windows Store C#" class="current">Windows Store C#</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone">Windows Phone</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title=".NET backend" class="current">.NET backend</a> | 
	<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="JavaScript backend">JavaScript backend</a>
</div>



This topic shows you how to use code in your .Net backend Azure Mobile Services to validate and modify data. The .Net backend service is an HTTP service built with the Web API framework and Entity Framework. If you are familiar with the `ApiController` class defined with the Web API framework, the `TableController` class provided by Mobile Services will be very intuitive. `TableController` is derived from the `ApiController` class and provides additional functionality for interfacing with a database table. It can be used to perform operations on data being inserted and updated, including validation and data modification which is demonstrated in this tutorial. 

This tutorial walks you through these basic steps:

1. [Add string length validation]
2. [Update the client to support validation]
3. [Test length validation]
4. [Add a timestamp field for CompleteDate]
5. [Update the client to display the CompleteDate]

This tutorial builds on the steps and the sample code from the previous tutorial, [Getting Started] or [Get started with data]. Before you begin this tutorial, you must first complete the [Getting Started] or [Get started with data] tutorial.  

## <a name="string-length-validation"></a>Add validation

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Update the client

Now that the mobile service is setup to validate data and send error responses for an invalid text length, you need to update your app to be able to handle error responses from validation. The error will be caught as a `MobileServiceInvalidOperationException` from the client app's call to `IMobileServiceTable<TodoItem].InsertAsync()`.

1. In the Solution Explorer window in Visual Studio, navigate to the client project and open the MainPage.xaml.cs file. Add the following **using** statements in that file:

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2. In MainPage.xaml.cs replace the existing **InsertTodoItem** method with the following code:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

   	This version of the method includes error handling for the **MobileServiceInvalidOperationException** that displays the deserialized error message from the response content in a message dialog.

## <a name="test-length-validation"></a>Test Length Validation

1. In Solution Explorer in Visual Studio, right click the client app project and then click **Set as StartUp Project**. Then press the **F5** key to start the app hosting the service locally in IIS Express.

2. Enter the text for a new todo item with a length greater than 10 characters and then click **Save**.

    ![][1]

3. You will get a message dialog similar to the following in response to the invalid text.

    ![][2]

## <a name="add-timestamp"></a>Add a timestamp field for CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]




## <a name="update-client-timestamp"></a>Update the client to display the CompleteDate

The final step is to update the client to display the new **CompleteDate** data. 


1. In Solution Explorer for Visual Studio, in the todolist client project, open the MainPage.xaml file and replace the **CheckBoxComplete** element with the definitions below. Then save the file. This changes the event handler on **CheckBoxComplete** so that we handle the `click` event. Also we add a text block next to the check box and bind it to the complete date timestamp.
	      
        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>


2. In Solution Explorer for Visual Studio, in the todolist client project, open the MainPage.xaml.cs file, replace the `CheckBoxComplete_Checked` event handler with the `CheckBoxComplete_Clicked` event handler that follows. This is so we can see the complete date after completing the item.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }


2. In the MainPage.xaml.cs file, then replace the existing **TodoItem** class with the following definition that includes the new **CompleteDate** property as a nullable type.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }
	
    >[WACOM.NOTE] The <code>DataMemberAttribute</code> tells the client to map the new <code>CompleteDate</code> property in the app to the <code>CompleteDate</code> column defined in the TodoItem table. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database.
    

	


4. In MainPage.xaml.cs, remove or comment out the `.Where` clause function in the existing **RefreshTodoItems** method so that completed todoitems are included in the results.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();


5. In MainPage.xaml.cs, update the **UpdateCheckedTodoItem** method as follows so that the items are refreshed after an update and completed items are not removed from the list. Then save the file.	

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }


6. In the Solution Explorer windows of Visual Studio, right click the **Solution** and click **Rebuild Solution** to rebuild both the client and the .NET backend service. Verify both project build without errors.

    ![][3]
	
7. Press the **F5** key to run the client app and service locally. Add some new items and click to mark some items complete to see the **CompleteDate** timestamp being updated.

    ![][4]

8. In Solution Explorer for Visual Studio, right click the todolist service project and click **Publish**. Publish your .NET backend service to Microsoft Azure using your publishing setting file that you downloaded from the Azure portal.

9. Update the App.xaml.cs file for the client project by uncommenting the connection to the mobile service address. Test the app against the .NET Backend hosted in your Azure account.




## <a name="next-steps"> </a>Next steps

Now that you have completed this tutorial, consider continuing on with the final tutorial in the data series: [Refine queries with paging].

Server scripts are also used when authorizing users and for sending push notifications. For more information see the following tutorials:

* [Service-side authorization of users]
  <br/>Learn how to filter data based on the ID of an authenticated user.

* [Get started with push notifications] 
  <br/>Learn how to send a very basic push notification to your app.

* [Mobile Services .NET How-to Conceptual Reference]
  <br/>Learn more about how to use Mobile Services with .NET.

<!-- Anchors. -->
[Add string length validation]: #string-length-validation
[Update the client to support validation]: #update-client-validation
[Test length validation]: #test-length-validation
[Add a timestamp field for CompleteDate]: #add-timestamp
[Update the client to display the CompleteDate]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Service-side authorization of users]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Getting Started]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Management Portal]: https://manage.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library

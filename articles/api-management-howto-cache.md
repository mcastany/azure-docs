<properties pageTitle="How to cache operation results in Azure API Management" metaKeywords="" description="Learn how to improve the latency, bandwidth consumption, and web service load for API Management service calls." metaCanonical="" services="" documentationCenter="API Management" title="How to cache operation results in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# How to cache operation results in Azure API Management

Operations in API Management (Preview) can be configured for response caching. Response caching can significantly reduce API latency, bandwidth consumption, and web service load for data that does not change frequently.

In this tutorial you will review the caching settings and policies for one of the sample Echo Api operations, and call the operation in the developer portal to see the caching in operation.

## In this topic

-   [Configure an operation for caching][]
-   [Review the caching policies][]
-   [Call an operation and test the caching][]
-   [Next steps][]

## <a name="configure-caching"> </a>Configure an operation for caching

In this step, you will review the caching settings of the **GET Resource (cached)** operation of the sample Echo Api.

>Each API Management service instance comes pre-configured with an Echo API that can be used to experiment with and learn about API Management. For more information, see [Get started with Azure API Management][].

To get started, click **Management Console** in the Azure Portal for your API Management service. This takes you to the API Management administrative portal.

![API Management console][api-management-management-console]

>If you have not yet created an API Management service instance, see [Create an API Management service instance][] in the [Get started with Azure API Management][] tutorial.

Click **APIs** from the **API Management** menu on the left, and click **Echo API**.

![Echo API][api-management-echo-api]

Select the **Operations** tab, and click the **GET Resource (cached)** operation from the **Operations** list.

![Echo API operations][api-management-echo-api-operations]

Select the **Caching** tab to view the caching settings for this operation.

![Caching tab][api-management-caching-tab]

To enable caching for an operation, check the **Enable** checkbox. In this example caching is enabled.

Each operation response is keyed based on the values in the **Vary by query string parameters** and **Vary by headers** fields. If you want to cache multiple responses based on query string parameters or headers, you can configure them in these two fields.

**Duration** specifies the expiration interval of the cached responses. In this example the interval is **3600** seconds, which is equivalent to one hour.

Using the caching configuration in this example, the first request to the **GET Resource (cached)** operation will return a response from the back-end service. This response will be cached, keyed by the specified headers and query string parameters. Subsequent calls to the operation, with matching parameters, will have the cached response returned, until the cache duration interval has expired.

## <a name="caching-policies"> </a>Review the caching policies

When caching settings are configured for an operation on the **Caching** tab, caching policies are added for the operation. These policies can be viewed and edited in the policy editor.

Click **Policies** from the **API Management** menu on the left, and select **Echo API / GET Resource (cached)** from the **Operation** drop-down.

![Policy scope operation][api-management-operation-dropdown]

This displays the policies for this operation in the policy editor.

![API Management policy editor][api-management-policy-editor]

The policy definition for this operation includes the policies that define the caching configuration that were reviewed using the **Caching** tab in the previous step.

	<policies>
		<inbound>
			<base />
			<cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
				<vary-by-header>Accept</vary-by-header>
				<vary-by-header>Accept-Charset</vary-by-header>
			</cache-lookup>
			<rewrite-uri template="/resource" />
		</inbound>
		<outbound>
			<base />
			<cache-store caching-mode="cache-on" duration="3600" />
		</outbound>
	</policies>

>Changes made to the caching policies in the policy editor will reflect in the **Caching** tab of an operation, and vice-versa.

## <a name="test-operation"> </a>Call an operation and test the caching

To see the caching in action, we can call the operation from the developer portal. Click **Developer portal** in the top right menu.

![Developer portal][api-management-developer-portal-menu]

Click **APIs** in the top menu and select **Echo API**.

![Echo API][api-management-apis-echo-api]

>If you have only one API configured or visible to your account, then clicking APIs takes you directly to the operations for that API.

Select the **GET Resource (cached)** operation and click **Open Console**.

![Open console][api-management-open-console]

The console allows you to invoke operations directly from the developer portal.

![Console][api-management-console]

Keep the default values for **param1** and **param2**.

Select the desired key from the **subscription-key** drop-down. If your account has only one subscription it will already be selected.

Enter **sampleheader:value1** in the **Request headers** text box.

Click **HTTP Get** and make a note of the response headers.

Enter **sampleheader:value2** in the **Request headers** text box and click **HTTP Get**.

Note that the value of **sampleheader** is still **value1** in the response. Try some different values and note that the cached response from the first call is returned.

Enter **25** into the **param2** field, and click **HTTP Get**.

Note that the value of **sampleheader** in the response is now **value2**. Because the operation results are keyed by query string, the previous cached response was not returned.

## <a name="next-steps"> </a>Next steps

-	Check out the other topics in the [Get started with advanced API configuration][] tutorial.
-	For more information on caching policies, see [Caching policies][] in the [API Management policy reference][].

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ../api-management-howto-add-operations
[How to add and publish a product]: ../api-management-howto-add-products
[Monitoring and analytics]: ../api-management-monitoring
[Add APIs to a product]: ../api-management-howto-add-products/#add-apis
[Publish a product]: ../api-management-howto-add-products/#publish-product
[Get started with Azure API Management]: ../api-management-get-started
[Get started with advanced API configuration]: ../api-management-get-started-advanced
[API Management policy reference]: ../api-management-policy-reference
[Caching policies]: ../api-management-policy-reference/#caching-policies
[Create an API Management service instance]: ../api-management-get-started/#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps


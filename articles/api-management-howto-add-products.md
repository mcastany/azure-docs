<properties pageTitle="How create and publish a product in Azure API Management" metaKeywords="" description="Learn how to create and publish products in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How create and publish a product in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# How create and publish a product in Azure API Management

In Azure API Management (Preview), a product contains one or more APIs as well as a usage quota and the terms of use. Once a product is published, developers can subscribe to the product and begin to use the product's APIs. The topic provides a guide to creating a product, adding an API, and publishing it for developers.

## In this topic

-   [Create a product][]
-   [Add APIs to a product][]
-   [Add descriptive information to a product][]
-   [Publish a product][]
-   [Make a product visible to developers][]
-   [View subscribers to a product][]
-   [Next steps][]

## <a name="create-product"> </a>Create a product

Operations are added and configured to an API in the management console. To access the management console, click **Management Console** in the Azure Portal for your API Management service.

>If you have not yet created an API Management service instance, see [Create an API Management service instance][] in the [Get started with Azure API Management][] tutorial.

![API Management console][api-management-management-console]

Click on **Products** in the menu on the left to display the **Products** page, and click **Add Product**.

![Products][api-management-products]

![New product][api-management-add-new-product]

Enter a descriptive name for the product in the **Name** field and a description of the product in the **Description** field.

Check **Require subscription approval** if you want an administrator to review and accept or reject subscription attempts to this product. If the box is unchecked, subscription attempts will be auto-approved. For more information on subscriptions, see [View subscribers to a product][].

![Products][api-management-products-page]

>By default new products are unpublished, and are visible only to the  **Administrators** group.

To configure a product, click on the product name in the **Products** tab.

## <a name="add-apis"> </a>Add APIs to a product

The **Products** page contains four links for configuration: **Summary**, **Settings**, **Visiblity**, and **Developers**. The **Summary** tab is where you can and APIs and publish or unpublish a product.

![Summary][api-management-new-product-summary]

Before publishing your product you need to add one or more APIs. To do this, click **Add API to product**.

![Add APIs][api-management-add-apis-to-product]

Select the desired APIs and click **Save**.

## <a name="add-description"> </a>Add descriptive information to a product

The **Settings** tab allows you to provide detailed information about the product such as its purpose, the APIs it provides access to, and other useful information. The content is targeted at the developers that will be calling the API and can be written in plain text or HTML markup.

![Product settings][api-management-product-settings]

Select **Require subscription approval** if you want to manually approve all product subscription requests. By default all product subscriptions are granted automatically.

Optionally fill in the **Terms of use** field describing the terms of use for the product which subscribers must accept in order to use the product.

## <a name="publish-product"> </a>Publish a product

Before the APIs in a product can be called, the product must be published. On the **Summary** tab for the product, click **Publish**, and then click **Yes, publish it** to confirm. To make a previously published product private, click **Unpublish**.

![Publish product][api-management-publish-product]

## <a name="make-visible"> </a>Make a product visible to developers

The **Visibility** tab allows you to choose which roles are able to see the product on the developer portal and subscribe to the product.

![Product visibility][api-management-product-visiblity]

To enable or disable visibility of a product for the developers in a group, check or uncheck the check box beside the group and then click **Save**.

>For more information, see [How to create and use groups to manage developer accounts in Azure API Management][].

## <a name="view-subscribers"> </a>View subscribers to a product

The **Developers** tab lists the developers who have subscribed to the product. The details and settings for each developer can be viewed by clicking on the developer's name. In this example no developers have yet subscribed to the product.

![Developers][api-management-developer-list]

## <a name="next-steps"> </a>Next steps

Once the desired APIs are added and the product published, developers can subscribe to the product and begin to call the APIs. For a tutorial that demonstrates these items as well as advanced product configuration see [How create and configure advanced product settings in Azure API Management][].


[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: ../api-management-howto-add-operations
[How to create and publish a product]: ../api-management-howto-add-products
[Get started with Azure API Management]: ../api-management-get-started
[Create an API Management service instance]: ../api-management-get-started/#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: ../api-management-howto-create-groups
[How create and configure advanced product settings in Azure API Management]: ../api-management-howto-product-with-rules
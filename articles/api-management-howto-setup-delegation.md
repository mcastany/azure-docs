<properties pageTitle="How to delegate user registration and product subscription" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba" />

# How to delegate user registration and product subscription

Delegation allows you to use your existing website for handling developer sign-in/sign-up and subscription to products as opposed to using the built-in functionality in the developer portal. This enables your website to own the user data and perform the validation of these steps in a custom way.


## In this topic

-   [Delegating developer sign-in and sign-up][]
-   [Delegating product subscription][]

## <a name="delegate-signin-up"> </a>Delegating developer sign-in and sign-up

To delegate developer sign-in and sign-up to your existing website you will need to create a special delegation endpoint on your site that acts as the entry-point for any such request initiated from the API Management developer portal.

The final workflow will be as follows:

1. Developer clicks on the sign-in or sign-up link at the API Management developer portal
2. Browser is redirected to the delegation endpoint
3. Delegation endpoint in return redirects to or presents UI asking user to sign-in or sign-up
4. On success, the user is redirected back to the API Management developer portal page they started from


To begin, let's first set-up API Management to route requests via your delegation endpoint. In the API Management publisher portal, under the **Developer Portal** heading in the menu on the left, click on **Delegation** and then click **Delegate sign-in and sign-up**.

![Delegation page][api-management-delegation-signin-up]

* Decide what the URL of your special delegation endpoint will be and enter it in the **Delegation endpoint URL** field.

* Within the **Delegation authentication key** field enter a secret that will be used to compute a signature provided to you for verification to ensure that the request is indeed coming from Azure API Management.

Now you need to create the **delegation endpoint**. It has to perform a number of actions:

1. Receive a request in the following form:

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&redirectUrl={URL of source page}&salt={string}&sid={string}*

	Query parameters for the sign-in / sign-up case:
	- **operation**: identifies what type of delegation request it is - it can only be "SignIn" in this case
	- **redirectUrl**: the URL of the page where the user clicked on a sign-in or sign-up link
	- **salt**: a special salt string used for computing a security hash
	- **sig**: a computed security hash to be used for comparison to your own computed hash

2. Verify that the request is coming from Azure API Management (optional, but highly recommended for security)

	* Compute an HMAC-SHA512 hash of a string based on the **redirectUrl** and **salt** query parameters:
           > HMAC(**redirectUrl** + '\n' + **salt**)
		 
	* Compare the above-computed hash to the value of the **sig** query parameter. If the two hashes match, move on to the next step, otherwise deny the request.

2. Verify that you are receiving a request for sign-in/sign-up: the **operation** query parameter will be set to "**SignIn**".

3. Present the user with UI to sign-in or sign-up

4. If the user is signing-up you have to create a corresponding account for them in API Management. [Create a user] with the API Management REST API. When doing so, ensure that you set the user ID to the same it is in your user store or to an ID that you can keep track of.

5. When the user is successfully authenticated:

	* [request a single-sign-on (SSO) token] via the API Management REST API

	* append a returnUrl query parameter to the SSO URL you have received from the API call above:
		> e.g. https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

	* redirect the user to the above produced URL


## <a name="delegate-product-subscription"> </a>Delegating product subscription

Delegating product subscription works similarly to delegating user sign-in/-up. The final workflow would be as follows:

1. Developer selects a product in the API Management developer portal and clicks on the Subscribe button
2. Browser is redirected to the delegation endpoint
3. Delegation endpoint performs required product subscription steps - this is up to you and may entail redirecting to another page to request billing information, asking additional questions, or simply storing the information and not requiring any user action
4. On success, the user is redirected back to the API Management developer portal page they started from

To enable the functionality, on the **Delegation** page click **Delegate product subscription**.

Then ensure the delegation endpoint performs the following actions:


1. Receive a request in the following form:

	> *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sid={string}*

	Query parameters for the product subscription case:
	- **operation**: identifies what type of delegation request it is. For product subscription requests the valid options are:
		- "Subscribe": a request to subscribe the user to a given product with provided ID (see below)
		- "Unsubscribe": a request to unsubscribe a user from a product
		- "Renew": a requst to renew a subscription (e.g. that may be expiring)
	- **productId**: the ID of the product the user requested to subscribe to
	- **userId**: the ID of the user for whom the request is made
	- **salt**: a special salt string used for computing a security hash
	- **sig**: a computed security hash to be used for comparison to your own computed hash


2. Verify that the request is coming from Azure API Management (optional, but highly recommended for security)

	* Compute an HMAC-SHA512 of a string based on the **productId**, **userId** and **salt** query parameters:
		> HMAC(**productId** + '\n' + **userId** + '\n' + **salt**)
		 
	* Compare the above-computed hash to the value of the **sig** query parameter. If the two hashes match, move on to the next step, otherwise deny the request.
	
3. Perform any product subscription processing based on the type of operation requested in **operation** - e.g. billing, further questions, etc.

4. On successfully subscribing the user to the product on your side, subscribe the user to the API Management product by [calling the REST API for product subscription].

5. Redirect the user back to the **redirectUrl** provided when receiving the request.



[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[request a single-sign-on (SSO) token]: http://go.microsoft.com/fwlink/?LinkId=507409
[create a user]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[calling the REST API for product subscription]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
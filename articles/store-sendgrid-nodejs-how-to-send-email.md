<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="" />





# How to Send Email Using SendGrid from Node.js

This guide demonstrates how to perform common programming tasks with the
SendGrid email service on Azure. The samples are written using
the Node.js API. The scenarios covered include **constructing email**,
**sending email**, **adding attachments**, **using filters**, and
**updating properties**. For more information on SendGrid and sending
email, see the [Next Steps][] section.

## Table of Contents

* [What is the SendGrid Email Service?][]   
* [Create a SendGrid Account][]   
* [Reference the SendGrid Node.js Module][]   
* [How to: Create an Email][]   
* [How to: Send an Email][]   
* [How to: Add an Attachment][]   
* [How to: Use Filters to Enable Footers, Tracking, and Analytics][]   
* [How to: Update Email Properties][]   
* [How to: Use Additional SendGrid Services][]   
* [Next Steps][1]

## <a name="whatis"> </a>What is the SendGrid Email Service?

SendGrid is a [cloud-based email service] that provides reliable
[transactional email delivery], scalability, and real-time analytics along with flexible APIs
that make custom integration easy. Common SendGrid usage scenarios
include:

-   Automatically sending receipts to customers
-   Administering distribution lists for sending customers monthly
    e-fliers and special offers
-   Collecting real-time metrics for things like blocked e-mail, and
    customer responsiveness
-   Generating reports to help identify trends
-   Forwarding customer inquiries
-   Email notifications from your application

For more information, see [http://sendgrid.com](http://sendgrid.com).

## <a name="createaccount"> </a>Create a SendGrid Account

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Reference the SendGrid Node.js Module

The SendGrid module for Node.js can be installed through the node
package manager (npm) by using the following command:

    npm install sendgrid

After installation, you can require the module in your application by
using the following code:

    var SendGrid = require('sendgrid')

The SendGrid module exports the **SendGrid** and **Email** functions.
**SendGrid** is responsible for sending email through either SMTP or Web
API, while **Email** encapsulates an email message.

## <a name="createemail"> </a>How to: Create an Email

Creating an email message using the SendGrid module involves first
creating an email message using the Email function, and then sending it
using the SendGrid function. The following is an example of creating a
new message using the Email function:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

You can also specify an HTML message for clients that support it by
setting the html property. For example:

    html: This is a sample <b>HTML<b> email message.

Setting both the text and html properties provides graceful fallback to
text content for clients that cannot support HTML messages.

For more information on all properties supported by the Email function,
see [sendgrid-nodejs][].

## <a name="sendemail"> </a>How to: Send an Email

After creating an email message using the Email function, you can send
it using either SMTP or the Web API provided by SendGrid. For details
about the benefits and differences of each API, see [SMTP vs. Web API][]
in the SendGrid documentation.

Using either the SMTP API or Web API requires that you first initialize
the SendGrid function using the user and key of your SendGrid account as
follows:

    var sender = new SendGrid('user','key');

The message can now be sent using either SMTP or the Web API. The calls
are virtually identical, passing the email message and an optional
callback function; The callback is used to determine the success or
failure of the operation. The following examples show how to send a
message using both SMTP and the Web API.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### Web API

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

<div class="dev-callout">
<strong>Note</strong>
<p>While the above examples show passing in an email object and
callback function, you can also directly invoke the send and smtp
functions by directly specifying email properties. For example:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>How to: Add an Attachment

Attachments can be added to a message by specifying the file name(s) and
path(s) in the **files** property. The following example demonstrates
sending an attachment:

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

<div class="dev-callout">
<strong>Note</strong>
<p>When using the <strong>files</strong> property, the file must be accessible
through <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. If the file you wish to attach is hosted in Azure Storage, such as in a Blob container, you must first copy the file to local storage or to an Azure drive before it can be sent as an attachment using the <strong>files</strong> property.</p>
</div>

## <a name="usefilters"> </a>How to: Use Filters to Enable Footers, Tracking, and Twitter

SendGrid provides additional email functionality through the use of
filters. These are settings that can be added to an email message to
enable specific functionality such as enabling click tracking, Google
analytics, subscription tracking, and so on. For a full list of filters,
see [Filter Settings][].

Filters can be applied to a message by using the **filters** property.
Each filter is specified by a hash containing filter-specific settings.
The following examples demonstrate the footer, click tracking, and
Twitter filters:

### Footer

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### Click Tracking

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

## <a name="updateproperties"> </a>How to: Update Email Properties

Some email properties can be overwritten using **set*Property*** or
appended using **add*Property***. For example, you can add additional
recipients by using

    email.addTo('jeff@contoso.com');

or set a filter by using

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

For more information, see [sendgrid-nodejs][].

## <a name="useservices"> </a>How to: Use Additional SendGrid Services

SendGrid offers web-based APIs that you can use to leverage additional
SendGrid functionality from your Azure application. For full
details, see the [SendGrid API documentation][].

## <a name="nextsteps"> </a>Next Steps

Now that you've learned the basics of the SendGrid Email service, follow
these links to learn more.

-   SendGrid Node.js module repository: [sendgrid-nodejs][]
-   SendGrid API documentation:
    <http://docs.sendgrid.com/documentation/api/>
-   SendGrid special offer for Azure customers:
    [http://sendgrid.com/azure.html](http://sendgrid.com/azure.html)

  [Next Steps]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid Node.js Module]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Update Email Properties]: #updateproperties
  [How to: Use Additional SendGrid Services]: #useservices
  [1]: #nextsteps

  
  
  [special offer]: http://www.sendgrid.com/azure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP vs. Web API]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  
  [Filter Settings]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [SendGrid API documentation]: http://docs.sendgrid.com/documentation/api/
  [cloud-based email service]: http://sendgrid.com/solutions
  [transactional email delivery]: http://sendgrid.com/transactional-email

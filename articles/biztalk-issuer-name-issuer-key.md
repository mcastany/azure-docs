<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Issuer name and issuer key" pageTitle="Issuer Name and Issuer Key in BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Learn how to retrieve Issuer Name and Issuer Key for either Service Bus or Access Control (ACS) in BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia" />




# BizTalk Services: Issuer Name and Issuer Key

Azure BizTalk Services uses the Service Bus Issuer Name and Issuer Key, and the Access Control Issuer Name and Issuer Key. Specifically:


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Task</strong></td>
<td><strong>Which Issuer Name and Issuer Key</strong></td>
</tr>
<tr>
<td>Deploying your application from Visual Studio</td>
<td>Access Control Issuer Name and Issuer Key</td>
</tr>
<tr>
<td>Configuring the Azure BizTalk Services Portal</td>
<td>Access Control Issuer Name and Issuer Key</td>
</tr>
<tr>
<td>Creating LOB Relays with the BizTalk Adapter Services in Visual Studio</td>
<td>Service Bus Issuer Name and Issuer Key</td>
</tr>
</table>

This topic lists the steps to retrieve the Issuer Name and Issuer Key. 

## Access Control Issuer Name and Issuer Key
The Access Control Issuer Name and Issuer Key are used by the following:

- Your Azure BizTalk Service application created in Visual Studio: To successfully deploy your BizTalk Service application in Visual Studio to Azure, you enter the Access Control Issuer Name and Issuer Key. 
- The Azure BizTalk Services  Portal: The first time you log in to the BizTalk Services Portal, you enter your BizTalk Service name as the service provider, enter the Access Control Issuer Name, and the Access Control Issuer Key.

### To retrieve the Access Control Issuer Name and Issuer Key

1. Log in to the [Azure Management Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. In the left navigation pane, select **BizTalk Services**.
3. Select your BizTalk Service. 
4. Select **Connection Information** in the task bar. The Access Control Namespace, Default Issuer (Issuer Name), and Default Key (Issuer Key) are listed and can be copied and pasted.<br/><br/>
To summarize:<br/>
Issuer Name = Default Issuer<br/>
Issuer Key = Default Key


You can also click **Open ACS Management Portal** to retrieve the Access Control values:

1. Log in to the [Azure Management Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. In the left navigation pane, select **BizTalk Services**.
3. Select your BizTalk Service.
4. Select the Connection Information button and select **Open ACS Management Portal**.
5. In the Portal under **Service Settings**, click **Service Identities**. This displays your Service Identity, which is your Access Control Issuer Name value. Click your Service Identity link to see the Password, which is your Issuer Key value. Their values can be copied.<br/><br/>
For example, in **Service Identities**, you see "owner". "Owner" is your Access Control Issuer Name. When you click the "owner" link, you see the **Password**. When you click the "Password" link, you see the value. This Password value is your Access Control Issuer Key. <br/><br/>
To summarize:<br/>
Issuer Name = Service Identity name<br/>
Issuer Key = Password value

In the left navigation pane, you can also select **Active Directory** to retrieve the Access Control values. 

<div class="dev-callout"> 
<b>Important</b> 
<p>When an Access Control Namespace is created using <strong>Active Directory</strong>, a Service Identity is <strong>not</strong> automatically created. When you provision a BizTalk Service, an Access Control Namespace, Service Identity named "owner" (Issuer Name), Password (Issuer Key), and Symmetric Key are automatically created.</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">How to: Use ACS Management Service to Configure Service Identities</a> provides more information on Access Control Service Identities.</p>
</div>


## Service Bus Issuer Name and Issuer Key
Service Bus Issuer Name and Issuer Key are used by BizTalk Adapter Services. In your BizTalk Services project in Visual Studio, you use the BizTalk Adapter Services to connect to an on-premise Line-of-Business (LOB) system. To connect, you create the LOB Relay and enter your LOB system details. When doing this, you also enter the Service Bus Issuer Name and Issuer Key.

### To retrieve the Service Bus Issuer Name and Issuer Key

1. Log in to the [Azure Management Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. In the left navigation pane, click **Service Bus**.
3. Click your namespace. In the task bar, click **Connection Information**. This displays the **Default Issuer** (Issuer Name) and **Default Key** (Issuer Key). Their values can be copied.<br/><br/>
To summarize:<br/>
Issuer Name = Default Issuer<br/>
Issuer Key = Default Key

## Next
Additional Azure BizTalk Services topics:

-  [Installing the Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutorials: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [How do I Start Using the Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## See Also
-  [How to: Use ACS Management Service to Configure Service Identities](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk Services: Developer, Basic, Standard and Premium Editions Chart](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk Services: Provisioning Using Azure Management Portal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Provisioning Status Chart](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services: Dashboard, Monitor and Scale tabs](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services: Backup and Restore](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services: Throttling](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

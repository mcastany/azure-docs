<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />
 
# Troubleshooting and Q&A - Application Insights on Microsoft Azure Preview

+ [I don't see any option to Add Application Insights to my project in Visual Studio](#q01)
+ [My new web project was created, but adding Application Insights failed.](#q02)
+ [I added Application Insights successfully and ran my app, but I've never seen data in the portal.](#q03)
+ [I see no data under Usage Analytics](#q04)
+ [I'm looking at the Microsoft Azure Preview start board. How do I find my data in Application Insights?](#q05)
+ [On the Microsoft Azure Preview home screen, does that map show the status of my application?](#q06)
+ [When I use add Application Insights to my application and open the Application Insights portal, it all looks completely different from your screenshots.](#q07)
+ [Can I use Application Insights to monitor an intranet web server?](#q08)
+ [How do I get data for Windows Phone or Windows Store?](#q09)
+ [How can I see the events and page views that I logged in my code?](#q10)
+ [How come there are two versions of Application Insights?](#q11)
+ [How do I get back all the features I had in the Visual Studio Online version of Application Insights?](#q13)
+ [What does Application Insights modify in my project?](#q14)
+ [How do I find my results in Application Insights?](#q15)
+ [Learn more](#next)



## <a name="q01"></a>I don't see any option to Add Application Insights to my project in Visual Studio

+ Make sure you have [Visual Studio Update 3](http://go.microsoft.com/fwlink/?LinkId=397827). It comes pre-installed with Application Insights Tools, which you should be able to see in Extension Manager.
+ Application Insights on Microsoft Azure Preview is currently available only for ASP.NET web projects in C# or Visual Basic.
+ If you have an existing project, go to Solution Explorer and make sure you click the web project (not another project or the solution). You should see a menu item 'Add Application Insights Telemetry to Project'.
+ If you are creating a new project, in Visual Studio, open File > New Project, and select {Visual C#|Visual Basic} > Web > ASP.NET Web Application. There should be an option to Add Application Insights to Project.

## <a name="q02"></a>My new web project was created, but adding Application Insights failed.

This can happen if communication with the Application Insights portal failed, or if there is some problem with your account.

+ Check that you provided login credentials for the right Azure account. The Microsoft Azure credentials, which you see in the New Project dialog, can be different from the Visual Studio Online credentials that you see at the top right of Visual Studio.
+ Wait a while and then [add Application Insights to your existing project][start].
+ Go to your Microsoft Azure account settings and check for restrictions. See if you can manually add an Application Insights application.


## <a name="q03"></a>I added Application Insights successfully and ran my app, but I've never seen data in the portal.

+ You have to close and open any blade where you are waiting for data. In the current version, the content of a blade doesn't refresh automatically.
+ In the Microsoft Azure start board, look at the service status map. If there are some alert indications, wait until they have returned to OK and then close and re-open your Application Insights application blade.
+ In your web server's firewall, you might have to open port 443 for outgoing traffic. 

## <a name="q04"></a>I see no data under Usage Analytics

+ The data comes from scripts in the web pages. If you added Application Insights to an existing web project, [you have to add the scripts by hand][start].


## <a name="q05"></a>I'm looking at the Microsoft Azure Preview start board. How do I find my data in Application Insights?

Either:

* Choose Browse, Application Insights, your project name. If you don't have any projects there, you need to [add Application Insights to your web project in Visual Studio][start].

* In Visual Studio Solution Explorer, right-click your web project and choose Open Application Insights Portal.

## <a name="q06"></a>On the Microsoft Azure Preview home screen, does that map show the status of my application?

No! It shows the status of the Azure service. To see your web test results, choose Browse > Application Insights > (your application) and then look at the web test results. 


## <a name="q07"></a>When I use add Application Insights to my application and open the Application Insights portal, it all looks completely different from your screenshots.

You might be using the older version of the Application Insights Tools, which connect to the Visual Studio Online version.

The help pages you're looking at refer to Application Insights for Microsoft Azure Preview, which comes already switched on in Visual Studio Update 3. 

## <a name="q08"></a>Can I use Application Insights to monitor an intranet web server?

Yes, you can monitor health and usage if your server can send data to the public internet.

But if you want to run web tests for your service, it must be accessible from the public internet.

## <a name="q09"></a>How do I get data for Windows Phone or Windows Store?

Not yet supported in the Microsoft Azure version. Use the [Visual Studio Online version][older].


## <a name="q10"></a>How can I see the events and page views that I logged in my code?

We don't support that yet in the Microsoft Azure version. Coming soon. For the present, you could try the [older version][older].


## <a name="q11"></a>How come there are two versions of Application Insights?

The older portal is part of Visual Studio Online. We'll make no more substantial changes to that version. If you have an older version of the Application Insights tools for Visual Studio, they connect to the Visual Studio Online portal.

Visual Studio Update 3 comes with a pre-installed version of the new Application Insights tools. They connect to the new Application Insights portal, which is a component of Microsoft Azure Preview. We're currently porting Application Insights to this new environment. The work isn't complete yet.

## <a name="q13"></a>How do I get back to all the features I had in the Visual Studio Online version of Application Insights?

1. Go into Visual Studio's Extension Manager. 
2. Uninstall Application Insights Tools.
3. Run [the installer for the older version of the tools](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) and read its [get-started guide][older].

## <a name="q14"></a>What does Application Insights modify in my project?

The details depend on the type of project. For a web application:+


+ Adds these files to your project:

 + ApplicationInsights.config. 
 + ai.js


+ Installs these NuGet packages:

 -  *Application Insights API* - the core API

 -  *Application Insights API for Web Applications* - used to send telemetry from the server

 -  *Application Insights API for JavaScript Applications* - used to send telemetry from the client

    The packages include these assemblies:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Inserts items into:

 - Web.config

 - packages.config

+ (New projects only - if you [add Application Insights to an existing project][start], you have to do this manually.) Inserts snippets into the client and server code to initialize them with the Application Insights resource ID. For example, in an MVC app, code is inserted into the master page Views/Shared/_Layout.cshtml


## <a name="q15"></a>How do I find my results in Application Insights?
1. Open Microsoft Azure:
 - In Visual Studio, right-click your web application project and choose **Open Azure Preview Portal**.
 - Or in a web browser you can open your account in Microsoft Azure Preview.

2. Choose Browse, Application Insights, then select your project.



## <a name="next"></a>Learn more

* [Application Insights][root]
* [Add Application Insights to your project][start]
* [Monitor a live web server now][redfield]
* [Explore metrics in Application Insights][explore]
* [Diagnostic log search][diagnostic]
* [Availability tracking with web tests][availability]
* [Usage tracking with events and metrics][usage]
* [Q & A and troubleshooting][qna]


<!--Link references-->


[root]: ../app-insights-get-started/
[start]: ../app-insights-monitor-application-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[explore]: ../app-insights-explore-metrics/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-track-usage-custom-events-metrics/
[qna]: ../app-insights-troubleshoot-faq/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs


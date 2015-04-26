<properties 
	pageTitle="" 
	description="Using mobile services in Cordova projects" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/11/2014" 
	ms.author="patshea"/>

### Использование мобильных служб с проектами Cordova в предварительной версии Visual Studio 2015

Для использования мобильных служб Azure с проектами Cordova в предварительной версии Visual Studio 2015 необходимо применить следующее временное решение.

1. В проекте Cordova предварительной версии Visual Studio 2015 откройте Config.xml и на вкладке **Подключаемые модули** включите подключаемый модуль **мобильных служб Windows Azure**.<br/>
![][1]

2. В index.html удалите строки, которые ссылаются на **MobileServices.Web-1.2.2.min.js**.<br/>

<PRE style="prettyprint">
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;meta charset="utf-8" /&gt;
    &lt;title&gt;MyCordovaApp&gt;/title&gt;

    &lt;!--MyCordovaApp references --&gt;
    &lt;link href="css/index.css" rel="stylesheet" /&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;p&gt;Hello world!&lt/p&gt;

    &lt;!--Cordova reference, this is added to your app when it's build. --&gt;
    &lt;script src="cordova.js"&gt;&lt;/script&gt;
    &lt;script src="scripts/platformOverrides.js"&gt;&lt;/script&gt;

    &lt;script src="scripts/index.js"&gt;&lt;/script&gt;

    &lt;!-- yourservicename references --&gt;
    <span style="background-color:yellow">&lt;script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js"&gt;&lt/script&gt;</span>
    &lt;script src="/services/mobileservices/settings/yourservicename.js"&gt;&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</PRE>

3. Откройте {имя_вашей_службы}.js в папках services -> mobileServices -> settings и замените существующий фрагмент кода следующим:

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png 


<!--HONumber=42-->

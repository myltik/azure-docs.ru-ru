<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [Что произошло?](/documentation/articles/vs-mobile-services-cordova-what-happened/)

###<span id="whathappened">Что произошло с моим проектом?</span>

#####Добавленные ссылки

Включен подключаемый модуль клиента мобильной службы Azure, содержащий все гибридные приложения для множественных устройств.
  
#####Значения строк подключения для мобильных служб

В папке services\mobileServices\settings был создан новый файл JavaScript (.js) с объектом **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы. Этот файл содержит код инициализации объекта клиента мобильной службы, аналогичный следующему.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)

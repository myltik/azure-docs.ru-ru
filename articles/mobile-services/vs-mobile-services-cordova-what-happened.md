<properties 
	pageTitle="" 
	description="Описание того, что произошло с вашим проектом мобильных служб Azure в Cordova" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Что произошло с моим проектом?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">Что произошло с моим проектом?</span>

#####Добавленные ссылки

Включен подключаемый модуль клиента мобильной службы Azure, содержащий все гибридные приложения для множественных устройств.
  
#####Значения строк подключения для мобильных служб

В папке `services\mobileServices\settings` создан новый файл JavaScript (JS) с объектом **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы. Файл содержит код инициализации объекта клиента мобильной службы, похожий на следующий:

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)
<!--HONumber=54--> 
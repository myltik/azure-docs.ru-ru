<properties 
	pageTitle="Что произошло с моим проектом Cordova (подключенные службы Visual Studio) | Microsoft Azure" 
	description="Описывается, что произошло с вашим проектом Cordova для Azure после добавления мобильных служб Azure с помощью подключенных служб Visual Studio. " 
	services="mobile-services" 
	documentationCenter="na" 
	authors="mlhoop" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2016" 
	ms.author="mlearned"/>

# Что произошло с моим проектом Cordova для Azure после добавления мобильных служб Azure с помощью подключенных служб Visual Studio?

##Добавленные ссылки

Включен подключаемый модуль клиента мобильной службы Azure, содержащий все гибридные приложения для множественных устройств.
  
##Значения строк подключения для мобильных служб

В папке `services\mobileServices\settings` создан новый файл JavaScript (JS) с объектом **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы. Файл содержит код инициализации объекта клиента мобильной службы, похожий на следующий:

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Дополнительные сведения о мобильных службах](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->
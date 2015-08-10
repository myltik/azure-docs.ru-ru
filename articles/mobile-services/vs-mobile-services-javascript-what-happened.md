<properties 
	pageTitle="" 
	description="Описание того, что произошло с вашим проектом мобильных служб Azure в Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea123"/>

# Что произошло с моим проектом?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###Что произошло с моим проектом?</id>

#####Добавлен пакет NuGet

Пакет NuGet **WindowsAzure.MobileServices.WinJS** установлен вместе с библиотекой мобильной службы Azure в файл `js\MobileServices.js`.
  
#####Значения строк подключения для мобильных служб 

В папке `services\mobileServices\settings` создан новый файл JavaScript (JS) с объектом **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы.


#####Ссылки добавлены в файл default.html

Ссылки на `MobileServices.js` и файл параметров добавлены в `default.html`.


#####Добавлены файлы подключенных служб

В папку службы добавлены файлы конфигурации подключенных служб.



 

<!---HONumber=July15_HO5-->
<properties 
	pageTitle="Что происходит при добавлении мобильных служб в приложение JavaScript с помощью подключенных служб Visual Studio | Microsoft Azure" 
	description="Описание того, что произошло с вашим проектом мобильных служб Azure в Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mlhoop" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="mlearned"/>

# Что происходит с моим проектом Javascript при добавлении мобильных служб Azure с помощью подключенных служб Visual Studio?

##Добавлен пакет NuGet

Пакет NuGet **WindowsAzure.MobileServices.WinJS** установлен вместе с библиотекой мобильной службы Azure в файл `js\MobileServices.js`.
  
##Значения строк подключения для мобильных служб 

В папке `services\mobileServices\settings` создан новый файл JavaScript (JS) с объектом **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы.

##Ссылки добавлены в файл default.html

Ссылки на `MobileServices.js` и файл параметров добавлены в `default.html`.

##Добавлены файлы подключенных служб

В папку службы добавлены файлы конфигурации подключенных служб.



 

<!---HONumber=AcomDC_0128_2016-->
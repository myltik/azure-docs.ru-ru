<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](vs-mobile-services-dotnet-getting-started.md)
> - [Что произошло](vs-mobile-services-dotnet-what-happened.md)

###<span id="whathappened">Что произошло с моим проектом?</span>

#####Добавленные ссылки

В проект добавлен пакет NuGet мобильных служб Azure. В результате в проект добавлены следующие ссылки на .NET:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Значения строк подключения для мобильных служб

В файле App.xaml.cs был создан объект **MobileServiceClient**, содержащий ключ и URL-адрес выбранного приложения мобильной службы. 

[Дополнительную информацию о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->

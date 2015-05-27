<properties 
	pageTitle="" 
	description="Описание произошедшего в вашем проекте .NET мобильных служб Azure в Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Что произошло с моим проектом?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

###<span id="whathappened">Что произошло с моим проектом?</span>

#####Добавленные ссылки

В проект добавлен пакет NuGet мобильных служб Azure. В результате в проект добавлены следующие ссылки на .NET:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Значения строк подключения для мобильных служб

В файле App.xaml.cs создан объект **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы.

[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)
<!--HONumber=54-->
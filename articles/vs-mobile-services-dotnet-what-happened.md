       <properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [Что произошло?](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

###<span id="whathappened">Что произошло с моим проектом?</span>

#####Добавленные ссылки

В проект добавлен пакет NuGet мобильных служб Azure. В результате в проект были добавлены следующие ссылки .NET:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Значения строк подключения для мобильных служб

В файле App.xaml.cs создан объект **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы. 

[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)

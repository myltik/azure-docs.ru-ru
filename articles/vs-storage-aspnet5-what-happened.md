<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Что случилось](/documentation/articles/vs-storage-aspnet5-what-happened/)

### <span id="whathappened">Что произошло с моим проектом?</span>

##### Добавленные ссылки

Пакет NuGet хранилища Azure был добавлен в проект Visual Studio.  
Этот пакет добавляет следующие ссылки .NET:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

Также добавлен пакет NuGet **Microsoft.Framework.ConfigurationModel.Json**.

##### Добавлена строка подключения к хранилищу Azure 
В файле config.json проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. в разделе [ASP.NET 5](http://www.asp.net/vnext).
<!--HONumber=42-->

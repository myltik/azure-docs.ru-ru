<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Описание произошедшего при создании хранилища Azure в проекте Visual Studio ASP.NET" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Что произошло с моим проектом?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

###<span id="whathappened">Что произошло с моим проектом?</span>

##### Добавленные ссылки

Пакет NuGet хранилища Azure был добавлен в проект Visual Studio. Этот пакет добавляет следующие ссылки .NET:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Добавлена строка подключения к хранилищу Azure 
В файле web.config проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. в разделе [ASP.NET](http://www.asp.net).

<!---HONumber=July15_HO2-->
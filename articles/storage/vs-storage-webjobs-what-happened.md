<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Описание произошедшего при создании хранилища Azure в проекте веб-задания Azure в Visual Studio" 
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
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# Что произошло с моим проектом?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

###<span id="whathappened">Что произошло с моим проектом?</span>

##### Добавленные ссылки

Пакет NuGet хранилища Azure добавлен в проект Visual Studio или обновлен в этом проекте. Этот пакет добавляет следующие ссылки .NET:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.ConfigurationManager`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Добавлена строка подключения к хранилищу Azure 
В файле App.config проекта элементы `AzureWebJobsStorage` и `AzureWebJobsDashboard` обновлены строкой подключения и ключом выбранной учетной записи хранения.

Дополнительные сведения см. в разделе [Рекомендуемые ресурсы для веб-заданий Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=July15_HO5-->
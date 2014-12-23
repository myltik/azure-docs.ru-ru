<properties title="Getting Started with Azure Storage" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Что произошло?](/documentation/articles/vs-storage-cloud-services-what-happened/)

###<span id="whathappened">Что произошло с моим проектом?</span>

###### Добавленные ссылки

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

######Добавлена строка подключения к хранилищу Azure 
Созданы элементы с ключом и строкой подключения выбранной учетной записи хранилища. Внесены изменения в следующие файлы:

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`


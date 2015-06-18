<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Описание произошедшего при создании хранилища Azure в проекте облачной службы Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](vs-storage-cloud-services-getting-started-blobs.md)
> - [Что произошло?](vs-storage-cloud-services-what-happened.md)

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


<!--HONumber=46--> 
 
<properties
    pageTitle="Что произошло с моим проектом облачных служб? | Microsoft Azure | Подключенные службы Visual Studio"
	description="Сведения о том, что происходит в проекте облачных служб после подключения к учетной записи хранения Azure с помощью подключенных служб Visual Studio"
    services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# Что произошло с моим проектом облачных служб (подключенными к службе хранилища Azure службами Visual Studio)?

## Добавлены ссылки

Пакет NuGet хранилища Azure был добавлен в проект Visual Studio. Этот пакет добавляет следующие ссылки .NET:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Добавлена строка подключения к хранилищу Azure
Созданы элементы с ключом и строкой подключения выбранной учетной записи хранилища. Внесены изменения в следующие файлы:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**

<!---HONumber=AcomDC_0224_2016-->
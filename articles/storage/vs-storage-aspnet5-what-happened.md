<properties
	pageTitle="Что произошло с моим проектом ASP.NET 5 (подключенными службами Visual Studio) | Служба хранилища Microsoft Azure"
	description="Сведения о том, что происходит после подключения к учетной записи хранения Azure в проекте Visual Studio ASP.NET 5 с помощью подключенных служб Visual Studio"
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

# Что произошло с моим проектом ASP.NET 5 (подключенными к службе хранилища Azure службами Visual Studio)?

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

Кроме того, добавлен пакет NuGet **Microsoft.Framework.Configuration.Json**.

## Добавлена строка подключения к хранилищу Azure
В файле config.json проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительную информацию см. на веб-сайте, посвященном [ASP.NET 5](http://www.asp.net/vnext).

<!---HONumber=AcomDC_0224_2016-->
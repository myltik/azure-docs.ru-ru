<properties
	pageTitle="Что произошло с моим проектом веб-заданий (подключенными к службе хранилища Azure службами Visual Studio)? | Microsoft Azure"
	description="Сведения о том, что происходит в проекте веб-заданий Azure после подключения к учетной записи хранения с помощью подключенных служб Visual Studio"
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
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Что произошло с моим проектом веб-заданий (подключенными к службе хранилища Azure службами Visual Studio)?

## Добавленные ссылки

Пакет NuGet хранилища Azure добавлен в проект Visual Studio или обновлен в этом проекте. Этот пакет добавляет следующие ссылки .NET:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Добавлена строка подключения к хранилищу Azure
В файле App.config вашего проекта в записи **AzureWebJobsStorage** и **AzureWebJobsDashboard** добавлена строка подключения и ключ выбранной учетной записи хранения.

Дополнительные сведения см. в статье [Документация по веб-заданиям Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0511_2016-->
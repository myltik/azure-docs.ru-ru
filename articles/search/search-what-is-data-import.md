---
title: "Отправка данных в службу поиска Azure | Документация Майкрософт"
description: "Сведения об отправке данных в индекс в службе поиска Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/09/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 72cc0d9ff35ff656a6134b52812b64c39a295a6f
ms.openlocfilehash: 53786d60d9971d9f976bf0f3ef4e40346c3101f4


---
# <a name="upload-data-to-azure-search"></a>Отправка данных в службу поиска Azure
> [!div class="op_single_selector"]
> * [Обзор](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

## <a name="data-upload-models-in-azure-search"></a>Модели отправки данных в службе поиска Azure
Есть два способа заполнения данными индекса в службе поиска Azure. Первый вариант — вручную передать данные в индекс с помощью [REST API](search-import-data-rest-api.md) или [пакета SDK для .NET](search-import-data-dotnet.md) службы поиска Azure. Второй вариант — [указать поддерживаемый источник данных](search-indexer-overview.md) для индекса службы поиска Azure, после чего данные будут автоматически извлечены в службу.

В этом руководстве представлены инструкции только о том, как использовать модель передачи данных (которая поддерживается только [REST API](search-import-data-rest-api.md) и [пакетом SDK для .NET](search-import-data-dotnet.md)), но ниже приводятся и некоторые сведения о модели извлечения.

## <a name="push-data-to-an-index"></a>Передача данных в индекс
Этот подход заключается в программной отправке данных в службу поиска Azure, что сделает их доступными для поиска. Для приложений с очень высокими требованиями к задержкам при обработке (например, если операции поиска должны выполняться синхронно с работой динамической учетной базы данных) модель передачи будет единственным вариантом.

Передать данные в индекс можно с использованием интерфейса [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) или [пакета SDK для .NET](search-import-data-dotnet.md). Инструментов для передачи данных через портал пока не существует.

Передача является более гибким подходом, чем извлечение, поскольку документы можно передавать по одному или пакетами (не более 1000 документов и не более 16 МБ в каждом пакете). Модель передачи позволяет отправлять документы в службу поиска Azure независимо от расположения данных.

## <a name="pull-data-into-an-index"></a>Извлечение данных в индекс
Модель извлечения выполняет поиск поддерживаемого источника данных и автоматически отправляет их в индекс службы поиска Azure. Отслеживая процессы изменения и удаления существующих документов, помимо распознания новых документов, индексаторы исключают необходимость в активном управлении данными в индексе.

Служба поиска Azure реализует эту возможность с помощью *индексаторов*, которые в настоящее время поддерживают [хранилище BLOB-объектов (предварительная версия)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [базу данных SQL Azure и SQL Server на виртуальных машинах Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).

Функция индексатора предоставляется на [портале Azure](search-import-data-portal.md), а также в интерфейсе [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations).




<!--HONumber=Dec16_HO2-->



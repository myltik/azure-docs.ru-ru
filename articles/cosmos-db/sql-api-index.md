---
title: "Azure Cosmos DB: Статьи об SQL API | Документы Microsoft"
description: "Список всех статей, специально для создания баз данных документов с помощью API SQL в базе данных Azure Cosmos."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: mimig
ms.openlocfilehash: 8de2a541f307adfed1125dbfc25825b3e28d9c48
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-sql-api-documentation"></a>Azure Cosmos DB: Документация по SQL API

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

В этой статье ссылки на все содержимое базы данных Azure Cosmos определенных API-интерфейса SQL.

Эти статьи неприменимы к API Graph, API таблиц или API MongoDB. 

## <a name="introduction-and-concepts"></a>Основные понятия

Это необходимо прочитать разделы и ресурсы начинать изучение API-Интерфейсы SQL для Azure Cosmos DB.

- [Введение в SQL API](sql-api-introduction.md)
- [Модель ресурсов базы данных](sql-api-resources.md)
- Веб-сайт [Query Playground](https://www.documentdb.com/sql/demo)
- Памятка по [грамматике SQL](sql-api-sql-query-cheat-sheet.md)

## <a name="quickstarts"></a>Быстрое начало работы

Краткое руководство — это самый быстрый способ создать работающее приложение с помощью Azure Cosmos DB. В каждом кратком руководстве описывается, как использовать портал Azure на основе пользовательского интерфейса и привычный язык программирования для создания решения базы данных с помощью Azure Cosmos DB. Клонируемые веб-приложения из GitHub доступны для каждого быстрого запуска. 

- [.NET + портал Azure + веб-приложения](create-sql-api-dotnet.md)
- [Java + портал Azure + веб-приложения](create-sql-api-java.md)
- [Node.js + портал Azure + веб-приложения](create-sql-api-nodejs.md)
- [Python + портал Azure + веб-приложения](create-sql-api-python.md)
- [Xamarin + портал Azure + веб-приложения](create-sql-api-xamarin-dotnet.md)

## <a name="tutorials"></a>Учебники

Эти руководства описывают примеры использования на более глубоком уровне. В этих руководствах вы будете создавать приложения с нуля, а затем копировать и вставлять в них код. Вы также узнаете, как импортировать и запрашивать данные, а также глобально распределять базы данных.

### <a name="create-a-web-app"></a>Создание веб-приложения

- [.NET](sql-api-dotnet-application.md)
- [Node.js](sql-api-nodejs-application.md) 
- [Java](sql-api-java-application.md)
- [Python](sql-api-python-application.md)

### <a name="create-a-console-app"></a>Создание консольного приложения

- [.NET](sql-api-get-started.md)
- [.NET Core](sql-api-dotnetcore-get-started.md) 
- [Java](sql-api-java-get-started.md) 
- [Node.js](sql-api-nodejs-get-started.md) 
- [C++](sql-api-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>Создание мобильного приложения

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>Работа с данными

- [Импорт данных](import-data.md)
- [Запрос](tutorial-query-sql-api.md)
- [Глобальное распределение данных](tutorial-global-distribution-sql-api.md)

### <a name="work-with-azure-functions"></a>Работа с решением "Функции Azure"

- [Создание триггера Azure Cosmos DB](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [Использование Azure Cosmos DB с входными и выходными привязками](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>Руководство разработчика

- [SQL-запросы](sql-api-sql-query.md)
- [Секционирование](sql-api-partition-data.md)
- [Программирование на стороне сервера: хранимые процедуры, триггеры баз данных и определяемые пользователем функции](programming.md)
- [Тестирование производительности](performance-testing.md)
- [Советы по улучшению производительности](performance-tips.md)
- [Архитектура с несколькими источниками](multi-region-writers.md)
- [Дата и время](working-with-dates.md)
- [Моделирование данных документа](modeling-data.md) 

## <a name="sdks"></a>Пакеты SDK

Azure Cosmos DB предоставляет различные пакеты SDK для разработки приложений на стороне клиента.

- [Java](sql-api-sdk-java.md)
- [.NET](sql-api-sdk-dotnet.md)
- [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
- [.NET Core](sql-api-sdk-dotnet-core.md)
- [Node.js](sql-api-sdk-node.md)
- [Python](sql-api-sdk-python.md)

## <a name="reference"></a>Справочные материалы

- [REST](/rest/api/documentdb/)
- [Поставщик ресурсов REST](/rest/api/documentdbresourceprovider/)
- [Справочник по SQL-запросам](sql-api-sql-query-reference.md)
- [Справочник по решению "Функции Azure"](../azure-functions/functions-bindings-documentdb.md)

## <a name="samples"></a>Примеры

Эти образцы страниц предоставляют ссылки на образцы кода и API ссылаются на содержимое для выполнения распространенных задач SQL API.

- [.NET](sql-api-dotnet-samples.md)
- [Node.js](sql-api-nodejs-samples.md)
- [Python](sql-api-python-samples.md) 

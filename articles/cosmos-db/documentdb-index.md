---
title: "Azure Cosmos DB. Статьи об API DocumentDB | Документация Майкрософт"
description: "Список всех статей, посвященных созданию баз данных документов с помощью API DocumentDB в Azure Cosmos DB."
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
ms.openlocfilehash: ce24e445177b7e2e499309f016478030efa6bfe8
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2017
---
# <a name="azure-cosmos-db-documentdb-api-documentation"></a>Azure Cosmos DB. Документация по API DocumentDB

В этой статье приведены ссылки на все содержимое Azure Cosmos DB, связанное с API DocumentDB.

Эти статьи неприменимы к API Graph, API таблиц или API MongoDB. 

## <a name="introduction-and-concepts"></a>Основные понятия

Эти разделы и ресурсы обязательны для ознакомления при изучении API DocumentDB для Azure Cosmos DB.

- [Общие сведения об API DocumentDB](documentdb-introduction.md)
- [Модель ресурсов базы данных](documentdb-resources.md)
- Веб-сайт [Query Playground](https://www.documentdb.com/sql/demo)
- Памятка по [грамматике SQL](documentdb-sql-query-cheat-sheet.md)

## <a name="quickstarts"></a>Быстрое начало работы

Краткое руководство — это самый быстрый способ создать работающее приложение с помощью Azure Cosmos DB. В каждом кратком руководстве описывается, как использовать портал Azure на основе пользовательского интерфейса и привычный язык программирования для создания решения базы данных с помощью Azure Cosmos DB. Клонируемые веб-приложения из области GitHub доступны для каждого краткого руководства. 

- [.NET + портал Azure + веб-приложения](create-documentdb-dotnet.md)
- [Java + портал Azure + веб-приложения](create-documentdb-java.md)
- [Node.js + портал Azure + веб-приложения](create-documentdb-nodejs.md)
- [Python + портал Azure + веб-приложения](create-documentdb-python.md)
- [Xamarin + портал Azure + веб-приложения](create-documentdb-xamarin-dotnet.md)

## <a name="tutorials"></a>Учебники

Эти руководства описывают примеры использования на более глубоком уровне. В этих руководствах вы будете создавать приложения с нуля, а затем копировать и вставлять в них код. Вы также узнаете, как импортировать и запрашивать данные, а также глобально распределять базы данных.

### <a name="create-a-web-app"></a>Создание веб-приложения

- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md) 
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

### <a name="create-a-console-app"></a>Создание консольного приложения

- [.NET](documentdb-get-started.md)
- [.NET Core](documentdb-dotnetcore-get-started.md) 
- [Java](documentdb-java-get-started.md) 
- [Node.js](documentdb-nodejs-get-started.md) 
- [C++](documentdb-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>Создание мобильного приложения

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>Работа с данными

- [Импорт данных](import-data.md)
- [Запрос](tutorial-query-documentdb.md)
- [Глобальное распределение данных](tutorial-global-distribution-documentdb.md)

### <a name="work-with-azure-functions"></a>Работа с решением "Функции Azure"

- [Создание триггера Azure Cosmos DB](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [Использование Azure Cosmos DB с входными и выходными привязками](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>Руководство разработчика

- [SQL-запросы](documentdb-sql-query.md)
- [Секционирование](documentdb-partition-data.md)
- [Программирование на стороне сервера: хранимые процедуры, триггеры баз данных и определяемые пользователем функции](programming.md)
- [Тестирование производительности](performance-testing.md)
- [Советы по улучшению производительности](performance-tips.md)
- [Архитектура с несколькими источниками](multi-region-writers.md)
- [Дата и время](working-with-dates.md)
- [Моделирование данных документа](modeling-data.md) 

## <a name="sdks"></a>Пакеты SDK

Azure Cosmos DB предоставляет различные пакеты SDK для разработки приложений на стороне клиента.

- [Java](documentdb-sdk-java.md)
- [.NET](documentdb-sdk-dotnet.md)
- [Веб-канал изменений в .NET](documentdb-sdk-dotnet-changefeed.md)
- [.NET Core](documentdb-sdk-dotnet-core.md)
- [Node.js](documentdb-sdk-node.md)
- [Python](documentdb-sdk-python.md)

## <a name="reference"></a>Справочные материалы

- [REST](/rest/api/documentdb/)
- [Поставщик ресурсов REST](/rest/api/documentdbresourceprovider/)
- [Справочник по SQL-запросам](documentdb-sql-query-reference.md)
- [Справочник по решению "Функции Azure"](../azure-functions/functions-bindings-documentdb.md)

## <a name="samples"></a>Примеры

На следующих страницах предоставлены ссылки на примеры кода и справочные материалы по API для выполнения распространенных задач в API DocumentDB.

- [.NET](documentdb-dotnet-samples.md)
- [Node.js](documentdb-nodejs-samples.md)
- [Python](documentdb-python-samples.md) 
---
title: Azure Cosmos DB. Статьи об API SQL | Документация Майкрософт
description: Список всех статей, посвященных созданию баз данных документов с помощью API SQL в Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: sngun
ms.openlocfilehash: 5e6f277a3c020334ba26a0b1a2681cbbdd672f45
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30902633"
---
# <a name="azure-cosmos-db-sql-api-documentation"></a>Azure Cosmos DB. Документация по API SQL

В этой статье приведены ссылки на все содержимое Azure Cosmos DB, связанное с API SQL.

Эти статьи неприменимы к API Graph, API таблиц или API MongoDB. 

## <a name="introduction-and-concepts"></a>Основные понятия

Эти разделы и ресурсы обязательны для ознакомления при изучении API SQL для Azure Cosmos DB.

- [Общие сведения об API SQL](sql-api-introduction.md)
- [Модель ресурсов базы данных](sql-api-resources.md)
- Веб-сайт [Query Playground](https://www.documentdb.com/sql/demo)
- Памятка по [грамматике SQL](query-cheat-sheet.md)

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

- [REST](/rest/api/cosmos-db/)
- [Поставщик ресурсов REST](/rest/api/cosmos-db-resource-provider/)
- [Справочник по SQL-запросам](sql-api-sql-query-reference.md)
- [Справочник по решению "Функции Azure"](../azure-functions/functions-bindings-cosmosdb.md)

## <a name="samples"></a>Примеры

На следующих страницах предоставлены ссылки на примеры кода и справочные материалы по API для выполнения распространенных задач в API SQL.

- [.NET](sql-api-dotnet-samples.md)
- [Node.js](sql-api-nodejs-samples.md)
- [Python](sql-api-python-samples.md) 

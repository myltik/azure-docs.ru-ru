---
title: Пакет SDK для .NET и ресурсы API Graph для базы данных Azure Cosmos DB | Документация Майкрософт
description: Узнайте об API Graph для базы данных Azure Cosmos DB, включая даты выпуска и вывода из эксплуатации, а также изменения, внесенные в каждую версию.
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 41608e76216f63ccf3165cc3959787c661d71fa0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>API Graph .NET для базы данных Azure Cosmos DB. Скачивание и заметки о выпуске

|   |   |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Документация по API**|[Справочная документация по API .NET](https://aka.ms/acdbgraphapiref)|
|**Краткое руководство**|[База данных Azure Cosmos DB. Создание приложения графа с помощью .NET и API Graph](create-graph-dotnet.md)|
|**Руководство**|[База данных Azure Cosmos DB. Создание контейнера с помощью API Graph](tutorial-develop-graph-dotnet.md)|
|**Текущая поддерживаемая платформа**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Заметки о выпуске

Рекомендуем использовать драйвер Gremlin открытым кодом для .NET, Gremlin.Net. Инструкции по началу работы см. в статье [Azure Cosmos DB. Создание приложения .NET Framework или Core с помощью API Graph](create-graph-dotnet.md).

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-preview

#### <a name="bug-fixes"></a>Исправления ошибок
* Исправлена необязательная загрузка `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Новые возможности
* Переключение Microsoft.Azure.Graphs на целевую платформу AnyCPU.
* Удаление сборки Mono из пакета манифеста `net461`.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>Новые возможности
* Добавлена поддержка параметра `.netstandard 1.6`.
  * Требуется `Microsoft.Azure.DocumentDB.Core >= 1.5.1`.
* Добавлено новое средство анализа `gremlin-groovy`, которое заменяет существующее средство. Это средство анализа поддерживает подмножество синтаксиса Tinkerpop `gremlin-groovy` и включает следующие возможности:
  * В два раза улучшена производительность.
  * Устранены проблемы, связанные с экранированием символов в строках, неправильной обработкой литеральных значений и других несоответствий в старом средстве анализа.
* Добавлены оптимизации для обходов с граничными предикатами.
  *  Это улучшение повлияет на прыжки обходов с фильтрами, например `g.V('1').outE().has('name', 'marko').inV()`.
* Добавлены оптимизации для обходов с шагом `limit()`.

#### <a name="breaking-changes"></a>Критические изменения
* Удалена поддержка .NET Framework 4.5.1.

* Новое средство анализа соответствует грамматике `gremlin-groovy`. В результате некоторые выражения, которые ранее работали, являются неоднозначными для нового средства анализа. Например:
  * `in` и `as` являются зарезервированными ключевыми словами в `gremlin-groovy`, поэтому эти действия должны быть дополнены `.in()` или `.as()`, чтобы избежать синтаксических ошибок. Например, `g.V().repeat(in()).times(2)` -> _вызывает синтаксическую ошибку_.  
 `g.V().repeat(__.in()).times(2)` -> _работает правильно_.

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* Обзор предварительного выпуска.

## <a name="release--retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии. 

Любые запросы к базе данных Azure Cosmos DB с помощью выведенного из эксплуатации пакета SDK будут отклонены службой.

<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [0.3.1-Preview](#0.3.1-preview) |17 октября 2017 г. |--- |
| [0.3.0-preview](#0.3.0-preview) |2 октября 2017 г. |--- |
| [0.2.4-preview](#0.2.4-preview) |4 августа 2017 г. |--- |
| [0.2.2-preview](#0.2.2-preview) |23 июня 2017 г. |--- |
| [0.2.1-preview](#0.2.2-preview) |8 июня 2017 г. |--- |
| [0.2.0-preview](#0.2.2-preview) |10 мая 2017 г. |--- |
| [0.1.0-preview](#0.1.0-preview) |8 мая 2017 г. |--- |

## <a name="see-also"></a>См. также
Дополнительные сведения об API Graph для базы данных Azure Cosmos DB см. в [этой статье](graph-introduction.md). 

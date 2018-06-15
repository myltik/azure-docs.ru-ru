---
title: Как выполнять запросы к данным графа в базе данных Azure Cosmos DB | Документация Майкрософт
description: Узнайте, как выполнять запросы к данным графа в базе данных Azure Cosmos DB
services: cosmos-db
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: dfaf3fe1427c9069939f5421c6a87efb584ebe48
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763210"
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Руководство. Выполнение запросов к API Graph в Azure Cosmos DB Graph с использованием Gremlin

[API Graph](graph-introduction.md) базы данных Azure Cosmos DB позволяет выполнять запросы [Gremlin](https://github.com/tinkerpop/gremlin/wiki). В этой статье приведены примеры документов и запросов, которые помогут вам начать работу. Подробная справка по Gremlin содержится в [этой статье](gremlin-support.md).

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Выполнение запросов к данным с помощью Gremlin.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы такие запросы работали, у вас должна быть учетная запись базы данных Azure Cosmos DB и данные графа в контейнере. У вас их нет? Завершите [краткое руководство](create-graph-dotnet.md) или [руководство разработчика](tutorial-query-graph.md), чтобы создать учетную запись и заполнить базу данных. Вы можете выполнить следующие запросы с помощью [библиотеки графов .NET базы данных Azure Cosmos DB](graph-sdk-dotnet.md), [консоли Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) или предпочитаемого драйвера Gremlin.

## <a name="count-vertices-in-the-graph"></a>Подсчет вершин в графе

В следующем фрагменте показано, как подсчитать количество вершин в графе:

```
g.V().count()
```

## <a name="filters"></a>Фильтры

Вы можете выполнять фильтрацию с помощью шагов Gremlin `has` и `hasLabel`, а также объединять их с помощью операторов `and`, `or` и `not` для создания более сложных фильтров. База данных Azure Cosmos DB предоставляет схемонезависимое индексирование всех свойств в ваших вершинах и степенях для быстрого выполнения запросов:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Проекция

Вы можете проецировать некоторые свойства в результатах запроса с помощью шага `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Поиск связанных ребер и вершин

Пока мы видели только операторы запросов, которые работают в любой базе данных. Графы способны быстро и эффективно выполнять операции обхода, когда вам необходимо перейти к связанным ребрам и вершинам. Давайте найдем всех друзей Томаса. Мы сделаем это с помощью шага Gremlin `outE`, чтобы найти все исходящие от Томаса ребра, а затем переместимся к вершинам исходящих ребер с помощью шага Gremlin `inV`:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Следующий запрос выполняет два прыжка, чтобы найти всех друзей друзей Томаса, вызвав `outE` и `inV` два раза. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Вы можете создавать более сложные запросы и внедрять эффективную логику обхода графа с помощью Gremlin, включая сочетание выражений фильтров, выполнение цикла с помощью шага `loop` и реализацию условной навигации с помощью шага `choose`. Дополнительные сведения о возможностях, допустимых благодаря поддержке Gremlin, см. в [этой статье](gremlin-support.md).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * Вы научились выполнять запросы с помощью Graph. 

Теперь вы можете приступать к следующему руководству, чтобы узнать, как глобально распределять данные.

> [!div class="nextstepaction"]
> [Глобальное распределение данных](tutorial-global-distribution-graph.md)

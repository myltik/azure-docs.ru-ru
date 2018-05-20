---
title: Как выполнять запросы к данным графа в базе данных Azure Cosmos DB | Документация Майкрософт
description: Узнайте, как выполнять запросы к данным графа в базе данных Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 449821d6121f8fec40b151ae06f687586133c3d1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
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

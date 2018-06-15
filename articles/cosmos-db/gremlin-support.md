---
title: Поддержка языка Gremlin в базе данных Azure Cosmos DB | Документация Майкрософт
description: Узнайте о языке Gremlin из Apache TinkerPop. Узнайте, какие функции и действия доступны в Azure Cosmos DB
services: cosmos-db
author: LuisBosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: overview
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: c675f37e50f5b8a259048d9a92fcdbe5b947068c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797623"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Поддержка графа Gremlin в базе данных Azure Cosmos DB
Azure Cosmos DB поддерживает [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) — язык обхода графов [Apache TinkerpPop](http://tinkerpop.apache.org), предназначенный для создания сущностей графа и выполнения операций запросов графов Graph API. Вы можете использовать язык Gremlin, чтобы создать сущности графа (вершины и ребра), изменить свойства в этих сущностях, выполнить запросы и обходы графа, а также удалить сущности. 

База данных Azure Cosmos DB предоставляет возможности корпоративного уровня для баз данных графа. Эти возможности включают глобальное распределение, независимое масштабирование хранилища и пропускной способности, прогнозируемую задержку операций менее 10 миллисекунд, автоматическое индексирование, соглашения об уровнях обслуживания, доступность для операций чтения для учетных записей баз данных, используемых в пределах двух и более регионов Azure. Так как база данных Azure Cosmos DB поддерживает TinkerPop и Gremlin, вы можете легко перемещать приложения, написанные с помощью другой базы данных графа, без необходимости изменять код. Кроме того, благодаря поддержке языка Gremlin база данных Azure Cosmos DB быстро и эффективно интегрируется с платформами аналитики, совместимыми с TinkerPop, например с [Apache Spark GraphX](http://spark.apache.org/graphx/). 

Эта статья содержит краткое руководство по языку Gremlin с перечислением функций Gremlin и действий, поддерживаемых в API Graph.

## <a name="gremlin-by-example"></a>Обзор Gremlin на примере
Давайте рассмотрим пример графа, чтобы лучше понять, как в Gremlin могут выражаться запросы. На рисунке ниже в форме графа показано бизнес-приложение, управляющее данными о пользователях, их интересах и устройствах.  

![Пример базы данных, в котором показаны люди, их интересы и устройства](./media/gremlin-support/sample-graph.png) 

Этот граф содержит следующие типы вершин (которые в Gremlin называются метками):

- Люди. На графе представлено трое: Робин (Robin), Томас (Thomas) и Бен (Ben).
- Интересы. Их интересы. В этом случае — футбол.
- Устройства. Устройства, которые эти люди используют.
- Операционные системы. Операционные системы, под управлением которых работают устройства.

Мы представим взаимосвязи между этими сущностями, используя следующие типы ребер или метки:

- Знакомства. Например, "Томас знает Робин".
- Интересы. Чтобы представить интересы людей на графе. Например, "Бен интересуется футболом".
- Использующаяся операционная система. Ноутбук работает под управлением Windows.
- Используемые устройства. Чтобы представить используемое устройство. Например, Робин использует телефон Motorola с серийным номером 77.

Теперь выполним некоторые операции с этим графом с помощью [консоли Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Вы также можете выполнить эти операции с помощью драйверов Gremlin на платформе по вашему усмотрению — Java, Node.js, Python или .NET.  Прежде чем перейти к поддерживаемым базой данных Azure Cosmos DB функциям, давайте рассмотрим несколько примеров, чтобы ознакомиться с синтаксисом.

Сначала рассмотрим операции CRUD (создание, чтение, обновление и удаление). Следующая инструкция Gremlin вставляет вершину "Thomas" в граф:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Затем инструкция Gremlin вставляет ребро "знакомства" между вершинами Thomas и Robin.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Следующий запрос возвращает вершины "людей" в порядке по убыванию их имен:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Если часть графа подсвечивается, необходимо ответить на вопросы типа: "Какие операционные системы используют друзья Томаса?" Вы можете выполнить эту простую операцию обхода Gremlin, чтобы получить сведения из графа:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
А теперь давайте рассмотрим, какие возможности предоставляет база данных Azure Cosmos DB разработчикам Gremlin.

## <a name="gremlin-features"></a>Функции Gremlin
TinkerPop — это стандартная платформа, которая охватывает широкий ряд технологий графа. Таким образом, она содержит стандартную терминологию для описания функций, которые предоставляет поставщик графа. База данных Azure Cosmos DB обеспечивает временную базу данных графа с высокой степенью параллелизма и возможностью записи, которую можно разделить на секции на нескольких серверах или кластерах. 

В таблице ниже перечислены функции TinkerPop, реализованные в базе данных Azure Cosmos DB: 

| Категория | Реализация базы данных Azure Cosmos DB |  Заметки | 
| --- | --- | --- |
| Функции графа | Обеспечивает сохраняемость и одновременный доступ. Разработаны для поддержки транзакций. | Вычислительные методы могут применяться через соединитель Spark. |
| Функции переменной | Поддерживаются такие типы значений: логическое значение, целое число, байт, двойное число с плавающей точкой, число с плавающей точкой, целое число со знаком, полная дата, строка. | Поддерживаются несложные типы. Совместимы со сложными типами через модель данных. |
| Функции вершины | Поддерживаются RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Поддерживается создание, изменение и удаление вершин. |
| Функции свойств вершины | Поддерживаются StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues. | Поддерживается создание, изменение и удаление свойств вершины. |
| Функции ребра | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Поддерживается создание, изменение и удаление ребра. |
| Функции свойств ребра | Поддерживаются типы Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues. | Поддерживается создание, изменение и удаление свойств ребра. |

## <a name="gremlin-wire-format-graphson"></a>Формат подключения Gremlin: GraphSON

При возвращении результатов операций Gremlin в базе данных Azure Cosmos DB используется [формат GraphSON](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format). GraphSON представляет собой стандартный формат Gremlin для представления вершин, ребер и свойств (единичных и со множественными значениями) с помощью JSON. 

Например, в следующем фрагменте кода показано представление GraphSON вершины, *возвращенной клиенту* из базы данных Azure Cosmos DB. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Свойства вершин, используемые в формате GraphSON:

| Свойство | ОПИСАНИЕ |
| --- | --- |
| id | Идентификатор вершины. Должен иметь уникальное значение (если применимо — со значением _partition). |
| label | Метка вершины. Это свойство является необязательным и используется для описания типа сущности. |
| Тип | Используется для отделения вершин от документов, не связанных с графом. |
| properties | Набор определенных пользователем свойств, связанных с вершиной. Каждое свойство может иметь несколько значений. |
| _partition (настраиваемое) | Ключ секции вершины. Это свойство может использоваться для масштабирования графов между несколькими серверами. |
| outE | Содержит список внешних ребер вершины. Хранение смежных сведений с помощью вершины обеспечивает быстрое выполнение обхода графов. Ребра сгруппированы на основе меток. |

Ребро содержит приведенные ниже сведения, необходимые для перехода в другие части графа.

| Свойство | ОПИСАНИЕ |
| --- | --- |
| id | Идентификатор ребра. Должен иметь уникальное значение (если применимо — со значением _partition). |
| label | Метка ребра. Это свойство является необязательным и используется для описания типа связи. |
| inV | Содержит список вершин для ребра. Хранение смежных сведений с помощью ребра обеспечивает быстрое выполнение обхода графов. Вершины сгруппированы на основе меток. |
| properties | Набор определенных пользователем свойств, связанных с ребром. Каждое свойство может иметь несколько значений. |

Каждое свойство может хранить несколько значений в массиве. 

| Свойство | ОПИСАНИЕ |
| --- | --- |
| value | Значение свойства.

## <a name="gremlin-steps"></a>Шаги Gremlin
Теперь рассмотрим шаги Gremlin, поддерживаемые базой данных Azure Cosmos DB. Дополнительные сведения о Gremlin см. в [руководстве по TinkerPop](http://tinkerpop.apache.org/docs/current/reference).

| Шаг | ОПИСАНИЕ | Руководство по TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Добавляет ребро между двумя вершинами. | [Шаг addE](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Добавляет вершину в граф. | [Шаг addV](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Обеспечивает возвращение значения для всех обходов. | [Шаг and](http://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Модулятор шага для назначения переменной выходным данным шага. | [Шаг as](http://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | Модулятор шага, используемый с `group` и `order`. | [Шаг by](http://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Возвращает первый обход, который возвращает результат. | [Шаг coalesce](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Возвращает постоянное значение. Используется с `coalesce`.| [Шаг constant](http://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Возвращает число из обхода. | [Шаг count](http://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Возвращает значения с удаленными повторяющимися значениями. | [Шаг dedup](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Удаляет значения (вершины или ребра). | [Шаг drop](http://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | Действует как барьер, который вычисляет статистическое значение результатов.| [Шаг fold](http://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Группирует значения на основе указанных меток.| [Шаг group](http://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Используется для фильтрации свойств, вершин и ребер. Поддерживает варианты `hasLabel`, `hasId`, `hasNot` и `has`. | [Шаг has](http://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Вставляет значения в поток.| [Шаг inject](http://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Используется для выполнения фильтра с помощью логического выражения. | [Шаг is](http://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | Используется для ограничения числа элементов в обходе.| [Шаг limit](http://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Локально обертывает раздел обхода аналогично вложенному запросу. | [Шаг local](http://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Используется для создания отрицания фильтра. | [Шаг not](http://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | Возвращает результат указанного обхода, если он выдается. В противном случае возвращается вызывающий элемент. | [Шаг optional](http://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Гарантирует, что по крайней мере один из обходов возвращает значение. | [Шаг or](http://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | Возвращает результаты в заданном порядке сортировки. | [Шаг order](http://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Возвращает полный путь обхода. | [Шаг path](http://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Выполняет проекцию свойств в виде сопоставления. | [Шаг project](http://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Возвращает свойства для указанных меток. | [Шаг properties](http://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | Выполняет фильтрацию до заданного диапазона значений.| [Шаг range](http://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | Повторяет шаг указанное количество раз. Используется для циклов. | [Шаг repeat](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Используется для вывода примеров результатов из обхода. | [Шаг sample](http://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Используется для проектирования результатов из обхода. |  [Шаг select](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Используется для статистических функций из обхода без блокировки. | [Шаг store](http://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Выполняет статистическое вычисление путей из вершины в дерево. | [Шаг tree](http://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Развертывает итератор.| [Шаг unfold](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Объединяет результаты из нескольких обходов.| [Шаг union](http://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | Содержит шаги, необходимые для обходов между вершинами и ребрами (`V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`) и `otherV` — для других вершин. | [Шаги vertex](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | Используется для фильтрации результатов из обхода. Поддерживает операторы `eq`, `neq`, `lt`, `lte`, `gt`, `gte` и `between`.  | [Шаг where](http://tinkerpop.apache.org/docs/current/reference/#where-step) |

Оптимизированный для операций записи обработчик Azure Cosmos DB по умолчанию поддерживает автоматическое индексирование всех свойств вершин и ребер. Следовательно, запросы с фильтрами, запросы диапазона, сортировка или статистические функции для любого свойства обрабатываются из индекса и эффективно обслуживаются. Дополнительные сведения о выполнении индексирования в базе данных Azure Cosmos DB см. в руководстве об [индексировании без использования схем](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Дополнительная информация
* Создайте приложение графа [с использованием пакетов SDK](create-graph-dotnet.md). 
* Дополнительные сведения о [поддержке графа](graph-introduction.md) в Azure Cosmos DB

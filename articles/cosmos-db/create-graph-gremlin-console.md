---
title: "Руководство по Azure Cosmos DB. Создание, запрос и просмотр в консоли Apache TinkerPops Gremlin | Документация Майкрософт"
description: "Краткое руководство по созданию вершин, границ и запросов с помощью Graph API в Azure Cosmos DB."
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/19/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: caf3b69b25ccd15322054a0bbf95fc2a5816e00a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/20/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB. Создание, запрос и просмотр в консоли Gremlin

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

В этом кратком руководстве показано, как создать учетную запись Azure Cosmos DB, базу данных и граф (контейнер) с помощью портала Azure, а затем использовать [консоль Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) из [Apache TinkerPop](http://tinkerpop.apache.org) для работы с данными API Graph (предварительная версия). В этом руководстве вы создадите вершины и границы и отправите к ним запрос, обновите свойства вершины, отправите запросы к вершинам, просмотрите граф и удалите вершину.

![Azure DB Cosmos в консоли Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

Консоль Gremlin создана на базе Groovy и Java и работает на компьютерах Linux, Mac и Windows. Ее можно скачать с [сайта Apache TinkerPop](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip).

## <a name="prerequisites"></a>Предварительные требования

Для создания учетной записи Azure Cosmos DB во время работы с этим кратким руководством вам потребуется подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Вам также нужно установить [консоль Gremlin](http://tinkerpop.apache.org/). Используйте версию 3.2.4 или более позднюю.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Добавление графа

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>Подключение к службе приложений
1. Перед запуском консоли Gremlin создайте или измените файл конфигурации *remote-secure.yaml* в каталоге *apache-tinkerpop-gremlin-console-3.2.4/conf*.
2. Укажите *узел*, *порт*, *пользователя*, *пароль*, *пул подключений* и *сериализатор*:

    Настройка|Рекомендуемое значение|Описание
    ---|---|---
    Узлы|***.graphs.azure.com|Универсальный код ресурса (URI) службы графа, который можно получить на портале Azure
    Порт|443|Задайте значение 443
    Имя пользователя|*Имя пользователя*|Ресурс следующего формата: `/dbs/<db>/colls/<coll>`
    Пароль|*Первичный главный ключ*|Первичный главный ключ Azure Cosmos DB
    Пул подключений|{enableSsl: true}|Параметр пула подключений для SSL
    serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Задайте это значение

3. В окне терминала запустите файл *bin/gremlin.bat* или *bin/gremlin.sh*, чтобы запустить [консоль Gremlin](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/).
4. В окне терминала запустите файл *:remote connect tinkerpop.server conf/remote-secure.yaml* для подключения к службе приложений.

Отлично! Теперь, когда мы завершили настройку, начнем выполнять команды консоли.

## <a name="create-vertices-and-edges"></a>Создание вершин и границ

Давайте начнем с добавления четырех вершин для пользователей *Thomas*, *Mary Kay*, *Robin* и *Ben*.

Входные данные (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Выходные данные:

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
Входные данные (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

Выходные данные:

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

Входные данные (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

Выходные данные:

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

Входные данные (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

Выходные данные:

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

Далее добавим границы для создания связей между пользователями.

Входные данные (Thomas -> Mary Kay):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Выходные данные:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Входные данные (Thomas -> Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Выходные данные:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Входные данные (Robin -> Ben):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Выходные данные:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Обновление вершины

Давайте обновим вершину *Thomas* и добавим свойство возраста (*45*).

Входные данные:
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Выходные данные:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Запрос графа

Теперь давайте отправим разные запросы к графу.

Сначала давайте отправим запрос с фильтрацией, который возвращает сведения только о людях старше 40 лет.

Входные данные (запрос с фильтрацией):

```
:> g.V().hasLabel('person').has('age', gt(40))
```

Выходные данные:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Далее давайте спроектируем имя для людей старше 40 лет.

Входные данные (запрос с фильтрацией и проекцией):

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Выходные данные:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Просмотр графа

Давайте просмотрим граф, чтобы возвратить сведения обо всех друзьях пользователя Thomas.

Входные данные (друзья пользователя Thomas):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Выходные данные: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Далее рассмотрим следующий уровень вершин. Давайте просмотрим граф, чтобы возвратить сведения обо всех знакомых пользователя Thomas.

Входные данные (знакомые пользователя Thomas):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Выходные данные:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Удаление вершины

Теперь давайте удалим вершину из базы данных графа.

Входные данные (удаление вершины Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>Очистка графа

Наконец, давайте очистим базу данных ото всех вершин и границ.

Входные данные:

```
:> g.V().drop()
```

Поздравляем! Вы завершили работу с этим руководством по использованию API Graph в Azure Cosmos DB.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, сделав следующее:  

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB, создать граф с помощью обозревателя данных, вершины и границы, а также просмотреть граф с помощью консоли Gremlin. Теперь вы можете создавать более сложные запросы и внедрять эффективную логику обхода графа с помощью Gremlin. 

> [!div class="nextstepaction"]
> [Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API Graph (предварительная версия)](tutorial-query-graph.md)

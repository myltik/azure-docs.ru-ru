---
title: "Разработка с использованием нескольких регионов в DocumentDB | Документация Майкрософт"
description: "Узнайте, как обращаться к данным в нескольких регионах из Azure DocumentDB — полностью управляемой службы базы данных NoSQL."
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: d4579378-0b3a-44a5-9f5b-630f1fa4c66d
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 240f45383639f1eb8a4a9b7c4e4489591be23dba
ms.openlocfilehash: aac8c26cd9af94659abc80b2ae260dc380cf38c9


---
# <a name="developing-with-multi-region-documentdb-accounts"></a>Разработка с помощью учетных записей DocumentDB в нескольких регионах

Узнайте больше об учетных записях DocumentDB в нескольких регионах в этом видео из цикла "Пятница с Azure" от Скотта Хенсельмана (Scott Hanselman) и главного технического руководителя Картика Рамана (Karthik Raman).

>[!ВИДЕО https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a name="introduction"></a>Введение

Чтобы воспользоваться преимуществами [глобального распределения](documentdb-distribute-data-globally.md), клиентские приложения могут указать упорядоченный список предпочитаемых регионов, который будет использоваться для операций с документами. Это можно сделать, настроив политику подключения. Для операций записи и чтения с помощью пакета SDK выбирается наиболее оптимальная конечная точка на основании текущих данных о региональной доступности и списка предпочтений, указанного в конфигурации учетной записи Azure DocumentDB.

Этот список предпочтений указывается при инициализации подключения с помощью клиентского пакета SDK для DocumentDB. Пакеты SDK принимают необязательный параметр PreferredLocations, представляющий собой упорядоченный список регионов Azure.

Пакет SDK будет автоматически отправлять все операции записи в текущий регион записи.

Все операции чтения будут отправляться в первой доступный регион из списка PreferredLocations. Если запрос завершится неудачей, клиент перейдет к следующему региону дальше по списку и так далее.

Клиентские пакеты SDK будут пытаться выполнять чтение данных из регионов, указанных в PreferredLocations. Например, если учетная запись базы данных доступна в трех регионах, но клиент указывает в PreferredLocations только два региона не для записи, то операции чтения из региона записи не будут обслуживаться даже в случае отработки отказа.

Приложение может проверить текущие конечную точку записи и конечную точку чтения, выбранные пакетом SDK, просмотрев два свойства, WriteEndpoint и ReadEndpoint, доступные в SDK 1.8 и более поздней версии.

Если свойство PreferredLocations не задано, будут обслуживаться все запросы из текущего региона записи.

## <a name="net-sdk"></a>ПАКЕТ SDK .NET
Пакет SDK можно использовать без изменения кода. В этом случае пакет SDK автоматически направляет операции чтения и записи в текущий регион записи.

В пакете SDK для .NET 1.8 и более поздней версии параметр ConnectionPolicy конструктора DocumentClient имеет свойство Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Это свойство имеет тип коллекции `<string>` и должно содержать список имен регионов. Строковые значения форматируются по столбцу "Имя региона" на странице [Регионы Azure][regions], без пробелов до или после первого и последнего знака, соответственно.

Текущие конечные точки записи и чтения доступны в DocumentClient.WriteEndpoint и DocumentClient.ReadEndpoint, соответственно.

> [!NOTE]
> Не следует считать URL-адреса конечных точек долговременными константами. Служба может обновить их в любой момент. Пакет SDK обрабатывает это изменение автоматически.
>
>

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
    
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();

    //Setting read region selection preference
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>Пакеты SDK для NodeJS, JavaScript и Python
Пакет SDK можно использовать без изменения кода. В этом случае пакет SDK будет автоматически направлять операции чтения и записи в текущий регион записи.

В пакете SDK 1.8 и более поздней версии параметр ConnectionPolicy конструктора DocumentClient имеет новое свойство DocumentClient.ConnectionPolicy.PreferredLocations. Этот параметр является массивом строк, который принимает список имен регионов. Имена форматируются по столбцу "Имя региона" на странице [Регионы Azure][regions]. Можно также использовать предопределенные константы во вспомогательном объекте AzureDocuments.Regions.

Текущие конечные точки записи и чтения доступны в DocumentClient.getWriteEndpoint и DocumentClient.getReadEndpoint, соответственно.

> [!NOTE]
> Не следует считать URL-адреса конечных точек долговременными константами. Служба может обновить их в любой момент. Пакет SDK обработает это изменение автоматически.
>
>

Ниже приведен пример кода для NodeJS и JavaScript. В Python и Java используется тот же шаблон.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();

    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>REST
После того, как учетная запись базы данных станет доступной в нескольких регионах, клиенты смогут запрашивать ее доступность с помощью запроса GET к следующему универсальному коду ресурса (URI).

    https://{databaseaccount}.documents.azure.com/

Служба вернет список регионов и соответствующих универсальных кодов ресурса (URI) конечных точек DocumentDB для реплик. В ответе будет указан текущий регион записи. Затем клиент сможет выбрать подходящую конечную точку для всех последующих запросов REST API следующим образом.

Пример ответа

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Все запросы PUT, POST и DELETE должны направляться на указанный универсальный код ресурса (URI) записи.
* Все запросы GET и другие запросы только для чтения могут направляться к любой конечной точке на выбор клиента.

Запросы на запись к регионам только для чтения завершатся ошибкой HTTP с кодом 403 ("Запрещено").

В случае изменения региона записи после этапа начального обнаружения клиента все последующие операции записи в предыдущий регион записи завершатся ошибкой HTTP с кодом 403 ("Запрещено"). Клиенту следует еще раз получить список регионов (запрос GET), чтобы получить обновленный регион записи.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о глобальном распространении данных с помощью DocumentDB в следующих статьях.

* [Глобальное распространение данных с помощью DocumentDB](documentdb-distribute-data-globally.md)
* [Уровни согласованности](documentdb-consistency-levels.md)
* [Добавление регионов](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/



<!--HONumber=Feb17_HO2-->



---
title: "Как создать базу данных в DocumentDB | Документация Майкрософт"
description: "Узнайте, как с помощью портала веб-служб для Azure DocumentDB создать базу данных — молниеносно быструю глобальную базу данных NoSQL."
keywords: "Как создать базу данных"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 2503db56-744f-44b5-bf6a-0be821d023ea
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5d93b218a61b325b6c7bd02a160bd2cae844341


---
# <a name="how-to-create-a-database-for-documentdb-using-the-azure-portal"></a>Создание базы данных для DocumentDB на портале Azure
Чтобы использовать Microsoft Azure DocumentDB, необходимы [учетная запись DocumentDB](documentdb-create-account.md), база данных, коллекция и документы. Теперь на портале Azure базы данных DocumentDB создаются одновременно с коллекцией. 

Чтобы создать базу данных DocumentDB и коллекцию на портале, ознакомьтесь с разделом [Создание коллекции DocumentDB на портале Azure](documentdb-create-collection.md).

## <a name="other-ways-to-create-a-documentdb-database"></a>Другие способы создания базы данных DocumentDB
Базы данных необязательно создавать на портале: для этого также можно использовать [пакеты SDK для DocumentDB](documentdb-sdk-dotnet.md) или [REST API](https://msdn.microsoft.com/library/mt489072.aspx). Сведения о работе с базами данных с помощью пакета SDK для .NET см. в [примерах баз данных для .NET](documentdb-dotnet-samples.md#database-examples). Сведения о работе с базами данных с помощью пакета SDK для Node.js см. в [примерах баз данных для Node.js](documentdb-nodejs-samples.md#database-examples). 

## <a name="next-steps"></a>Дальнейшие действия
После создания базы данных и коллекции можно [добавить документы JSON](documentdb-view-json-document-explorer.md) с помощью проводника документов на портале, [импортировать документы](documentdb-import-data.md) в коллекцию с помощью DocumentDB Data Migration Tool или использовать один из [пакетов SDK для DocumentDB](documentdb-sdk-dotnet.md) для выполнения операций CRUD. DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. Примеры кода .NET, в которых показано, как создавать, удалять и обновлять документы, см. в [примерах документов .NET](documentdb-dotnet-samples.md#document-examples). Для работы с документами с помощью пакета SDK для Node.js ознакомьтесь с [примерами документов Node.js](documentdb-nodejs-samples.md#document-examples). 

Добавив документы в коллекцию, [DocumentDB SQL](documentdb-sql-query.md) можно использовать для [выполнения запросов](documentdb-sql-query.md#executing-sql-queries) к документам с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале, [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](documentdb-sdk-dotnet.md). 




<!--HONumber=Nov16_HO3-->



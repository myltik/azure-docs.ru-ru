---
title: Поддержка хранилища таблиц Azure в Azure Cosmos DB | Документация Майкрософт
description: Узнайте, как совместно использовать API таблиц Azure Cosmos DB и хранилище таблиц Azure.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 2560c2ee34a83ce86db043e17fb41192c31de398
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Разработка с использованием API таблиц Azure Cosmos DB и хранилища таблиц Azure

API таблиц Azure Cosmos DB и хранилище таблиц Azure используют одну и ту же модель данных и предоставляют одинаковые операции создания, удаления, обновления и запроса в своих пакетах SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Разработка с использованием API таблиц Azure Cosmos DB

В настоящее время [API таблиц в Azure Cosmos DB](table-introduction.md) содержит четыре пакета SDK для разработки: 
- Пакет SDK для .NET [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget). Эта библиотека включает в себя те же классы и подписи методов, что и [пакет SDK WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Кроме того, она может подключаться к учетным записям Azure Cosmos DB через API таблиц. 
- [Пакет SDK для Python](table-sdk-python.md). Новый пакет SDK для Python в Azure Cosmos DB — единственный пакет SDK, который поддерживает хранилище таблиц Azure в Python. Этот пакет SDK подключается к хранилищу таблиц Azure и API таблиц Azure Cosmos DB.
- [Пакет SDK для Java](table-sdk-java.md). Этот пакет SDK для службы хранилища Azure может подключаться к учетным записям Azure Cosmos DB с помощью API таблиц.
- [Пакет SDK для Node.js](table-sdk-nodejs.md). Этот пакет SDK для службы хранилища Azure может подключаться к учетным записям Azure Cosmos DB с помощью API таблиц.

Дополнительные сведения о работе с API таблиц доступны в разделе [Разработка с использованием предварительной версии API таблиц](faq.md#develop-with-the-table-api).

## <a name="developing-with-the-azure-table-storage"></a>Разработка с использованием хранилища таблиц Azure

Для [хранилища таблиц Azure](table-storage-overview.md) доступно много пакетов SDK и руководств, которые теперь представлены в разделе [Хранилище таблиц Azure](table-storage-overview.md). Эти статьи обновляются по мере реализации взаимодействия между пакетами SDK хранилища таблиц Azure SDK и интерфейсами API таблиц Azure Cosmos DB.  






---
title: "Поддержка хранилища таблиц Azure в Azure Cosmos DB | Документация Майкрософт"
description: "Узнайте, как совместно использовать API таблиц Azure Cosmos DB и хранилище таблиц Azure."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Разработка с использованием API таблиц Azure Cosmos DB и хранилища таблиц Azure

API таблиц Azure Cosmos DB и хранилище таблиц Azure используют одну и ту же модель данных и предоставляют одинаковые операции создания, удаления, обновления и запроса в своих пакетах SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Разработка с использованием API таблиц Azure Cosmos DB

В настоящее время для [API таблиц Azure Cosmos DB](table-introduction.md) доступен один пакет SDK для .NET, [WindowsAzure.Storage-PremiumTable (предварительная версия)](https://aka.ms/premiumtablenuget). Эта библиотека включает в себя те же классы и подписи методов, что и [пакет SDK WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Кроме того, она может подключаться к учетным записям Azure Cosmos DB через API таблиц (предварительная версия). Краткое руководство и руководство по использованию API таблиц Azure Cosmos DB приведены в следующих статьях:
- Краткое руководство: [Azure Cosmos DB. Создание приложения .NET с помощью API таблицы](create-table-dotnet.md).
- Руководство: [Разработка с помощью API таблицы базы данных Azure Cosmos DB на языке .NET](tutorial-develop-table-dotnet.md).

Дополнительные сведения о работе с API таблиц доступны в разделе [Разработка с использованием предварительной версии API таблиц](faq.md#develop-with-the-table-api-preview).

## <a name="developing-with-the-azure-table-storage"></a>Разработка с использованием хранилища таблиц Azure

Для [хранилища таблиц Azure](table-storage-overview.md) доступно много пакетов SDK и руководств, которые теперь представлены в разделе [Хранилище таблиц Azure](table-storage-overview.md). Эти статьи обновляются по мере реализации взаимодействия между пакетами SDK хранилища таблиц Azure SDK и интерфейсами API таблиц Azure Cosmos DB.  






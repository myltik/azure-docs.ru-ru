---
title: "Примеры Azure CLI для Azure Cosmos DB | Документация Майкрософт"
description: "Примеры Azure CLI для создания учетных записей, баз данных, контейнеров, регионов и брандмауэров Azure Cosmos DB и управления ими."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d1a0aa78c94c6305018d24c521de643197d4402c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Примеры Azure CLI для Azure Cosmos DB

В следующей таблице содержатся ссылки на примеры сценариев Azure CLI для Azure Cosmos DB.

| |  |
|---|---|
|**Создание учетной записи, базы данных и контейнеров Azure Cosmos DB**||
|[Создание учетной записи API документов, диаграмм или таблиц](scripts/create-database-account-collections-cli.md)| Создание отдельной учетной записи, базы данных и контейнера API DocumentDB в Azure Cosmos DB. |
| [Создание учетной записи API MongoDB](scripts/create-mongodb-database-account-cli.md) | Создание отдельной учетной записи, базы данных и коллекции API MongoDB в Azure Cosmos DB. |
|**Масштабирование Azure Cosmos DB**||
| [Масштабирование пропускной способности контейнера](scripts/scale-collection-throughput-cli.md) | Изменение подготовленной пропускной способности контейнера.|
|[Репликация учетной записи базы данных Azure Cosmos DB в нескольких регионах и настройка приоритетов отработки отказа](scripts/scale-multiregion-cli.md)|Глобальная репликация данные учетной записи в нескольких регионах с указанным приоритетом отработки отказа.|
|**Безопасность Azure Cosmos DB**||
| [Получение ключей учетной записи](scripts/secure-get-account-key-cli.md) | Возвращение первичного и вторичного главных ключей для записи и первичного и вторичного ключей только для чтения для учетной записи.|
| [Получение строки подключения MongoDB](scripts/secure-mongo-connection-string-cli.md) | Получение строки подключения для подключения приложения MongoDB к учетной записи Azure Cosmos DB.|
|[Повторное создание ключей учетной записи](scripts/secure-regenerate-key-cli.md)|Повторное создание главного ключа или ключа только для чтения для учетной записи.|
|[Создание брандмауэра](scripts/create-firewall-cli.md)| Создание политики контроля доступа для входящих IP-адресов для разрешения доступа к учетной записи с утвержденных компьютеров и (или) из облачных служб.|
|**Высокий уровень доступности, аварийное восстановление, архивация и восстановление**||
|[Настройка политики отработки](scripts/ha-failover-policy-cli.md)|Настройка приоритета отработки отказа для каждого региона, в котором реплицируется учетная запись.|
|**Подключение Azure Cosmos DB к ресурсам**||
|[Подключение веб-приложения к Azure Cosmos DB](https://docs.microsoft.com/azure/app-service-web/scripts/app-service-cli-app-service-documentdb?toc=%2fcli%2fazure%2ftoc.json)|Создание и подключение базы данных Azure Cosmos DB и веб-приложения Azure.|
|||

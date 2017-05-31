---
title: "Примеры Azure PowerShell для базы данных Azure Cosmos DB | Документация Майкрософт"
description: "Примеры Azure PowerShell — это скрипты для создания учетных записей базы данных Azure Cosmos DB и управления ими."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sample
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8bf047bd19c5278bfff85cab63ea10a1838cc683
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Примеры Azure PowerShell для базы данных Azure Cosmos DB

В следующей таблице содержатся ссылки на примеры скриптов Azure PowerShell для базы данных Azure Cosmos DB.

| |  |
|---|---|
|**Создание учетной записи Azure Cosmos DB**||
|[Azure Cosmos DB: Create a DocumentDB API account using PowerShell](scripts/create-database-account-powershell.md) (База данных Azure Cosmos: создание учетной записи API DocumentDB с помощью PowerShell)| Создает одну учетную запись базы данных Azure Cosmos DB для использования с API DocumentDB. |
|**Масштабирование базы данных Azure Cosmos DB**||
|[Replicate an Azure Cosmos DB database account in multiple regions and configure failover priorities using PowerShell](scripts/scale-multiregion-powershell.md) (Репликация учетной записи базы данных Azure Cosmos DB в нескольких регионах и настройка приоритетов отработки отказа с помощью PowerShell)|Глобально реплицирует данные учетной записи в нескольких регионах с указанным приоритетом отработки отказа.|
|**Безопасность базы данных Azure Cosmos DB**||
| [Get account keys for Azure Cosmos DB using PowerShell](scripts/secure-get-account-key-powershell.md) (Получение ключей учетной записи для базы данных Azure Cosmos с помощью PowerShell) | Возвращает первичный и вторичный главные ключи для записи и первичный и вторичный ключи только для чтения для учетной записи.|
| [Get an Azure Cosmos DB connection string for MongoDB apps using PowerShell](scripts/secure-mongo-connection-string-powershell.md) (Получение строки подключения базы данных Azure Cosmos для приложений MongoDB с помощью PowerShell) | Получает строку подключения для подключения приложения MongoDB к учетной записи базы данных Azure Cosmos DB.|
|[Regenerate an Azure Cosmos DB account key using PowerShell](scripts/secure-regenerate-key-powershell.md) (Повторное создание ключей учетных записей базы данных Azure Cosmos с помощью PowerShell)|Повторно создает главный ключ или ключ только для чтения для учетной записи.|
|[Azure Cosmos DB: Create a firewall using PowerShell](scripts/create-firewall-powershell.md) (База данных Azure Cosmos: создание брандмауэра с помощью PowerShell)| Создает политику контроля доступа на основе входящих IP-адресов для разрешения доступа к учетной записи из утвержденных компьютеров и (или) облачных служб.|
|**Высокий уровень доступности, аварийное восстановление, архивация и восстановление**||
|[Создание политики отработки отказа базы данных Azure Cosmos для обеспечения высокой доступности с помощью PowerShell](scripts/ha-failover-policy-powershell.md)|Задает приоритет отработки отказа каждого региона, в котором реплицируется учетная запись.|
|||

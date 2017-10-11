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
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 7698e03c0dc8d1c6d1e926f45e903a909bfd0c93
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Примеры Azure PowerShell для базы данных Azure Cosmos DB

В следующей таблице содержатся ссылки на примеры скриптов Azure PowerShell для базы данных Azure Cosmos DB.

| |  |
|---|---|
|**Создание учетной записи Azure Cosmos DB**||
|[Azure Cosmos DB: Create a DocumentDB API account using PowerShell](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (База данных Azure Cosmos: создание учетной записи API DocumentDB с помощью PowerShell)| Создает одну учетную запись базы данных Azure Cosmos DB для использования с API DocumentDB. |
|**Масштабирование базы данных Azure Cosmos DB**||
|[Replicate an Azure Cosmos DB database account in multiple regions and configure failover priorities using PowerShell](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Репликация учетной записи базы данных Azure Cosmos DB в нескольких регионах и настройка приоритетов отработки отказа с помощью PowerShell)|Глобально реплицирует данные учетной записи в нескольких регионах с указанным приоритетом отработки отказа.|
|**Безопасность базы данных Azure Cosmos DB**||
| [Получение ключей учетной записи](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Возвращение первичного и вторичного главных ключей для записи и первичного и вторичного ключей только для чтения для учетной записи.|
| [Получение строки подключения MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Получает строку подключения для подключения приложения MongoDB к учетной записи базы данных Azure Cosmos DB.|
|[Повторное создание ключей учетной записи](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Повторное создание главного ключа или ключа только для чтения для учетной записи.|
|[Создание брандмауэра](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание политики контроля доступа для входящих IP-адресов для разрешения доступа к учетной записи с утвержденных компьютеров и (или) из облачных служб.|
|**Высокий уровень доступности, аварийное восстановление, архивация и восстановление**||
|[Настройка политики отработки](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Задает приоритет отработки отказа каждого региона, в котором реплицируется учетная запись.|
|||
---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664716"
---
### <a name="sql-databases"></a>Базы данных SQL

|  |  |
|---------|---------|
| [Допустимые номера SKU базы данных SQL](../articles/azure-policy/scripts/allowed-sql-db-skus.md) | Требует, чтобы базы данных SQL использовали утвержденные номера SKU. Вы можете указать массив допустимых идентификаторов или названий SKU. |
| [Аудит параметра обнаружения угроз на уровне базы данных](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) | Проверяет, имеют ли политики оповещения системы безопасности для базы данных SQL указанное состояние. Задайте значение, указывающее состояние обнаружения угроз (включено или отключено).  |
| [Проверка шифрования базы данных SQL](../articles/azure-policy/scripts/sql-database-encryption-audit.md) | Проверяет, включено ли прозрачное шифрование данных для базы данных SQL. |
| [Аудит параметров аудита уровня базы данных SQL](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) | Проверяет, соответствуют ли заданной настройке параметры аудита базы данных SQL. Задайте значение, указывающее, должны ли быть включены параметры аудита.  |
| [Проверка состояния прозрачного шифрования данных](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) | Проверяет, включено ли прозрачное шифрование данных для базы данных SQL.  |
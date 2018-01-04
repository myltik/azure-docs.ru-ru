---
title: "Обзор предварительной версии службы Azure Database Migration Service | Документация Майкрософт"
description: "Обзор службы Azure Database Migration Service, которая обеспечивает непрерывную миграцию из множества источников баз данных на платформы данных Azure."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 12/13/2017
ms.openlocfilehash: 80ec9e177e5c1408eb28338c0c0acae9c672eeb1
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="what-is-the-azure-database-migration-service-preview"></a>Что такое предварительная версия службы Azure Database Migration Service?
Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя. Служба, которая сейчас доступна в режиме общедоступной предварительной версии, оптимизируется для включения следующих возможностей:

- надежность и производительность;
- итеративное добавление пар исходного и целевого объектов;
- работа над функциями непрерывной миграции.

## <a name="use-familiar-tools"></a>Использование привычных средств
Служба Azure Database Migration Service обладает некоторыми функциональными возможностями, которые можно найти в других наших средствах и службах.  Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](http://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы будете готовы приступить к переносу, Azure Database Migration Service выполнит все необходимые действия. Можно отправить и забыть проектами миграции с спокойствие помните, зная, что этот процесс использует рекомендации по определению корпорации Майкрософт.

## <a name="regional-availability-during-public-preview"></a>Региональные доступности предварительной версии
Общедоступная предварительная версия Azure Database Migration Service сейчас доступна в таких регионах:
- Восток США
- Южно-центральный регион США
- Запад США
- Южная часть Бразилии
- Западная Европа
- Северная Европа

## <a name="next-steps"></a>Дальнейшие действия
- [Создание экземпляра службы Database Migration Service с помощью портала Azure](quickstart-create-data-migration-service-portal.md)

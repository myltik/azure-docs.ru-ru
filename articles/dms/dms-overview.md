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
ms.date: 11/17/2017
ms.openlocfilehash: cc4053ac2be7e3a7a45da9a2a55f32f68bd9e3a0
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2017
---
# <a name="what-is-the-azure-database-migration-service-preview"></a>Что такое предварительная версия службы Azure Database Migration Service?
Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя. Служба, которая сейчас доступна в режиме общедоступной предварительной версии, оптимизируется для включения следующих возможностей:

- надежность и производительность;
- итеративное добавление пар исходного и целевого объектов;
- работа над функциями непрерывной миграции.

## <a name="use-familiar-tools"></a>Использование привычных средств
Служба Azure Database Migration Service обладает некоторыми функциональными возможностями, которые можно найти в других наших средствах и службах.  Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](http://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы будете готовы приступить к переносу, Azure Database Migration Service выполнит все необходимые действия. Запустив перенос, вы можете больше не беспокоиться об этом процессе. Все будет сделано автоматически в соответствии с рекомендациями Майкрософт.

## <a name="regional-availabilty-during-public-preview"></a>Доступность предварительной версии в регионах
Общедоступная предварительная версия Azure Database Migration Service сейчас доступна в таких регионах:
- Восток США
- Южно-центральный регион США
- Запад США
- Южная часть Бразилии
- Западная Европа
- Северная Европа

## <a name="next-steps"></a>Дальнейшие действия
- [Создание экземпляра службы Database Migration Service с помощью портала Azure](quickstart-create-data-migration-service-portal.md)

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
ms.openlocfilehash: 2aae105b7454209131db79c60d74740ce97c21ce
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2018
---
# <a name="what-is-the-azure-database-migration-service-preview"></a>Что такое предварительная версия службы Azure Database Migration Service?
Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя. Служба, которая сейчас доступна в режиме общедоступной предварительной версии, оптимизируется для включения следующих возможностей:

- надежность и производительность;
- итеративное добавление пар исходного и целевого объектов;
- работа над функциями непрерывной миграции.

## <a name="use-familiar-tools"></a>Использование привычных средств
Служба Azure Database Migration Service обладает некоторыми функциональными возможностями, которые можно найти в других наших средствах и службах.  Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](http://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы будете готовы приступить к переносу, Azure Database Migration Service выполнит все необходимые действия. Запустив перенос, вы можете больше не думать об этом процессе. Все будет сделано автоматически в соответствии с рекомендациями Майкрософт.

## <a name="regional-availability-during-public-preview"></a>Доступность предварительной версии в регионах
Общедоступная предварительная версия Azure Database Migration Service сейчас доступна в таких регионах:
- Восток США
- Южно-центральный регион США
- Запад США
- Южная часть Бразилии
- Западная Европа
- Северная Европа
- Юго-Восточная Азия
- Западная Индия

## <a name="next-steps"></a>Дополнительная информация
- [Создание экземпляра службы Database Migration Service с помощью портала Azure](quickstart-create-data-migration-service-portal.md)
- [Миграция с SQL Server в базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md).
- [Overview of prerequisites for using the Azure Database Migration Service](pre-reqs.md) (Предварительные требования для использования службы Azure Database Migration Service).
- [FAQ about using the Azure Database Migration Service](faq.md) (Часто задаваемые вопросы о службе Azure Database Migration Service).

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
ms.date: 11/08/2017
ms.openlocfilehash: 7c7f93278d7020f1a20054934cfd6b34ce002c26
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-the-azure-database-migration-service-preview"></a>Что такое предварительная версия службы Azure Database Migration Service?
Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя. Служба, которая сейчас доступна в режиме общедоступной предварительной версии, оптимизируется для включения следующих возможностей:

- надежность и производительность;
- итеративное добавление пар исходного и целевого объектов;
- работа над функциями непрерывной миграции.

## <a name="use-familiar-tools"></a>Использование привычных средств
Служба Azure Database Migration Service объединяет некоторые функции существующих средств и служб.  Она предоставляет пользователям комплексное решение с высокой доступностью. Служба Azure Database Migration Service использует [Помощник по миграции данных](http://aka.ms/dma) для предоставления оценочных отчетов.  В этих рекомендациях описываются изменения, которые нужно внести перед выполнением миграции. Все необходимые исправления выполняются пользователями. Когда вы будете готовы приступить к миграции, служба Azure Database Migration Service выполнит все связанные действия. Можно запустить миграцию и больше не беспокоиться об этом процессе, зная, что он основывается на рекомендациях, определенных корпорацией Майкрософт. 


## <a name="next-steps"></a>Дальнейшие действия
- [Создайте службу Azure Database Migration Service с помощью портала Azure](quickstart-create-data-migration-service-portal.md).

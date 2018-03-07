---
title: "Интерфейс командной строки Azure Service Fabric : sfctl | Документация Майкрософт"
description: "Описание команд sfctl интерфейса командной строки Azure Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 7c8563539ca8507f05fa99fdeffbf511b1540a6a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl"></a>sfctl 
Команды для управления кластерами и сущностями Service Fabric. Эта версия совместима со средой выполнения Service Fabric 6.1. Команды следуют схеме "глагол-существительное". Чтобы узнать больше, ознакомьтесь с приведенными ниже подгруппами.

## <a name="subgroups"></a>Подгруппы
|Подгруппа|ОПИСАНИЕ|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| Создание и удаление приложений и типов приложений, а также управление ими.|
| [chaos](service-fabric-sfctl-chaos.md)   | Запуск и остановка службы хаотического тестирования, а также вывод отчета о ее работе.|
| [cluster](service-fabric-sfctl-cluster.md) | Выбор кластеров Service Fabric, управление ими и их эксплуатация.|
| [compose](service-fabric-sfctl-compose.md) | Создание, удаление приложений Docker Compose и управление ими.|
| [is](service-fabric-sfctl-is.md)      | Запрос службы инфраструктуры и отправка команд для нее.|
| [node](service-fabric-sfctl-node.md)    | Управление узлами, которые образуют кластер.|
| [partition](service-fabric-sfctl-partition.md)  | Запрос секций для любой службы и управление ими.|
| property  | Хранение и запрос свойств с именами Service Fabric.|
| [rpm](service-fabric-sfctl-rpm.md)        | Запрос службы диспетчера восстановления и отправка команд для нее.|
| [replica](service-fabric-sfctl-replica.md) | Управление репликами, принадлежащими секциям службы.|
| [service](service-fabric-sfctl-service.md) | Создание и удаление служб, типов служб и пакетов службы, а также управление ими.|
| [store](service-fabric-sfctl-store.md)   | Выполнение базовых операций на уровне файла с хранилищем образов кластера.|

## <a name="next-steps"></a>Дополнительная информация
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).
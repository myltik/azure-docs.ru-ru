---
title: "Создание пространства имен служебной шины с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как создать пространство имен служебной шины с помощью портала Azure."
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/30/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 4a972b9b8b52a90f27afda98d8bdc661016d1fe1
ms.openlocfilehash: ba7093f8a2c06ab4cecf11207174a4871435ae64
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Создание пространства имен служебной шины с помощью портала Azure
Пространство имен — это общий контейнер для всех компонентов обмена сообщениями. В одном пространстве имен могут содержаться несколько очередей и разделов. Часто пространства имен выполняют роль контейнеров приложений. В настоящее время создать пространство имен служебной шины можно двумя способами.

1. Портал Azure (в этой статье)
2. [Шаблоны Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Создание пространства имен на портале Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Поздравляем! Вы создали пространство имен служебной шины, предназначенное для обмена сообщениями.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с [примерами GitHub][github-samples], демонстрирующими расширенные возможности обмена сообщениями служебной шины Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples


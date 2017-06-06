---
title: "Создание пространства имен служебной шины с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как создать пространство имен служебной шины с помощью портала Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 5de92033eb7eb4fef8d27a215b3284ab80594065
ms.contentlocale: ru-ru
ms.lasthandoff: 03/24/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Создание пространства имен служебной шины с помощью портала Azure
Пространство имен — это общий контейнер для всех компонентов обмена сообщениями. В одном пространстве имен могут содержаться несколько очередей и разделов. Часто пространства имен выполняют роль контейнеров приложений. Сейчас создать пространство имен служебной шины можно двумя способами.

1. Портал Azure (в этой статье)
2. [Шаблоны Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Создание пространства имен на портале Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Поздравляем! Вы создали пространство имен служебной шины, предназначенное для обмена сообщениями.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с [примерами GitHub][github-samples], демонстрирующими расширенные возможности обмена сообщениями служебной шины Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples


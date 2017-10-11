---
title: "Как создать пространство имен служебной шины с помощью портала Azure | Документация Майкрософт"
description: "Создание пространства имен служебной шины с помощью портала Azure."
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
ms.date: 06/27/2017
ms.author: sethm
ms.openlocfilehash: c8654ed547a9001e2e968d2a45d990a73ef27d3b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Создание пространства имен служебной шины с помощью портала Azure

Пространство имен — это контейнер, определяющий область действия для всех компонентов обмена сообщениями. В одном пространстве имен могут содержаться несколько очередей и разделов. Часто пространства имен выполняют роль контейнеров приложений. Сейчас создать пространство имен служебной шины можно двумя способами.

1. Портал Azure (в этой статье)
2. [Шаблоны Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Создание пространства имен на портале Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Поздравляем! Вы создали пространство имен служебной шины, предназначенное для обмена сообщениями.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [примерами GitHub][github-samples], демонстрирующими расширенные возможности обмена сообщениями служебной шины Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples

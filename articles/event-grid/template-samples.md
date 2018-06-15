---
title: Примеры шаблонов Azure Resource Manager для службы "Сетка событий" | Документация Майкрософт
description: Примеры шаблонов Azure Resource Manager для службы "Сетка событий"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246276"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Шаблоны Azure Resource Manager для службы "Сетка событий"

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для службы "Сетка событий".

| | |
|-|-|
|**Подписки на службу "Сетка событий"**||
| [Использование настраиваемых разделов и подписок с конечной точкой веб-перехватчика](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Развертывает пользовательский раздел службы "Сетка событий". Создает подписку на этот пользовательский раздел, который использует конечную точку веб-перехватчика. |
| [Использование настраиваемых разделов и подписок с конечной точкой концентратора событий](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Создает подписку службы "Сетка событий" на пользовательский раздел, который уже существует. Подписка использует концентратор событий для конечной точки. |
| [Подписка на группу ресурсов](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Создает подписку на события для группы ресурсов. Группа ресурсов, указанная как целевой объект во время развертывания, является источником событий. Подписка использует концентратор событий для конечной точки. |
| [Учетная запись хранения BLOB-объектов и подписка](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Развертывает учетную запись службы хранилища больших двоичных объектов Azure и создает подписку на события для этой учетной записи хранения. |
| | |

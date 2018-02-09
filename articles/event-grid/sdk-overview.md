---
title: "Пакеты SDK для службы \"Сетка событий Azure\""
description: "Описывает пакеты SDK для службы \"Сетка событий Azure\". Эти пакеты SDK обеспечивают управление, публикацию и использование."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Пакеты SDK для управления службой "Сетка событий Azure" и публикации в ней

В службе "Сетка событий Azure" доступны пакеты SDK, которые позволяют управлять ресурсами и публиковать события с помощью программных средств.

## <a name="management-sdks"></a>Пакеты SDK для управления

Пакеты SDK для управления позволяют создавать, обновлять и удалять разделы и подписки сетки событий. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>Пакеты SDK для публикации

Пакеты SDK для публикации позволяют публиковать события в разделы. Эти пакеты обеспечивают аутентификацию, формирование события и асинхронную публикацию на указанной конечной точке. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [службе "Сетка событий Azure"](overview.md).
* Команды Azure CLI для службы "Сетка событий Azure" см. в статье об [Azure CLI](/cli/azure/eventgrid).
* Команды PowerShell для службы "Сетка событий Azure" см. в статье об [PowerShell](/powershell/module/azurerm.eventgrid).
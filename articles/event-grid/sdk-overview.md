---
title: Пакеты SDK для службы "Сетка событий Azure"
description: Описывает пакеты SDK для службы "Сетка событий Azure". Эти пакеты SDK обеспечивают управление, публикацию и использование.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 82f08341f8c96695a4ceddb19d1b610d70b89f88
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Пакеты SDK для управления службой "Сетка событий Azure" и публикации в ней

В службе "Сетка событий Azure" доступны пакеты SDK, которые позволяют управлять ресурсами и публиковать события с помощью программных средств.

## <a name="management-sdks"></a>Пакеты SDK для управления

Пакеты SDK для управления позволяют создавать, обновлять и удалять разделы и подписки сетки событий. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Пакеты SDK для плоскости данных

Пакеты SDK для плоскости данных позволяют публиковать события в разделы. Эти пакеты обеспечивают аутентификацию, формирование события и асинхронную публикацию на указанной конечной точке. Они также позволяют использовать события первой стороны. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [службе "Сетка событий Azure"](overview.md).
* Команды Azure CLI для службы "Сетка событий Azure" см. в статье об [Azure CLI](/cli/azure/eventgrid).
* Команды PowerShell для службы "Сетка событий Azure" см. в статье об [PowerShell](/powershell/module/azurerm.eventgrid).
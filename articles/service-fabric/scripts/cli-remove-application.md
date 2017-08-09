---
title: "Пример скрипта Azure CLI: удаление приложения из кластера | Документация Майкрософт"
description: "Пример скрипта Azure CLI для удаления приложения из кластера Service Fabric."
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Удаление приложения из кластера Service Fabric

Этот скрипт удаляет запущенный экземпляр приложения Service Fabric и отменяет регистрацию типа и версии приложения.  При удалении экземпляра приложения также удаляются все выполняющиеся экземпляры службы, связанные с этим приложением. Затем из хранилища образов удаляются файлы приложения. 

При необходимости установите [Azure CLI](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Пример скрипта

[!code-sh[главный](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Удаление приложения из кластера")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Выбирает кластер. |
| [sf application delete](/cli/azure/sf/application#delete) | Удаляет экземпляр приложения из кластера. |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | Отменяет регистрацию версии и типа приложения Service Fabric в кластере.|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | Удаляет пакет приложения Service Fabric из хранилища образов. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [документации по Azure CLI](../service-fabric-azure-cli-2-0.md).

Дополнительные примеры скриптов Azure CLI для Azure Service Fabric см. в статье [Примеры сценариев для Azure PowerShell](../samples-cli.md).


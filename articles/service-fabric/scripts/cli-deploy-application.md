---
title: "Пример скрипта Azure CLI: развертывание приложения в кластере"
description: "Пример скрипта Azure CLI: развертывание приложения в кластере Service Fabric."
services: service-fabric
documentationcenter: 
author: Thraka
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
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Развертывание приложения в кластера Service Fabric

Этот пример сценария копирует пакет приложения в хранилище образов кластера, регистрирует тип приложения в кластере и создает экземпляр приложения с типом приложения.  Если в манифесте приложения для целевого типа приложения были определены используемые по умолчанию службы, то они также будут созданы.

При необходимости установите [Azure CLI](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Пример скрипта

[!code-sh[главный](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Развертывание приложения в кластере")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После запуска примера сценария можно использовать сценарий из раздела [Удаление приложения](cli-remove-application.md) для удаления экземпляра приложения, отмены регистрация типа приложения и удаления пакета приложения из хранилища образов.

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.


| Команда | Примечания |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Выбирает кластер. |
| [sf application upload](/cli/azure/sf/application#upload) | Отправляет файлы и манифесты приложения. |
| [sf application provision](/cli/azure/sf/application#provision) | Регистрирует приложение в кластере.|
| [sf application create](/cli/azure/sf/application#create) | Создает экземпляр приложения и развертывает все указанные службы на узлах. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [документации по Azure CLI](../service-fabric-azure-cli-2-0.md).

Дополнительные примеры скриптов Azure CLI для Azure Service Fabric см. в статье [Примеры сценариев для Azure PowerShell](../samples-cli.md).


---
title: "Пример сценария Azure PowerShell. Создание кластера Service Fabric | Документы Майкрософт"
description: "Сценарий Azure PowerShell пример — создание кластера Service Fabric теста трех узлов."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/12/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 03348efa4ebdaed987df73756c6b57da0cc76fb5
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Создание тестов с тремя узлами кластера Service Fabric

Этот образец скрипта создает тестов с тремя узлами кластера Service Fabric, защищен с помощью сертификата X.509. Три узла конфигурации кластера поддерживается для разработки и тестирования, так как можно выполнять обновление и остались после сбоев отдельных узлов (при условии, что они не осуществляется одновременно). Для устойчивость к отказам одновременных производственного кластера требуется пять или более узлов.  

Команда создает самозаверяющий сертификат и передает его в новый хранилища ключей, который создается в той же группе ресурсов кластера. Сертификат также копируется в локальный каталог.  Укажите параметр *-OS* для выбора версии Windows или Linux, которая запущена на узлах кластера.  Измените параметры, если это необходимо.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После запуска сценария вы можете удалить группу ресурсов, кластер и все связанные ресурсы, выполнив следующую команду.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Создает кластер Service Fabric. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).

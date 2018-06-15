---
title: Пример сценария Azure PowerShell. Создание кластера Service Fabric | Документы Майкрософт
description: Пример скрипта Azure PowerShell для создания кластера Service Fabric с тремя узлами
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 169f68d179c7f895078fe649d0e2a69e58d148cb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597509"
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Создание тестового кластера Service Fabric с тремя узлами

В этом примере скрипта создается кластер Service Fabric с тремя узлами, защищенный с помощью сертификата X.509. Кластер с тремя узлами можно использовать для разработки и тестирования, так как в этом случае можно безопасно выполнять обновления и справляться со сбоями отдельных узлов, если они не происходят одновременно. Для устойчивости к одновременным сбоям рабочий кластер должен включать не менее пяти узлов.  

Команда создает самозаверяющий сертификат и передает его в новое хранилище ключей, которое создается в той же группе ресурсов, что и кластер. Сертификат также копируется в локальный каталог.  Укажите параметр *-OS* для выбора версии Windows или Linux, которая запущена на узлах кластера.  Измените параметры, если это необходимо.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzureRmAccount`, чтобы создать подключение к Azure. 

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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).

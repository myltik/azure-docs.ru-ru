---
title: "Azure Service Fabric: различия между версиями для Linux и Windows | Документация Майкрософт"
description: "Различия между предварительной версией Azure Service Fabric для Linux и Azure Service Fabric для Windows."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 00c1f10f7df865344233143ce9cdf546e64acca0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Различия между Service Fabric для Linux (предварительная версия) и Windows (общедоступная версия)

Так как платформа Service Fabric для Linux доступна в предварительной версии, некоторые ее функции поддерживаются в Windows, но не в Linux. В конечном счете, когда появится общедоступная версия Service Fabric для Linux, наборы функций будут одинаковыми.

* Надежные коллекции (и надежные службы с отслеживанием состояния) не поддерживаются в Linux.
* Функция ReverseProxy недоступна в Linux.
* Автономный установщик недоступен в Linux.
* Проверка схемы XML для файлов манифеста не выполняется на платформе Linux. 
* Перенаправление консоли не поддерживается на платформе Linux. 
* Служба анализа сбоя (FAS) недоступна в Linux.
* Поддержка Azure Active Directory недоступна в Linux.
* Недоступны некоторые команды интерфейса командной строки, эквивалентные командам PowerShell.
* В кластере Linux можно выполнять только определенный набор команд PowerShell (подробнее об этом — в следующем разделе).

>[!NOTE]
>Перенаправление консоли не поддерживается в рабочих кластерах, даже в Windows.

Средства разработки в Windows и Linux также отличаются. В Windows используются Visual Studio, PowerShell, VSTS и трассировка событий Windows, а в Linux — Yeoman, Eclipse, Jenkins и LTTng.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Командлеты PowerShell, которые не работают в кластере Service Fabric для Linux

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Дальнейшие действия
* [Подготовка среды разработки в Linux](service-fabric-get-started-linux.md)
* [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md)
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-java.md)
* [Getting started with Eclipse Plugin for Service Fabric Java application development](service-fabric-get-started-eclipse.md) (Начало работы с подключаемым модулем Eclipse для разработки приложения Service Fabric на Java)
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)


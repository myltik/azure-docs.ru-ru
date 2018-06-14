---
title: 'Azure Service Fabric: различия между версиями для Linux и Windows | Документация Майкрософт'
description: Различия между Azure Service Fabric для Linux и Azure Service Fabric для Windows.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 5e0002437b4fc22d9dfdcdca9a587bf420a7d8fa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207688"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Различия между Service Fabric для Linux и для Windows

Некоторые функции Service Fabric поддерживаются в Windows, но пока не поддерживаются в Linux. Со временем наборы функций будут одинаковыми, и с каждом выпуском это различие между функциями будет уменьшаться. Между последними доступными версиями (то есть между версией 6.0 для Windows и версией 6.0 для Linux) существуют различия в следующих компонентах: 

* все модели программирования находятся в предварительной версии (Reliable Actors, Reliable Services без отслеживания состояния и Reliable Services с отслеживанием состояния для Java и C#);
* Envoy (ReverseProxy) для Linux находится в предварительной версии;
* автономный установщик для Linux пока недоступен в Linux;
* перенаправление консоли (не поддерживается в рабочих кластерах Linux и Windows);
* служба анализа сбоев (FAS) в Linux;
* служба DNS для служб Service Fabric (служба DNS поддерживается для контейнеров в Linux);
* команды интерфейса командной строки, эквивалентные некоторым командам PowerShell (большинство из перечисленных ниже команд применяются только к изолированным кластерам).

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



## <a name="next-steps"></a>Дополнительная информация
* [Подготовка среды разработки в Linux](service-fabric-get-started-linux.md)
* [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md)
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-java.md)
* [Getting started with Eclipse Plugin for Service Fabric Java application development](service-fabric-get-started-eclipse.md) (Начало работы с подключаемым модулем Eclipse для разработки приложения Service Fabric на Java)
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Manage an Azure Service Fabric application by using Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md) (Управление приложением Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric)

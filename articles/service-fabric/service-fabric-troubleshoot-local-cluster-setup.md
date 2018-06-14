---
title: Устранение неполадок в настройке локального кластера Azure Service Fabric | Документация Майкрософт
description: В этой статье описываются рекомендации по устранению неполадок с кластером локальной разработки.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: a7f58914fd6e498e717e19bfea11c9e3fcfc0399
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212023"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Устранение неполадок в работе кластера локальной разработки
Если у вас возникли проблемы при взаимодействии с кластером локальной разработки Azure Service Fabric, ознакомьтесь со следующими возможностями для решения.

## <a name="cluster-setup-failures"></a>Ошибки настройки кластера
### <a name="cannot-clean-up-service-fabric-logs"></a>Не удается очистить журналы Service Fabric
#### <a name="problem"></a>Проблема
При выполнении скрипта DevClusterSetup появляется следующее сообщение об ошибке:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Решение
Закройте текущее и откройте новое окно PowerShell от имени администратора. Теперь можно успешно запустить скрипт.

## <a name="cluster-connection-failures"></a>Ошибки подключения к кластеру

### <a name="type-initialization-exception"></a>Исключение типа инициализации
#### <a name="problem"></a>Проблема
При подключении к кластеру в PowerShell отображается ошибка TypeInitializationException для System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Решение
При установке была неправильно настроена переменная пути. Выйдите из Windows и снова выполните вход. Путь обновится.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Сбой подключения к кластеру с сообщением об ошибке "Объект закрыт"
#### <a name="problem"></a>Проблема
Вызов Connect-ServiceFabricCluster завершается со следующей ошибкой:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Решение
Закройте текущее и откройте новое окно PowerShell от имени администратора.

### <a name="fabric-connection-denied-exception"></a>Исключение Fabric Connection Denied
#### <a name="problem"></a>Проблема
При отладке в Visual Studio отображается ошибка FabricConnectionDeniedException.

#### <a name="solution"></a>Решение
Эта ошибка обычно возникает при попытке вручную запустить хост-процесс службы.

Убедитесь, что в решении нет проектов служб, настроенных в качестве запускаемых проектов. В качестве запускаемых проектов можно настраивать только проекты приложений Service Fabric.

> [!TIP]
> Если после завершения программы установки у локального кластера наблюдается нетипичное поведение, такой кластер можно сбросить с помощью приложения панели задач диспетчера локального кластера. Будет удален существующий кластер и настроен новый. Учтите, что все развернутые приложения и связанные данные будут удалены.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* [Обзор и диагностика кластера с помощью системных отчетов о работоспособности](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)


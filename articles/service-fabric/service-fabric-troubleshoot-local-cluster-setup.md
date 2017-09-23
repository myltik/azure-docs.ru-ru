---
title: "Устранение неполадок в настройке локального кластера Service Fabric | Документация Майкрософт"
description: "В этой статье описываются рекомендации по устранению неполадок с кластером локальной разработки."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: aa393f884b564cee81fcf75cc2eff895efea9471
ms.contentlocale: ru-ru
ms.lasthandoff: 06/09/2017

---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Устранение неполадок в работе кластера локальной разработки
Если у вас возникли проблемы при взаимодействии с кластером локальной разработки Azure Service Fabric, ознакомьтесь со следующими возможностями для решения.

## <a name="cluster-setup-failures"></a>Ошибки настройки кластера
### <a name="cannot-clean-up-service-fabric-logs"></a>Не удается очистить журналы Service Fabric
#### <a name="problem"></a>Проблема
При выполнении сценария DevClusterSetup появляется следующее сообщение об ошибке:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Решение
Закройте текущее и откройте новое окно PowerShell от имени администратора. Теперь можно успешно запустить сценарий.

## <a name="cluster-connection-failures"></a>Ошибки подключения к кластеру
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Командлеты Service Fabric PowerShell не распознаются в Azure PowerShell
#### <a name="problem"></a>Проблема
Попытка запустить командлеты Service Fabric PowerShell, например `Connect-ServiceFabricCluster` в окне Azure PowerShell, не удается, с сообщением о том, что командлет не распознан. Причина в том, что Azure PowerShell использует 32-разрядную версию Windows PowerShell (даже на 64-разрядной версии ОС), а командлеты Service Fabric работают только в 64-разрядной среде.

#### <a name="solution"></a>Решение
Всегда запускайте командлеты Service Fabric непосредственно из Windows PowerShell.

> [!NOTE]
> Последняя версия Azure PowerShell не создает специальный ярлык, поэтому так больше не должно происходить.
> 
> 

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
Закройте текущее и откройте новое окно PowerShell от имени администратора. Теперь можно успешно подключиться.

### <a name="fabric-connection-denied-exception"></a>Исключение Fabric Connection Denied
#### <a name="problem"></a>Проблема
При отладке в Visual Studio отображается ошибка FabricConnectionDeniedException.

#### <a name="solution"></a>Решение
Эта ошибка обычно возникает, если вы пытаетесь запустить процесс узла службы вручную, а не указываете среде выполнения Service Fabric запустить его автоматически.

Убедитесь, что в решении нет проектов служб, настроенных в качестве запускаемых проектов. В качестве запускаемых проектов можно настраивать только проекты приложений Service Fabric.

> [!TIP]
> Если после завершения программы установки у локального кластера наблюдается нетипичное поведение, такой кластер можно сбросить с помощью приложения панели задач диспетчера локального кластера. Будет удален существующий кластер и настроен новый. Учтите, что все развернутые приложения и связанные данные будут удалены.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор и диагностика кластера с помощью системных отчетов о работоспособности](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)



<properties
   pageTitle="Устранение неполадок в настройке кластера локальной службы Service Fabric | Microsoft Azure"
   description="В этой статье описываются рекомендации по устранению неполадок с кластером локальной разработки."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# Устранение неполадок в работе кластера локальной разработки

Если у вас возникли проблемы при взаимодействии с кластером локальной разработки Azure Service Fabric, ознакомьтесь со следующими возможностями для решения.

## Ошибки настройки кластера

### Не удается очистить журналы Service Fabric

#### Проблема

При выполнении сценария DevClusterSetup появляется следующее сообщение об ошибке:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### Решение

Закройте текущее и откройте новое окно PowerShell от имени администратора. Теперь можно успешно запустить сценарий.

## Ошибки подключения к кластеру

### Командлеты Service Fabric PowerShell не распознаются в Azure PowerShell

#### Проблема

Попытка запустить командлеты Service Fabric PowerShell, например `Connect-ServiceFabricCluster` в окне Azure PowerShell, не удается, с сообщением о том, что командлет не распознан. Причина в том, что Azure PowerShell использует 32-разрядную версию Windows PowerShell (даже на 64-разрядной версии ОС), а командлеты Service Fabric работают только в 64-разрядной среде.

#### Решение

Всегда запускайте командлеты Service Fabric непосредственно из Windows PowerShell.

>[AZURE.NOTE] Последняя версия Azure PowerShell не создает специальный ярлык, поэтому так больше не должно происходить.

### Исключение типа инициализации

#### Проблема

При подключении к кластеру в PowerShell отображается ошибка TypeInitializationException для System.Fabric.Common.AppTrace.

#### Решение

При установке была неправильно настроена переменная пути. Выйдите из Windows и снова выполните вход. Путь будет полностью обновлен.

### Сбой подключения к кластеру с сообщением об ошибке "Объект закрыт"

#### Проблема

Вызов Connect-ServiceFabricCluster завершается со следующей ошибкой:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### Решение

Закройте текущее и откройте новое окно PowerShell от имени администратора. Теперь можно успешно подключиться.

### Исключение Fabric Connection Denied

#### Проблема

При отладке в Visual Studio отображается ошибка FabricConnectionDeniedException.

#### Решение

Эта ошибка обычно возникает, если вы пытаетесь запустить процесс узла службы вручную, а не указываете среде выполнения Service Fabric запустить его автоматически.

Убедитесь, что в решении нет проектов служб, настроенных в качестве запускаемых проектов. В качестве запускаемых проектов можно настраивать только проекты приложений Service Fabric.

>[AZURE.TIP] Если после завершения программы установки у локального кластера наблюдается нетипичное поведение, такой кластер можно сбросить с помощью приложения панели задач диспетчера локального кластера. Будет удален существующий кластер и настроен новый. Учтите, что все развернутые приложения и связанные данные будут удалены.

## Дальнейшие действия

- [Обзор и диагностика кластера с помощью системных отчетов о работоспособности](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)

<!---HONumber=AcomDC_0713_2016-->
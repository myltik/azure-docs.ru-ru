<properties
   pageTitle="Устранение неполадок в работе локального кластера"
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
   ms.date="04/22/2015"
   ms.author="seanmck"/>

# Устранение неполадок в работе кластера локальной разработки

Если у вас возникли проблемы при взаимодействии с кластером локальной разработки, ознакомьтесь со следующими возможностями для решения.

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

### Исключение типа инициализации

#### Проблема

При подключении к кластеру в PowerShell или обозревателе Service Fabric отображается ошибка TypeInitializationException для System.Fabric.Common.AppTrace.

#### Решение

При установке была неправильно настроена переменная пути. Выйдите из Windows и войдите снова. Путь будет полностью обновлен.

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

### FabricConnectionDeniedException

#### Проблема

При отладке в Visual Studio отображается ошибка FabricConnectionDeniedException.

#### Решение

Эта ошибка обычно возникает, если вы пытаетесь запустить процесс узла службы вручную, а не указываете среде выполнения Service Fabric запустить его автоматически.

Убедитесь, что в решении нет проектов служб, настроенных в качестве запускаемых проектов. В качестве запускаемых проектов можно настраивать только проекты приложений Service Fabric.


## Дальнейшие действия

- [Обзор и диагностика кластера с помощью системных отчетов о работоспособности](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)
 

<!---HONumber=July15_HO2-->
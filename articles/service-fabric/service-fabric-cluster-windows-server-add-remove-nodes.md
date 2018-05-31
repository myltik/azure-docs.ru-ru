---
title: Добавление узлов к автономному кластеру Service Fabric или удаление узлов из него | Документация Майкрософт
description: Узнайте, как добавлять узлы в кластер Azure Service Fabric или удалять их из него на физическом или виртуальном компьютере под управлением Windows Server, расположенном в локальной системе или в любом облаке.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: b1ffe3510cf4f5e73b05572a482f49d529fca60d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212550"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него
После [создания автономного кластера Service Fabric на компьютерах под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md) потребности (бизнес-потребности) компании могут измениться, и вам нужно будет добавить или удалить несколько узлов в кластере. В данной статье содержатся детальные инструкции по выполнению этой задачи. Обратите внимание, что добавление и удаление узлов в кластерах локальной разработки не поддерживается.

## <a name="add-nodes-to-your-cluster"></a>Добавление узлов в кластер

1. Подготовьте виртуальную машину или компьютер, который вы хотите добавить в кластер, выполнив действия, описанные в статье [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md)
2. Определите, в какой домен сбоя и домен обновления нужно добавить эту виртуальную машину или компьютер.
3. Подключитесь к виртуальной машине или компьютеру, который нужно добавить в кластер, с помощью удаленного рабочего стола.
4. Скопируйте или [скачайте изолированный пакет для Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) на виртуальную машину или компьютер и извлеките его содержимое.
5. Запустите PowerShell с более высоким уровнем привилегий и перейдите к распакованному пакету.
6. Запустите скрипт *AddNode.ps1*, указав параметры, описывающие новый узел, который будет добавлен. В этом примере добавляется новый узел с именем VM5, типом NodeType0, IP-адресом 182.17.34.52 и UD1 со значением fd:/dc1/r0. *ExistingClusterConnectionEndPoint* — это конечная точка подключения для узла в имеющемся кластере, который может содержать IP-адрес *любого* узла в этом кластере.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    После выполнения скрипта можно проверить, добавлен ли новый узел, выполнив командлет [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps).

7. Чтобы обеспечить согласованность на различных узлах в кластере, нужно обновить конфигурацию. Выполните команду [Get- ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps), чтобы получить самый последний файл конфигурации, и добавьте вновь добавленный узел в раздел узлов. Кроме того, мы рекомендуем всегда иметь под рукой последнюю конфигурацию кластера, если вам понадобится развернуть кластер с той же конфигурацией.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Выполните команду [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps), чтобы начать обновление.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Ход выполнения обновления можно отслеживать с помощью Service Fabric Explorer. Кроме того, с этой же целью можно выполнить команду [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Добавление узлов в кластеры, настроенные с помощью Windows Security, с использованием gMSA
Для кластеров, настроенных с помощью групповой управляемой учетной записи службы (https://technet.microsoft.com/library/hh831782.aspx)), можно добавить новый узел, используя обновление конфигурации.
1. Выполните команду [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) на любом из имеющихся узлов, чтобы получить самый последний файл конфигурации, и добавьте сведения о новом узле, который нужно добавить в раздел узлов. Убедитесь, что новый узел входит в ту же учетную запись, которой управляет группа. Этой учетной записи нужно назначить роль "Администратор" на всех компьютерах.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Выполните команду [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps), чтобы начать обновление.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Ход выполнения обновления можно отслеживать с помощью Service Fabric Explorer. Кроме того, с этой же целью можно выполнить команду [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="add-node-types-to-your-cluster"></a>Добавление типов узлов в кластер
Чтобы добавить новый тип узла, измените конфигурацию, чтобы добавить новый тип узла в разделе NodeTypes в области Properties, и начните обновление конфигурации, используя [ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). После завершения обновления в кластер можно добавить новые узлы этого типа.

## <a name="remove-nodes-from-your-cluster"></a>Удаление узлов из кластера
Вы можете удалить узел из кластера в ходе обновления конфигурации следующим образом:

1. Выполните команду [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps), чтобы получить последний файл конфигурации, и *удалите* узел из раздела узлов.
Добавьте параметр NodesToBeRemoved в раздел Setup в разделе FabricSettings. В качестве значений следует использовать разделенный запятыми список узлов, которые необходимо удалить.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Выполните команду [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps), чтобы начать обновление.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Ход выполнения обновления можно отслеживать с помощью Service Fabric Explorer. Кроме того, с этой же целью можно выполнить команду [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

> [!NOTE]
> При удалении узлов может выполниться несколько обновлений. Некоторые узлы будут отмечены тегом `IsSeedNode=”true”`. Их можно определить, запросив манифест кластера с использованием `Get-ServiceFabricClusterManifest`. Удаление таких узлов может занимать больше времени, чем других, так как в таких сценариях начальные узлы придется перемещать. Кластер должен поддерживать как минимум 3 первичных узла.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Удаление типов узлов из кластера
Прежде чем удалить тип узла, перепроверьте, нет ли узлов, которые ссылаются на этот тип. Перед удалением соответствующего типа узла удалите эти узлы. После удаления всех соответствующих узлов NodeType можно удалить из конфигурации кластера и начать обновление конфигурации с помощью [ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Замена основных узлов кластера
Замену основных узлов следует выполнять последовательно, вместо того чтобы удалять, а затем добавлять их массово.


## <a name="next-steps"></a>Дополнительная информация
* [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md)
* [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)
* [Create a three node standalone Service Fabric cluster with Azure virtual machines running Windows Server (Создание автономного кластера Service Fabric с тремя узлами на виртуальных машинах Azure под управлением Windows)](service-fabric-cluster-creation-with-windows-azure-vms.md)


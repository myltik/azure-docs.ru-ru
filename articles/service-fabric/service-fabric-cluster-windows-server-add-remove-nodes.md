---
title: "Добавление узлов к автономному кластеру Service Fabric или удаление узлов из него | Документация Майкрософт"
description: "Узнайте, как добавлять узлы в кластер Azure Service Fabric или удалять их из него на физическом или виртуальном компьютере под управлением Windows Server, расположенном в локальной системе или в любом облаке."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: af121309be44852ee51f34130330533adf19d586
ms.openlocfilehash: 68474b24519a46db71fe59b5d0574cc4700efccb
ms.lasthandoff: 02/16/2017


---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него
После [создания автономного кластера Service Fabric на компьютерах под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md)потребности компании могут измениться, и вам может понадобиться добавить или удалить несколько узлов в кластере. Данная статья содержит подробные инструкции по выполнению этой задачи.

## <a name="add-nodes-to-your-cluster"></a>Добавление узлов в кластер
1. Подготовьте виртуальную машину или компьютер, который необходимо добавить в кластер, выполнив действия, описанные в разделе [Шаг&2;. Подготовка компьютеров для соблюдения предварительных требований](service-fabric-cluster-creation-for-windows-server.md) .
2. Решите, в какой домен сбоя и домен обновления нужно добавить эту виртуальную машину или компьютер.
3. Подключитесь к виртуальной машине или компьютеру, который нужно добавить в кластер, с помощью удаленного рабочего стола.
4. Скопируйте или [скачайте изолированный пакет для Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) на виртуальную машину или компьютер и извлеките его содержимое.
5. Запустите PowerShell от имени администратора и перейдите к расположению распакованного пакета.
6. Запустите файл Powershell *AddNode.ps1* , указав параметры, описывающие новый узел для добавления. В этом примере добавляется новый узел с именем VM5, типом NodeType0, IP-адресом 182.17.34.52 и UD1 со значением fd:/dc1/r0. *ExistingClusterConnectionEndPoint* — это конечная точка подключения для узла в существующем кластере. Для этой конечной точки можно выбрать IP-адрес *любого* узла в кластере.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA

```
Можно проверить, добавлен ли новый узел, выполнив командлет [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode).


## <a name="remove-nodes-from-your-cluster"></a>Удаление узлов из кластера
В зависимости от уровня надежности, выбранного для кластера, вы не сможете удалить первые n (3,&5;,&7;,&9;) узлов основного типа. Кроме того, обратите внимание, что выполнение команды RemoveNode в кластере разработки не поддерживается.

1. Подключитесь к виртуальной машине или компьютеру, который нужно удалить из кластера, с помощью удаленного рабочего стола.
2. Скопируйте или [скачайте изолированный пакет для Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) на виртуальную машину или компьютер и извлеките его содержимое.
3. Запустите PowerShell от имени администратора и перейдите к расположению распакованного пакета.
4. Запустите файл *RemoveNode.ps1* в PowerShell. В приведенном ниже примере текущий узел удаляется из кластера. *ExistingClientConnectionEndpoint* — это конечная точка подключения клиента для любого узла, который останется в кластере. Выберите IP-адрес и порт конечной точки *любого* **другого узла** в кластере. Этот **другой узел**, в свою очередь, обновит конфигурацию кластера для удаленного узла. 

```

.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000

```

> [!NOTE]
> Некоторые узлы не могут быть удалены из-за зависимостей системных служб. Это первичные узлы. Чтобы определить их, следует запросить манифест кластера с помощью `Get-ServiceFabricClusterManifest` и найти записи об узлах со значением `IsSeedNode=”true”`. 
> 
> 

Даже после удаления узел может отображаться как неработающий в запросах и SFX. Это известный дефект. Эта ошибка будет устранена в следующем выпуске. 


## <a name="remove-node-types-from-your-cluster"></a>Удаление типов узлов из кластера
При удалении типа узла необходимо быть особо осторожными. Прежде чем удалить тип узла, перепроверьте, нет ли узла, который ссылается на этот тип.


## <a name="replace-primary-nodes-of-your-cluster"></a>Замена основных узлов кластера
Замену основных узлов следует выполнять последовательно, вместо того чтобы удалять, а затем добавлять их массово.


## <a name="next-steps"></a>Дальнейшие действия
* [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md)
* [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)
* [Create a three node standalone Service Fabric cluster with Azure virtual machines running Windows Server (Создание автономного кластера Service Fabric с тремя узлами на виртуальных машинах Azure под управлением Windows)](service-fabric-cluster-creation-with-windows-azure-vms.md)



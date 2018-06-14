---
title: Программное масштабирование с помощью Azure Service Fabric | Документация Майкрософт
description: Программное уменьшение и увеличение масштаба кластера Azure Service Fabric в соответствии с пользовательскими триггерами
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: dcf4721012fb8ec39bcd1de02c294747357b3539
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213067"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Программное масштабирование кластера Service Fabric 

В основе кластеров Service Fabric кластеры, запущенных в Azure, лежат масштабируемые наборы виртуальных машин.  Вы можете узнать, как выполнять [масштабирование кластеров](./service-fabric-cluster-scale-up-down.md) вручную или с помощью правил автомасштабирования. В этой статье описывается, как управлять учетными данными и масштабировать кластер с помощью свободного пакета SDK для вычислений Azure, что является более сложным сценарием. См. дополнительные сведения о [программных методах выполнения операций](service-fabric-cluster-scaling.md#programmatic-scaling). 

## <a name="manage-credentials"></a>Управление учетными данными
Одна из проблем написания службы для обработки масштабирования заключается в том, что служба должна иметь возможность доступа к ресурсам масштабируемого набора виртуальных машин без интерактивного входа в систему. Доступ к кластеру Service Fabric прост, если служба масштабирования изменяет собственное приложение Service Fabric, но для доступа к масштабируемому набору нужны учетные данные. Для входа в систему можно использовать [субъект-службу](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli), созданный с помощью [Azure CLI 2.0](https://github.com/azure/azure-cli).

Чтобы создать субъект-службу, необходимо выполнить следующие действия:

1. Войдите в интерфейс командной строки Azure (`az login`) как пользователь с правом доступа к масштабируемому набору виртуальных машин.
2. Создайте субъект-службу с помощью `az ad sp create-for-rbac`.
    1. Запишите идентификатор приложения (другое название — идентификатор клиента), имя, пароль и клиент для последующего использования.
    2. Также вам понадобится идентификатор подписки, который можно просмотреть с помощью `az account list`.

Свободная библиотека вычислений может войти в систему, используя эти учетные данные, следующим образом (обратите внимание, что основные типы свободных библиотек Azure, например `IAzure`, расположены в пакете [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

После входа в систему можно запросить подсчет экземпляров масштабируемого набора с помощью `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Развертывание
Используя свободный пакет SDK вычислений для Azure, можно добавлять экземпляры в масштабируемый набор виртуальных машин с помощью всего нескольких вызовов:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Кроме того, размер масштабируемого набора виртуальных машин можно изменить с помощью командлетов PowerShell. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) может извлечь объект масштабируемого набора виртуальных машин. Текущая емкость указывается в свойстве `.sku.capacity`. После того как для емкости будет задано нужное значение, вы сможете обновить масштабируемый набор виртуальных машин в Azure, выполнив команду [`Update-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

Как и при добавлении узла вручную, добавления экземпляра масштабируемого набора должно быть достаточно для запуска нового узла Service Fabric, так как шаблон масштабируемого набора включает расширения для автоматического присоединения новых экземпляров к кластеру Service Fabric. 

## <a name="scaling-in"></a>Уменьшение масштаба

Уменьшение масштаба похоже на его увеличение. Изменения масштабируемого набора виртуальных машин практически ничем не отличаются. Но, как было описано ранее, служба Service Fabric автоматически очищает только удаленные узлы с уровнем устойчивости Gold или Silver. Таким образом, при уменьшении масштаба на узле с уровнем устойчивости Bronze необходимо взаимодействовать с кластером Service Fabric для завершения работы узла, который нужно удалить, а затем удалить его состояние.

Подготовка узла к завершению работы предусматривает поиск узла, который нужно удалить (последний добавленный экземпляр масштабируемого набора виртуальных машин), и его отключение. Экземпляры масштабируемого набора виртуальных машин нумеруются в порядке, в котором они добавляются. Поэтому новые узлы можно находить, сравнивая числовые суффиксы в именах узлов (которые соответствует именам базовых экземпляров масштабируемого набора виртуальных машин). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

После обнаружения узла, который следует удалить, его можно отключить и удалить с помощью того же экземпляра `FabricClient` и экземпляра `IAzure` более ранней версии.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Как и при развертывании, можно использовать командлеты PowerShell для изменения емкости масштабируемого набора виртуальных машин, если подход с использованием скриптов предпочтительнее. После удаления экземпляра виртуальной машины можно удалить состояние узла Service Fabric.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы внедрить собственную логику автомасштабирования, ознакомьтесь со следующими понятиями и полезными API-интерфейсами:

- [Масштабирование кластера Service Fabric с помощью правил автомасштабирования](./service-fabric-cluster-scale-up-down.md)
- [Свободные библиотеки управления Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (для взаимодействия с базовыми масштабируемыми наборами виртуальных машин для кластеров Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (для взаимодействия с кластером Service Fabric и его узлами)

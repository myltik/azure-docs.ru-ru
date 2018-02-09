---
title: "Программное масштабирование с помощью Azure Service Fabric | Документация Майкрософт"
description: "Программное уменьшение и увеличение масштаба кластера Azure Service Fabric в соответствии с пользовательскими триггерами"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: bfa020e29a9bb67f0634d220725bc11279e1565c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Программное масштабирование кластера Service Fabric 

Основы масштабирования кластера Service Fabric в Azure рассматриваются в статье [Масштабирование кластера Service Fabric с помощью правил автомасштабирования](./service-fabric-cluster-scale-up-down.md). В этой статье описано устройство кластеров Service Fabric, состоящих из масштабируемых наборов виртуальных машин. Масштабирование этих кластеров может выполняться вручную или с помощью правил автомасштабирования. В текущем документе рассматриваются программные методы выполнения операций масштабирования Azure для более сложных сценариев. 

## <a name="reasons-for-programmatic-scaling"></a>Причины для программного масштабирования
В большинстве сценариев масштабирование вручную или с помощью правил автомасштабирования — это оптимальное решение. Однако в некоторых ситуациях такие подходы неприемлемы. К их потенциальным недостаткам относятся следующие:

- При масштабировании вручную необходимо войти в систему и явным образом запросить операции масштабирования. Если операции масштабирования нужно выполнять часто или в непредвиденные моменты, этот подход может оказаться не лучшим решением.
- Если правила автомасштабирования удаляют экземпляр из масштабируемого набора виртуальных машин, они не удаляют автоматически сведения об этом узле из связанного кластера Service Fabric, за исключением случаев, когда тип узла имеет уровень устойчивости Silver или Gold. Правила автомасштабирования работают на уровне масштабируемого набора (а не на уровне Service Fabric), поэтому они могут удалять узлы Service Fabric без корректного завершения их работы. При таком грубом удалении узла остается фантомное состояние узла Service Fabric после операций уменьшения масштаба. Ответственное лицо (или служба) должно время от времени очищать состояние удаленных узлов из кластера Service Fabric.
  - Тип узла с уровнем надежности Gold или Silver автоматически очищает удаленные узлы, поэтому дополнительная очистка не требуется.
- Правила автомасштабирования поддерживают [многие метрики](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md), но их число все еще ограниченно. Если в вашем сценарии требуется масштабирование на основе метрики, которой нет в этом наборе, правила автомасштабирования могут оказаться не самым подходящим вариантом.

С учетом этих ограничений вам может потребоваться реализовать модели автомасштабирования с дополнительными возможностями настройки. 

## <a name="scaling-apis"></a>Масштабирование API-интерфейсов
Существуют API-интерфейсы Azure, позволяющие приложениям программно управлять масштабируемыми наборами виртуальных машин и кластерами Service Fabric. Если существующие параметры автомасштабирования не выполняются для вашего сценария, эти API-интерфейсы позволяют реализовать настраиваемую логику масштабирования. 

Один из способов реализации этой "самодельной" функции автомасштабирования заключается в добавлении новой службы без отслеживания состояния в приложение Service Fabric для управления масштабированием. В рамках метода `RunAsync` службы набор триггеров может определить, требуется ли масштабирование (включая проверку таких параметров, как максимальный размер кластера и время ожидания следующей операции масштабирования).   

API-интерфейс, использующийся для взаимодействий в масштабируемом наборе виртуальных машин (для проверки и изменения текущего количества экземпляров виртуальных машин), является свободной [библиотекой вычислений для управления Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Свободная библиотека вычислений предоставляет простой в использовании API-интерфейс для взаимодействия с масштабируемыми наборами виртуальных машин.

Для взаимодействия с кластером Service Fabric используется класс [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Очевидно, что код масштабирования не должен выполняться как служба в кластере, который нужно масштабировать. Как `IAzure`, так и `FabricClient` могут удаленно подключаться к связанным ресурсам Azure, поэтому службой масштабирования вполне может быть консольное приложение или служба Windows, которая выполняется за пределами приложения Service Fabric. 

## <a name="credential-management"></a>Управление учетными данными
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

## <a name="potential-drawbacks"></a>Потенциальные недостатки

Как показано в предыдущих фрагментах кода, создание собственной службы масштабирования обеспечивает максимальный уровень контроля и настройки через поведение масштабирования приложения. Это может быть полезно в сценариях, в которых необходим точный контроль над временем или способом изменения масштаба приложения. Тем не менее этот элемент управления отличается сложностью кода. Для использования такого подхода нужен собственный нестандартный код масштабирования.

Подход к масштабированию Service Fabric зависит от сценария. Если масштабирование используется нечасто, возможности добавлять и удалять узлы вручную будет вполне достаточно. В более сложных сценариях используйте правила автомасштабирования и пакеты SDK, предоставляющие широкие возможности программного масштабирования.

## <a name="next-steps"></a>Дополнительная информация

Чтобы внедрить собственную логику автомасштабирования, ознакомьтесь со следующими понятиями и полезными API-интерфейсами:

- [Масштабирование кластера Service Fabric с помощью правил автомасштабирования](./service-fabric-cluster-scale-up-down.md)
- [Свободные библиотеки управления Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (для взаимодействия с базовыми масштабируемыми наборами виртуальных машин для кластеров Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (для взаимодействия с кластером Service Fabric и его узлами)

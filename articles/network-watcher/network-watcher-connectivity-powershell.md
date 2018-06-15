---
title: Устранение неполадок подключений с помощью службы "Наблюдатель за сетями Azure" в PowerShell | Документация Майкрософт
description: Узнайте, как использовать функцию устранения неполадок подключений службы "Наблюдатель за сетями Azure" с помощью PowerShell.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 7e8c04fd2284a3a00d4847f39fd34982a543cc29
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181867"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-powershell"></a>Устранение неполадок подключений с помощью службы "Наблюдатель за сетями Azure" с помощью PowerShell

> [!div class="op_single_selector"]
> - [Портал](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Узнайте, как проверить возможность прямого подключения TCP между виртуальной машиной и определенной конечной точкой с помощью функции устранения неполадок подключения.

## <a name="before-you-begin"></a>Перед началом работы

* Экземпляр службы "Наблюдатель за сетями" в регионе, в котором нужно устранить проблему подключения.
* Виртуальные машины, на которых требуется устранить неполадки подключения.

> [!IMPORTANT]
> Чтобы устранить неполадки подключения на виртуальной машине, установите на ней расширение `AzureNetworkWatcherExtension`. Информацию об установке расширения для виртуальной машины Windows см. в статье [Расширение виртуальной машины агента Наблюдателя за сетями для Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), а для виртуальной машины Linux — в статье [Расширение виртуальной машины агента Наблюдателя за сетями для Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Эта расширение не нужно устанавливать на целевой конечной точке.

## <a name="check-connectivity-to-a-virtual-machine"></a>Проверка возможности подключения к виртуальной машине

В этом примере проверяется возможность подключения к целевой виртуальной машине через порт 80. Для этого примера регион, в котором находится исходная виртуальная машина, должна поддерживать служба "Наблюдатель за сетями".  

### <a name="example"></a>Пример

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"
$destVMName = "Database0"

$RG = Get-AzureRMResourceGroup -Name $rgName

$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName
$VM2 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $destVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location} 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationId $VM2.Id -DestinationPort 80
```

### <a name="response"></a>Ответ

Следующий ответ взят из предыдущего примера.  В этом ответе параметр `ConnectionStatus` имеет значение **Unreachable** (Недоступно). Как видите, все отправленные пробы завершились неудачей. Попытка подключения завершилась сбоем в виртуальном модуле из-за пользовательского правила `NetworkSecurityRule` с именем **UserRule_Port80**, настроенного на блокировку входящего трафика на порту 80. Эти сведения можно использовать для анализа проблем с подключением.

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "0f1500cd-c512-4d43-b431-7267e4e67017",
                       "Address": "10.1.3.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/auNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "a88940f8-5fbe-40da-8d99-1dee89240f64"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "NetworkSecurityRule",
                           "Context": [
                             {
                               "key": "RuleName",
                               "value": "UserRule_Port80"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "VnetLocal",
                       "Id": "a88940f8-5fbe-40da-8d99-1dee89240f64",
                       "Address": "10.1.4.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/dbNic0/ipConfigurati
                   ons/ipconfig1",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="validate-routing-issues"></a>Проверка проблем с маршрутизацией

В этом примере проверяется возможность подключения между виртуальной машиной и удаленной конечной точкой. Для этого примера регион, в котором находится исходная виртуальная машина, должна поддерживать служба "Наблюдатель за сетями".  

### <a name="example"></a>Пример

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzureRMResourceGroup -Name $rgName
$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress 13.107.21.200 -DestinationPort 80
```

### <a name="response"></a>Ответ

В следующем примере состояние `ConnectionStatus` отображается как **Unreachable** (Недоступно). В блоке `Hops` в разделе `Issues` видно, что трафик заблокирован из-за `UserDefinedRoute`. 

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "b4f7bceb-07a3-44ca-8bae-adec6628225f",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "3fee8adf-692f-4523-b742-f6fdf6da6584"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "UserDefinedRoute",
                           "Context": [
                             {
                               "key": "RouteType",
                               "value": "User"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "Destination",
                       "Id": "3fee8adf-692f-4523-b742-f6fdf6da6584",
                       "Address": "13.107.21.200",
                       "ResourceId": "Unknown",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-website-latency"></a>Проверка задержки веб-сайта

В следующем примере проверяется возможность подключения к веб-сайту. Для этого примера регион, в котором находится исходная виртуальная машина, должна поддерживать служба "Наблюдатель за сетями".  

### <a name="example"></a>Пример

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzureRMResourceGroup -Name $rgName
$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName


Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress http://bing.com/
```

### <a name="response"></a>Ответ

В следующем ответе видно, что параметр `ConnectionStatus` отображается со значением **Reachable** (Достижимо). Когда подключение будет установлено, отобразятся значения задержки.

```
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 7
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "1f0e3415-27b0-4bf7-a59d-3e19fb854e3e",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0",
                       "Address": "204.79.197.200",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Проверка возможности подключения к конечной точке хранилища

В следующем примере проверяется возможность подключения с виртуальной машины к учетной записи хранилища BLOB-объектов. Для этого примера регион, в котором находится исходная виртуальная машина, должна поддерживать служба "Наблюдатель за сетями".  

### <a name="example"></a>Пример

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzureRMResourceGroup -Name $rgName

$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://contosostorageexample.blob.core.windows.net/ 
```

### <a name="response"></a>Ответ

JSON-код ниже — это пример ответа на предыдущий командлет. Так как назначение доступно, для свойства `ConnectionStatus` отображается значение **Reachable** (Доступно).  Также отображаются сведения о числе прыжков, необходимых для доступа к BLOB-объекту в хранилище, а также о задержке.

```json
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 8
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "9e7f61d9-fb45-41db-83e2-c815a919b8ed",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "1e6d4b3c-7964-4afd-b959-aaa746ee0f15"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "1e6d4b3c-7964-4afd-b959-aaa746ee0f15",
                       "Address": "13.71.200.248",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о состоянии (разрешен или запрещен) входящего и исходящего трафика виртуальной машины см. в статье, посвященной [проверке потока IP-адресов](diagnose-vm-network-traffic-filtering-problem.md).

Если трафик блокируется, чего не должно быть, см. статью [Управление группами безопасности сети с помощью портала](../virtual-network/manage-network-security-group.md). В ней содержатся сведения об отслеживании группы безопасности сети и определенных правил безопасности.
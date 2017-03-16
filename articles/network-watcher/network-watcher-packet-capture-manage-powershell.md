---
title: "Наблюдатель за сетями Azure: управление записью пакетов с помощью PowerShell | Документация Майкрософт"
description: "На этой странице объясняется, как управлять функцией записи пакетов службы наблюдения за сетями с помощью PowerShell"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d2a65104743d9497debdc85c134fd1a06114c514
ms.openlocfilehash: 1ecd1cd12a5c8b62ee3c8623bd38431879546760
ms.lasthandoff: 02/23/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Управление записью пакетов с помощью Наблюдателя за сетями Azure в PowerShell

> [!div class="op_single_selector"]
> - [Портал Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](network-watcher-packet-capture-manage-cli.md)
> - [REST API](network-watcher-packet-capture-manage-rest.md)

Возможность записи пакетов в службе наблюдения за сетями (Наблюдатель за сетями) позволяет создавать сеансы записи для отслеживания входящего и исходящего трафика виртуальной машины. Для сеанса записи доступны фильтры, с помощью которых можно убедиться, что записывается только требуемый трафик. Функция записи пакетов помогает выявлять аномалии в работе сети по факту или заранее. Она также помогает выполнять сбор сетевой статистики, получать сведения о сетевых вторжениях, выполнять отладку передачи данных между клиентом и сервером и многое другое. Так как запись пакетов активируется удаленно, ее не нужно запускать вручную. К тому же она сразу выполняется на требуемой виртуальной машине, что также позволяет сэкономить ваше время.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

Из этой статьи вы узнаете о разных задачах управления, доступных сейчас для функции записи пакетов.

- [**Запуск записи пакета**](#start-a-packet-capture)
- [**Прекращение записи пакета**](#stop-a-packet-capture)
- [**Удаление записи пакета**](#delete-a-packet-capture)
- [**Скачивание записи пакета**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть следующие ресурсы:

* экземпляр Наблюдателя за сетями в регионе, в котором нужно создать запись пакетов;

* виртуальная машина с включенным расширением для записи пакетов.

> [!IMPORTANT]
> Для записи пакетов необходимо расширение виртуальной машины `AzureNetworkWatcherExtension`. Информацию об установке расширения для виртуальной машины Windows см. в статье [Расширение виртуальной машины агента Наблюдателя за сетями для Windows](../virtual-machines/virtual-machines-windows-extensions-nwa.md), а для виртуальной машины Linux — в статье [Расширение виртуальной машины агента Наблюдателя за сетями для Linux](../virtual-machines/virtual-machines-linux-extensions-nwa.md).

## <a name="install-vm-extension"></a>Установка расширения виртуальной машины

### <a name="step-1"></a>Шаг 1

```powershell
$VM = Get-AzureRmVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Шаг 2

В следующем примере извлекается информация о расширении, необходимом для выполнения командлета `Set-AzureRmVMExtension`. Этот командлет устанавливает агент записи пакетов на гостевой виртуальной машине.

> [!NOTE]
> Для завершения командлетом `Set-AzureRmVMExtension` этой операции может потребоваться несколько минут.

Для виртуальных машин Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Для виртуальных машин Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
````

В следующем примере приведен успешный ответ после выполнения командлета `Set-AzureRmVMExtension`.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Шаг 3.

Чтобы убедиться, что агент установлен, выполните командлет `Get-AzureRmVMExtension` и передайте ему имя виртуальной машины и расширения.

```powershell
Get-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Ниже приведен пример ответа после выполнения `Get-AzureRmVMExtension`.

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Запуск записи пакета

После выполнения предыдущих шагов на виртуальной машине будет установлен агент записи пакетов.

### <a name="step-1"></a>Шаг 1

Далее необходимо извлечь экземпляр службы наблюдения за сетями. Эта переменная передается в командлет `New-AzureRmNetworkWatcherPacketCapture` на шаге 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>Шаг 2

Получите учетную запись хранения. Она используется для хранения файла записи пакетов.

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Шаг 3.

С помощью фильтров можно ограничить данные, которые сохраняются при записи пакетов. В следующем примере устанавливаются два фильтра.  Один фильтр собирает исходящий TCP-трафик только с локального IP-адреса 10.0.0.3 на порты назначения 20, 80 и 443.  Второй фильтр собирает только трафик, передаваемый по протоколу UDP.

```powershell
$filter1 = New-AzureRmPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzureRmPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Для записи пакетов можно определить несколько фильтров.

### <a name="step-4"></a>Шаг 4.

Выполните командлет `New-AzureRmNetworkWatcherPacketCapture`, чтобы начать процесс записи пакетов, передав требуемые значения, полученные на предыдущих шагах.
```powershell

New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filters $filter1, $filter2
```

Ниже приведен пример ожидаемого результата выполнения командлета `New-AzureRmNetworkWatcherPacketCapture`.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Получение записи пакета

При выполнении командлета `Get-AzureRmNetworkWatcherPacketCapture` вы получаете сведения о состоянии выполняющейся или завершенной записи пакетов.

```powershell
Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Ниже представлен пример выходных данных командлета `Get-AzureRmNetworkWatcherPacketCapture`, полученных после завершения записи пакетов. В качестве значения параметра PacketCaptureStatus указано Stopped, а для параметра StopReason — значение TimeExceeded. По этому значению можно понять, что запись пакетов выполнена успешно за требуемое время.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Прекращение записи пакета

Если выполнить командлет `Stop-AzureRmNetworkWatcherPacketCapture` во время сеанса записи пакета, сеанс будет остановлен.

```powershell
Stop-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> При выполнении во время текущего сеанса записи или в имеющемся остановленном сеансе командлет не возвращает ответ.

## <a name="delete-a-packet-capture"></a>Удаление записи пакета

```powershell
Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> При удалении записи пакета файл в учетной записи хранения не удаляется.

## <a name="download-a-packet-capture"></a>Скачивание записи пакета

Когда сеанс записи пакета будет завершен, файл записи можно передать в хранилище BLOB-объектов или в локальный файл на виртуальной машине. Расположение для хранения файлов, созданных при записи пакетов, определяется в ходе создания сеанса. Удобное средство для доступа к этим файлам записи, сохраненным в учетной записи хранения — обозреватель службы хранилища Microsoft Azure, который можно скачать по адресу http://storageexplorer.com/.

Если указать учетную запись хранения, файлы при записи пакетов будут сохраняться в следующем расположении:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об автоматизации записи пакетов с помощью оповещений на виртуальной машине см. в статье, посвященной [созданию записи пакетов, активируемой с использованием оповещений](network-watcher-alert-triggered-packet-capture.md).

Сведения о состоянии (разрешен или запрещен) входящего и исходящего трафика виртуальной машины см. в статье, посвященной [проверке потока IP-адресов](network-watcher-check-ip-flow-verify-portal.md).

<!-- Image references -->
















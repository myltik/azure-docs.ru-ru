---
title: "Проверка трафика с использованием проверки IP-потока с помощью Наблюдателя за сетями Azure (PowerShell) | Документы Майкрософт"
description: "В этой статье описывается, как проверить состояние передачи входящего и исходящего трафика виртуальной машины (разрешен или запрещен) с помощью PowerShell."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 68860006266a60bf8e87f72d8669fb26ed3a5486
ms.lasthandoff: 03/04/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Проверка состояния входящего и исходящего трафика виртуальной машины (разрешен или запрещен) с помощью функции проверки потока IP-адресов службы наблюдения за сетями Azure

> [!div class="op_single_selector"]
> - [Портал Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

Проверка потока IP-адресов — это функция службы наблюдения за сетями (Наблюдатель за сетями), которая позволяет определить состояние трафика виртуальной машины: разрешен он или запрещен. В этом сценарии можно получить сведения о текущем состоянии взаимодействия виртуальной машины с внешним ресурсом или сервером. Проверка потока IP-адресов позволяет убедиться, что правила группы безопасности сети настроены правильно, и устранить неполадки потоков, заблокированных правилами NSG. Такая проверка также гарантирует, что NSG будет соответствующим образом блокировать трафик, который нужно заблокировать.

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии предполагается, что у вас уже есть Наблюдатель за сетями или что вы создали его в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями). Предполагается также, что у вас имеется группа ресурсов с допустимой виртуальной машиной.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Сценарий

В этом сценарии проверка потока IP-адресов используется, чтобы определить, может ли виртуальная машина обратиться к Bing по известному IP-адресу. Если трафик отклоняется, возвращается правило безопасности, блокирующее трафик. Дополнительные сведения о проверке потока IP-адресов см. в [этом обзоре](network-watcher-ip-flow-verify-overview.md).

## <a name="retrieve-network-watcher"></a>Получение Наблюдателя за сетями

Сначала необходимо получить экземпляр Наблюдателя за сетями. Переменная `$networkWatcher` передается в командлет проверки IP-потока.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Получение виртуальной машины

При проверке IP-потока тестируется входящий и исходящий трафик виртуальной машины по IP-адресу в удаленное расположение и из него. Для командлета требуется идентификатор виртуальной машины. Если вы уже знаете идентификатор виртуальной машины, этот шаг можно пропустить.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>Получение сетевых карт

Вам понадобится IP-адрес сетевой карты на виртуальной машине. В этом примере мы получаем сетевые карты на виртуальной машине. Если вы уже знаете IP-адрес, который необходимо протестировать на виртуальной машине, этот шаг можно пропустить.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkInterfaceIDs.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Выполнение проверки IP-потока

Теперь, когда у нас есть сведения, необходимые для выполнения командлета, мы выполним командлет `Test-AzureRmNetworkWatcherIPFlow` для проверки трафика. В этом примере мы используем первый IP-адрес первой сетевой карты.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> Для проверки IP-потока требуется выделение ресурса виртуальной машины для выполнения.

## <a name="review-results"></a>Просмотр результатов

После выполнения командлет `Test-AzureRmNetworkWatcherIPFlow` вернет результаты. Они представлены в следующем примере.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>Дальнейшие действия

Если трафик блокируется, чего не должно быть, см. статью [Управление группами безопасности сети с помощью портала](../virtual-network/virtual-network-manage-nsg-arm-portal.md). В ней содержатся сведения об отслеживании группы безопасности сети и определенных правил безопасности.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png















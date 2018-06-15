---
title: Проверка трафика с использованием проверки потока IP-адресов с помощью Наблюдателя за сетями (REST) | Документы Майкрософт
description: 'В этой статье описывается, как проверить состояние передачи входящего и исходящего трафика виртуальной машины: разрешен он или запрещен'
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c3d38f776db63e777174b7dca8b09a0d19c387e8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809792"
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Проверка состояния входящего и исходящего трафика (разрешен или запрещен) путем проверки IP-потока (компонент Наблюдателя за сетями Azure)

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


Проверка потока IP-адресов — это функция службы наблюдения за сетями (Наблюдатель за сетями), которая позволяет определить состояние трафика виртуальной машины: разрешен он или запрещен. Проверка может выполняться как для входящего, так и исходящего трафика. В этом сценарии можно получить сведения о текущем состоянии взаимодействия виртуальной машины с внешним ресурсом или сервером. Проверка потока IP-адресов позволяет убедиться, что правила группы безопасности сети настроены правильно, и устранить неполадки потоков, заблокированных правилами NSG. Такая проверка также гарантирует, что NSG будет соответствующим образом блокировать трафик, который нужно заблокировать.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы вызвать REST API при помощи командлетов PowerShell, вам потребуется ARMClient. Пакет ARMClient можно скачать на сайте [Chocolatey](https://chocolatey.org/packages/ARMClient).

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями).

## <a name="scenario"></a>Сценарий

В этом сценарии проверка потока IP-адресов помогает определить, может ли виртуальная машина обратиться к другой машине через порт 443. Если трафик отклоняется, возвращается правило безопасности, блокирующее трафик. Дополнительные сведения о проверке потока IP-адресов см. в [этом обзоре](network-watcher-ip-flow-verify-overview.md).

В рамках этого сценария вы:

* Получение виртуальной машины
* Вызов проверки IP-потока
* проверите результаты;

## <a name="log-in-with-armclient"></a>выполните вход с помощью ARMClient;

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Получение виртуальной машины

Выполните следующий сценарий, чтобы получить сведения о виртуальной машине. В приведенном ниже коде нужно указать значения переменных.

* **subscriptionId** — идентификатор используемой подписки.
* **resourceGroupName** — имя группы ресурсов, в которой содержатся виртуальные машины.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Необходим идентификатор типа `Microsoft.Compute/virtualMachines`. Результаты должны выглядеть, как с следующем фрагменте кода.

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Вызов проверки IP-потока

В следующем примере создается запрос на проверку трафика указанной виртуальной машины. Возвращаемый ответ содержит информацию о том, разрешен трафик или запрещен. Если трафик запрещен, также возвращается правило, которое блокирует трафик.

> [!NOTE]
> Для проверки потока для IP-адреса необходимо, чтобы ресурс виртуальной машины был выделен.

Сценарию требуется идентификатор ресурса виртуальной машины и ее сетевая карта. Эти значения имеются в предыдущих выходных данных.

> [!Important]
> При всех вызовах REST Наблюдателя за сетями универсальный код ресурса (URI) должен содержать имя группы ресурсов, в которой находится экземпляр Наблюдателя за сетями, а не диагностируемые ресурсы.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Анализ результатов

Полученный ответ указывает, разрешен трафик или запрещен. Ниже приведены примеры ответов.

**Разрешен**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Запрещен**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Дополнительная информация

Если трафик блокируется, чего не должно быть, прочитайте статью [Управление группами безопасности сети с помощью портала](../virtual-network/manage-network-security-group.md), чтобы узнать больше о группах безопасности сети.













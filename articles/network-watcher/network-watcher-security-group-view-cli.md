---
title: Анализ безопасности сети с помощью представления группы безопасности в Наблюдателе за сетями (Azure CLI 2.0) | Документация Майкрософт
description: Из этой статьи вы узнаете, как с помощью Azure CLI 2.0 анализировать безопасность виртуальных машин, используя представление группы безопасности.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9ae5ec422b197b077c52dbb2e64ddab4e08e3a50
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "26409884"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-20"></a>Анализ безопасности виртуальных машин с помощью Azure CLI 2.0 и представления группы безопасности

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Представление группы безопасности возвращает настроенные и действующие правила сетевой безопасности, применяемые к виртуальной машине. Эта возможность полезна для аудита и диагностики групп безопасности сети и настроенных на виртуальной машине правил, позволяющих обеспечить разрешение или отклонение трафика соответствующим образом. В этой статье мы покажем, как получить настроенные и действующие правила безопасности, применяемые к виртуальной машине, с помощью Azure CLI.


В этой статье мы используем наш новейший интерфейс командной строки для модели развертывания с помощью Resource Manager, а именно Azure CLI 2.0. Этот инструмент доступен для Windows, Mac и Linux.

Для выполнения действий, описанных в этой статье, требуется [установить интерфейс командной строки Azure для Mac, Linux и Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями).

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, вы получите настроенные и действующие правила безопасности для указанной виртуальной машины.

## <a name="get-a-vm"></a>Получение виртуальной машины

Для выполнения командлета `vm list` требуется виртуальная машина. Следующая команда выводит список виртуальных машин в группе ресурсов:

```azurecli
az vm list -resource-group resourceGroupName
```

Определив виртуальную машину, можно получить ее идентификатор ресурса, используя команду `vm show`:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Получение представления группы безопасности

Далее необходимо получить результат представления группы безопасности.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Просмотр результатов

Далее приведен пример сокращенного ответа возвращенных результатов. В результатах приводятся все действующие и применяемые правила безопасности на виртуальной машине, разделенные по группам **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** и **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Сведения об автоматизации проверки групп безопасности сети см. в статье [Auditing Network Security Groups (NSG) with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Выполнение аудита групп безопасности сети с помощью Наблюдателя за сетями).

Дополнительные сведения о правилах безопасности, применяемых к сетевым ресурсам, см. в статье [Security group view overview](network-watcher-security-group-view-overview.md) (Обзор представления группы безопасности).

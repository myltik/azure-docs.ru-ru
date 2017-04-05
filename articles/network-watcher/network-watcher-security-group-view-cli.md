---
title: "Анализ безопасности сети с помощью представления группы безопасности Наблюдателя за сетями (Azure CLI) | Документация Майкрософт"
description: "В этой статье вы узнаете, как проанализировать безопасность виртуальных машин, используя представление группы безопасности, с помощью Azure CLI."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 9c539f5885a5014f98ccc1083c93daa4eee3646c
ms.lasthandoff: 03/28/2017

---

# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Анализ безопасности виртуальной машины с использованием представления группы безопасности в Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Представление группы безопасности возвращает настроенные и действующие правила сетевой безопасности, применяемые к виртуальной машине. Эта возможность полезна для аудита и диагностики групп безопасности сети и настроенных на виртуальной машине правил, позволяющих обеспечить разрешение или отклонение трафика соответствующим образом. В этой статье мы покажем, как получить настроенные и действующие правила безопасности, применяемые к виртуальной машине, с помощью Azure CLI.

В этой статье используется кроссплатформенной Azure CLI 1.0, доступный для Windows, Mac и Linux. Наблюдатель за сетями в настоящее время использует Azure CLI 1.0 в качестве интерфейса командной строки.

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями).

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, вы получите настроенные и действующие правила безопасности для указанной виртуальной машины.

## <a name="get-a-vm"></a>Получение виртуальной машины

Для выполнения командлета `vm list` требуется виртуальная машина. Следующая команда выводит список виртуальных машин в группе ресурсов:

```azurecli
azure vm list -g resourceGroupName
```

Определив виртуальную машину, можно получить ее идентификатор ресурса, используя команду `vm show`:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Получение представления группы безопасности

Далее необходимо получить результат представления группы безопасности. Если добавить флаг --json, результаты в JSON-файле будут отформатированы.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Просмотр результатов

Далее приведен пример сокращенного ответа возвращенных результатов. В результатах приводятся все действующие и применяемые правила безопасности на виртуальной машине, разделенные по группам **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** и **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

Сведения об автоматизации проверки групп безопасности сети см. в статье [Auditing Network Security Groups (NSG) with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Выполнение аудита групп безопасности сети с помощью Наблюдателя за сетями).

Дополнительные сведения о правилах безопасности, применяемых к сетевым ресурсам, см. в статье [Security group view overview](network-watcher-security-group-view-overview.md) (Обзор представления группы безопасности).


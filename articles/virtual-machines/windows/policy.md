---
title: Обеспечение безопасности с помощью политик на виртуальных машинах Windows в Azure | Документация Майкрософт
description: Как применить политику к виртуальной машине Azure Resource Manager на основе Windows
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: fc95b57c244e855f6788b7f21fc9abc0eba3d44d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654714"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Применение политик к виртуальным машинам Windows с помощью Azure Resource Manager
С помощью политик организация может обеспечить выполнение различных норм и правил во всей организации. Обязательные для выполнения стандарты поведения помогают снизить риск, что способствует успешной деятельности организации. В этой статье описано, как определить требуемое поведение для виртуальных машин вашей организации с помощью политик Azure Resource Manager.

Общие сведения о политиках см. в статье [Что такое служба "Политика Azure"](../../azure-policy/azure-policy-introduction.md).

## <a name="permitted-virtual-machines"></a>Разрешенные виртуальные машины
Чтобы обеспечить совместимость виртуальных машин вашей организации с приложением, вы можете ограничить допустимые операционные системы. В этом примере политики вы разрешите только создание виртуальных машин на основе Windows Server 2012 R2 Datacenter:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Чтобы разрешить любой образ Windows Server Datacenter, измените предыдущую политику, используя подстановочный знак:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Чтобы разрешить любой образ центра обработки данных Windows Server 2012 R2 или более поздней версии, измените предыдущую политику, используя оператор anyOf:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Сведения о полях политики см. в разделе [Псевдонимы](../../azure-policy/policy-definition.md#aliases).

## <a name="managed-disks"></a>Управляемые диски

Чтобы задать использование управляемых дисков, используйте следующую политику:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Образы виртуальных машин

По соображениям безопасности можно требовать, чтобы в среде развертывались только утвержденные пользовательские образы. Вы можете указать группу ресурсов, содержащую утвержденные образы, или конкретные утвержденные образы.

В следующем примере требуются образы из группы утвержденных ресурсов:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

В следующем примере задаются идентификаторы утвержденных образов:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Расширения виртуальной машины

Вам может потребоваться запретить использование некоторых типов расширений. Например, расширение может оказаться несовместимым с некоторыми пользовательскими образами виртуальной машины. В следующем примере показано, как заблокировать определенное расширение. Чтобы определить модули, которые следует заблокировать, он использует издателя и тип.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Программа преимуществ гибридного использования Azure

При наличии локальной лицензии можно сэкономить на лицензионном сборе для виртуальных машин. Если у вас нет лицензии, следует запретить этот параметр. Следующая политика запрещает использование программы преимуществ гибридного использования Azure:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Дополнительная информация
* После определения правила политики (как показано в приведенных выше примерах) необходимо создать определение политики и назначить ей область. Областью может быть подписка, группа ресурсов или ресурс. Чтобы узнать, как назначать политики, изучите статью о [назначении политик ресурсов и управлении ими с помощью портала Azure](../../azure-policy/assign-policy-definition.md), о [назначении политик с помощью PowerShell](../../azure-policy/assign-policy-definition-ps.md) или [назначении политик с помощью Azure CLI](../../azure-policy/assign-policy-definition-cli.md).
* Общие сведения о политиках ресурсов см. в статье [Что такое служба "Политика Azure"](../../azure-policy/azure-policy-introduction.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
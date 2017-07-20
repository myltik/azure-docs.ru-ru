---
title: "Обеспечение безопасности с помощью политик на виртуальных машинах Linux в Azure | Документация Майкрософт"
description: "Как применить политику к виртуальной машине Azure Resource Manager на основе Linux"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: singhkay
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: c1ad80a56627695f3594f4d9b60cd623fa9bcce3
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Применение политик к виртуальным машинам Linux с помощью Azure Resource Manager
С помощью политик организация может обеспечить выполнение различных норм и правил во всей организации. Обязательные для выполнения стандарты поведения помогают снизить риск, что способствует успешной деятельности организации. В этой статье описано, как определить требуемое поведение для виртуальных машин вашей организации с помощью политик Azure Resource Manager.

Общие сведения о политиках см. в статье [Применение политик для управления ресурсами и контроля доступа](../../azure-resource-manager/resource-manager-policy.md).

## <a name="define-policy-for-permitted-virtual-machines"></a>Определение политики для разрешенных виртуальных машин
Чтобы обеспечить совместимость виртуальных машин вашей организации с приложением, вы можете ограничить допустимые операционные системы. В этом примере политики вы разрешите только создание виртуальных машин на основе Ubuntu 14.04.2-LTS.

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
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
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

Чтобы разрешить любой образ Ubuntu LTS, измените предыдущую политику, используя подстановочный знак: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Сведения о полях политики см. в разделе [Псевдонимы](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="define-policy-for-using-managed-disks"></a>Определение политики для использования управляемых дисков

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

## <a name="next-steps"></a>Дальнейшие действия
* После определения правила политики (как показано в приведенных выше примерах) необходимо создать определение политики и назначить ей область. Областью может быть подписка, группа ресурсов или ресурс. Сведения о назначении политик с помощью портала см. в статье [Назначение политик ресурсов и управление ими с помощью портала Azure](../../azure-resource-manager/resource-manager-policy-portal.md). Сведения о назначении политик с помощью REST API, PowerShell или Azure CLI см. в статье [Назначение политик ресурсов и управление ими](../../azure-resource-manager/resource-manager-policy-create-assign.md).
* Введение в политики ресурсов представлено в разделе [Общие сведения о политике ресурсов](../../azure-resource-manager/resource-manager-policy.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](../../azure-resource-manager/resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).


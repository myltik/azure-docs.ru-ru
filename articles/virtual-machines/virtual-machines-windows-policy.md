---
title: "Применение политик к виртуальным машинам Azure Resource Manager | Документация Майкрософт"
description: "Как применить политику к виртуальной машине Azure Resource Manager на основе Windows"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5ee1e47b940516ea872daba7d1dd7b23f404f326


---
# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Применение политик к виртуальным машинам Azure Resource Manager
С помощью политик организация может обеспечить выполнение различных норм и правил во всей организации. Обязательные для выполнения стандарты поведения помогают снизить риск, что способствует успешной деятельности организации. В этой статье мы расскажем, как определить требуемое поведение для виртуальных машин вашей организации с помощью политик Azure Resource Manager.

Ниже представлены шаги для реализации вертикального масштабирования.

1. Политика Azure Resource Manager 101
2. Определение политики для виртуальной машины
3. Создание политики
4. Применение политики

## <a name="azure-resource-manager-policy-101"></a>Политика Azure Resource Manager 101
Чтобы начать работу с политиками диспетчера ресурсов Azure, рекомендуется прочесть статью ниже, а затем выполнить действия, описанные в этой статье. В статье ниже описываются базовое определение, структура и оценка политики, а также приводятся различные примеры определений политики.

* [Применение политик для управления ресурсами и контроля доступа](../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Определение политики для виртуальной машины
Один из самых распространенных сценариев для организации состоит в том, что пользователям разрешается создавать виртуальные машины только на основе заданного набора операционных систем, которые были протестированы на совместимость с бизнес-приложением. С помощью политики Azure Resource Manager эту задачу можно выполнить за несколько шагов.
В данном примере политики мы собираемся разрешить только создание виртуальных машин на основе Windows Server 2012 R2 Datacenter. Определение политики приведено ниже.

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

Приведенную выше политику можно легко превратить в сценарий, в котором для виртуальной машины разрешается использовать любой образ Windows Server Datacenter. Для этого выполните следующее изменение:

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>Поля свойств виртуальной машины
В следующей таблице описываются свойства виртуальной машины, которые можно использовать в качестве полей в определении политики. Дополнительные сведения о полях политики см. в следующей статье:

* [Поля и источники](../azure-resource-manager/resource-manager-policy.md#conditions)

| Имя поля | Description (Описание) |
| --- | --- |
| imagePublisher |Указывает издателя образа |
| imageOffer |Указывает предложение для выбранного издателя образа |
| imageSku |Указывает номер SKU для выбранного предложения |
| imageVersion |Указывает версию образа для выбранного номера SKU |

## <a name="create-the-policy"></a>Создание политики
Политику можно легко создать напрямую в API REST или с помощью командлетов PowerShell. Для создания политики обратитесь к следующей статье:

* [Создание политики](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)

## <a name="apply-the-policy"></a>Применение политики
После создания политики необходимо применить ее к заданной области. Областью может быть подписка, группа ресурсов или даже ресурс. Для применения политики обратитесь к следующей статье:

* [Создание политики](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)



<!--HONumber=Dec16_HO2-->



---
title: "Ошибки, связанные с недопустимым шаблоном Azure | Документация Майкрософт"
description: "Описывается, как устранить ошибки, связанные с недопустимым шаблоном."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 87bc6e4def624785c5052a9a25f579b022c940ec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="resolve-errors-for-invalid-template"></a>Устранение ошибок, связанных с недопустимым шаблоном

В этой статье описывается, как устранить ошибки, связанные с недопустимым шаблоном.

## <a name="symptom"></a>Симптом

При развертывании шаблона появляется следующее сообщение об ошибке:

```
Code=InvalidTemplate
Message=<varies>
```

Сообщение об ошибке зависит от типа ошибки.

## <a name="cause"></a>Причина:

Эта ошибка может появиться в результате ошибок нескольких различных типов. Как правило, это синтаксические или структурные ошибки в шаблоне.

## <a name="solution"></a>Решение

### <a name="solution-1---syntax-error"></a>Решение 1 — синтаксическая ошибка

Если появляется сообщение об ошибке, указывающее, что шаблону не удалось пройти аутентификацию, возможно, в нем есть синтаксическая ошибка.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Такую ошибку легко допустить, так как выражения шаблонов могут быть сложными. Например, представленное ниже присвоение имени для учетной записи хранения содержит один набор квадратных скобок, три функции, три набора круглых скобок, один набор одинарных кавычек и одно свойство:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Если вы не укажете соответствующий синтаксис, шаблон создаст значение, которое не будет соответствовать ожидаемому значению.

При получении сообщения об ошибке такого типа тщательно проверьте синтаксис выражения. Рекомендуется использовать редактор JSON, например [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) или [Visual Studio Code](resource-manager-vs-code.md), в котором отображаются предупреждения о синтаксических ошибках.

### <a name="solution-2---incorrect-segment-lengths"></a>Решение 2 — неправильная длина сегментов

Шаблон также считается недопустимым, если имя ресурса указано в неправильном формате.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Имя ресурса на корневом уровне должно содержать на один сегмент меньше, чем тип ресурса. Каждый сегмент разделяется косой чертой. В следующем примере тип содержит два сегмента, а имя — один. Так что это **допустимое имя**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

В приведенном ниже примере указано **недопустимое имя** , так как оно содержит такое же количество сегментов, что и тип.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

В случае дочерних ресурсов тип и имя должны содержать одинаковое количество сегментов. Это целесообразно, так как полное имя и тип дочернего элемента включает имя родительского элемента и его тип. Таким образом в полном имени на один сегмент меньше, чем в полном типе.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Разобраться с правильной длиной сегментов сложно при использовании типов Resource Manager, которые применяются для поставщиков ресурсов. Например, для применения блокировки ресурсов на веб-сайте требуется тип с четырьмя сегментами. Поэтому имя содержит три сегмента:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>Решение 3 — недопустимый параметр

Если шаблон указывает допустимые значения для параметра, а вы укажете значение, отличное от одного из указанных, появится сообщение об ошибке, аналогичное следующему:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Внимательно проверьте допустимые значения в шаблоне и укажите одно из них во время развертывания.

### <a name="solution-4---too-many-target-resource-groups"></a>Решение 4 — слишком много целевых групп ресурсов

Если вы указали более пяти целевых групп ресурсов в одном развертывании, поступает это сообщение об ошибке. Рекомендуем консолидировать группы ресурсов в развертывании или развернуть некоторые шаблоны как отдельные развертывания. Дополнительные сведения см. в разделе [Развертывание ресурсов Azure в нескольких подписках или группах ресурсов](resource-manager-cross-resource-group-deployment.md).

### <a name="solution-5---circular-dependency-detected"></a>Решение 5 — обнаружена циклическая зависимость

Эта ошибка возникает, когда ресурсы зависят друг от друга таким образом, что это не позволяет начать развертывание. Сочетание взаимозависимостей вынуждает два или более ресурсов ожидать другие ресурсы, которые также находятся в ожидании. Например, resource1 зависит от resource3, resource2 зависит от resource1, а resource3 зависит от resource2. Как правило, эту проблему можно устранить, удалив ненужные зависимости.

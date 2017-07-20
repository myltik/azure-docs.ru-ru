---
title: "Политики ресурсов Azure для соглашений об именовании | Документация Майкрософт"
description: "В этой статье описываются политики Azure Resource Manager для соглашений об именовании ресурсов."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017


---
# <a name="apply-resource-policies-for-names-and-text"></a>Применение политик ресурсов для имен и текста
В этом разделе показано несколько [политик ресурсов](resource-manager-policy.md), которые можно применить для настройки соглашений об именовании и соглашений текста. Эти политики обеспечивают согласованность данных в именах ресурсов и в значениях тегов. 

## <a name="set-naming-convention-with-wildcard"></a>Соглашение об именовании с символами подстановки
В следующем примере показано, как использовать подстановочный знак в условии **like**. Это условие означает следующее: если имя не соответствует заданному шаблону (namePrefix\*nameSuffix), запрос будет отклонен.

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>Соглашение об именовании с шаблоном

Чтобы указать, что имена ресурсов должны соответствовать шаблону, используйте условие match. В следующем примере задано, что имена должны начинаться с `contoso` и содержать 6 дополнительных букв:

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>Настройка шаблона даты для значения тега

Чтобы задать дату в формате "две цифры — три буквы — четыре цифры", используйте следующий код:

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия
* После определения правила политики (как показано в приведенных выше примерах) необходимо создать определение политики и назначить ей область. Областью может быть подписка, группа ресурсов или ресурс. Сведения о назначении политик с помощью портала см. в статье [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md). Сведения о назначении политик с помощью REST API, PowerShell или Azure CLI см. в статье [Назначение политик ресурсов и управление ими](resource-manager-policy-create-assign.md). 
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).



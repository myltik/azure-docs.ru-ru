---
title: "Ошибка RequestDisallowedByPolicy с политикой ресурсов Azure | Документация Майкрософт"
description: "Описывает причину ошибки RequestDisallowedByPolicy."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: ru-ru
ms.lasthandoff: 07/13/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Ошибка RequestDisallowedByPolicy с политикой ресурсов Azure

В этой статье описывается причина ошибки RequestDisallowedByPolicy, а также предлагается решение для ее устранения.

## <a name="symptom"></a>Симптом

Когда вы пытаетесь выполнить действие во время развертывания, может появиться ошибка **RequestDisallowedByPolicy**, которая блокирует выполнение этого действия. Вот пример такой ошибки:

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Устранение неполадок

Чтобы получить информацию о том, какая политика блокирует развертывание, используйте один из следующих методов.

### <a name="method-1"></a>Метод 1

В PowerShell укажите идентификатор политики в качестве значения для параметра **Id**, чтобы получить сведения о политике, заблокировавшей развертывание.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Метод 2 

В Azure CLI 2.0 укажите имя определения политики. 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Решение

Возможно, ваш ИТ-отдел для обеспечения безопасности или соответствия применяет некоторые политики ресурсов, например такие, которые запрещают создавать общедоступные IP-адреса, сетевые группы безопасности, определяемые пользователем маршруты или таблицы маршрутизации. В нашем примере ошибки, представленном в разделе "Проблема", проблемная политика имеет имя **regionPolicyDefinition**. Это имя может быть любым.
Чтобы устранить такую проблему, свяжитесь с ИТ-отделом, совместно оцените политики ресурсов и определите приемлемый метод выполнить нужное действие, не нарушая политики организации.


Дополнительные сведения см. в следующих статьях:

- [Общие сведения о политике ресурсов](resource-manager-policy.md)
- [Описание для RequestDisallowedByPolicy в статье о распространенных ошибках развертывания](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 




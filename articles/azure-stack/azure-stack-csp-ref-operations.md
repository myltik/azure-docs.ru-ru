---
title: Регистрация клиентов для отслеживания использования Azure Stack | Документация Майкрософт
description: Сведения об операциях для управления регистрацией клиентов и о том, как отслеживать использование клиента в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: be15fbc5fad79f1079b901b3d6cb4948c45a0ab4
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2018
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Управление регистрацией клиента в Azure Stack

*Область применения: интегрированные системы Azure Stack*

Эта статья содержит сведения об операциях для управления регистрациями клиентов и о том, как отслеживать использование клиента. Кроме того, вы узнаете, как добавлять, перечислять и удалять сопоставления клиентов. Отслеживанием использования можно управлять при помощи PowerShell или конечных точек API выставления счетов.

## <a name="add-tenant-to-registration"></a>Добавление клиента в регистрацию

Эта операция используется, если нужно добавить в регистрацию новый клиент. При этом создаются отчеты о его использовании для подписки Azure, подключенной к соответствующему клиенту Azure Active Directory (Azure AD).

Кроме того, операция позволяет изменить подписку, связанную с клиентом. Для этого можно повторно вызвать PUT/New-AzureRMResource. Старые сопоставления перезаписывается.

Обратите внимание, что с клиентом можно связать только одну подписку Azure. Если добавить вторую подписку в существующий клиент, первая подписка перезаписывается. 


| Параметр                  | ОПИСАНИЕ |
|---                         | --- |
| registrationSubscriptionID | Подписка Azure, которая использовалась для первоначальной регистрации. |
| customerSubscriptionID     | Подписка Azure (не Azure Stack), принадлежащая клиенту, для которого выполняется регистрация. Ее нужно создать в предложении поставщика облачных служб (CSP). На практике это осуществляется через Центр партнеров. Если у клиента более одного клиента, подписку нужно создать в клиенте, который будет использоваться для входа в Azure Stack. |
| resourceGroup              | Группа ресурсов в Azure, в которой хранятся данные регистрации. |
| registrationName           | Имя регистрации Azure Stack. Это объект, который хранится в Azure. Имя обычно представлено в формате azurestack-CloudID, где CloudID — это идентификатор облака для развертывания Azure Stack. |

> [!Note]  
> Клиенты должны быть зарегистрированы в каждом используемым ими экземпляре Azure Stack. Если клиент использует несколько экземпляров Azure Stack, обновите первоначальные регистрации каждого развертывания с использованием подписки клиента.

### <a name="powershell"></a>PowerShell

Обновите ресурс регистрации при помощи командлета New-AzureRmResource. Войдите в Azure (`Login-AzureRMAccount`) с помощью учетной записи, которая использовалась для первоначальной регистрации. Пример добавление клиента:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Вызов API

**Операция**: PUT  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 201, создано  
**Текст ответа**: отсутствует  

## <a name="list-all-registered-tenants"></a>Перечисление всех зарегистрированных клиентов

Вы можете получить список всех клиентов, добавленных в регистрацию.

 > [!Note]  
 > Если клиенты не зарегистрированы, вы не получите ответ.

### <a name="parameters"></a>Параметры

| Параметр                  | ОПИСАНИЕ          |
|---                         | ---                  |
| registrationSubscriptionId | Подписка Azure, которая использовалась для первоначальной регистрации.   |
| resourceGroup              | Группа ресурсов в Azure, в которой хранятся данные регистрации.    |
| registrationName           | Имя регистрации Azure Stack. Это объект, который хранится в Azure. Обычно имя указывается в формате **azurestack**-***CloudID***, где ***CloudID*** — это идентификатор облака для развертывания Azure Stack.   |

### <a name="powershell"></a>PowerShell

Для перечисления всех зарегистрированных клиентов воспользуйтесь командлетом Get-AzureRmResovurce. Войдите в Azure (`Login-AzureRMAccount`) с помощью учетной записи, которая использовалась для первоначальной регистрации. Пример добавления клиента:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Вызов API

Список всех сопоставлений клиента можно получить с помощью операции GET.

**Операция**: GET  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 200  
**Текст ответа**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Удаление сопоставления клиента

Вы можете удалить клиент, добавленный в регистрацию. Если этот клиент по-прежнему использует ресурсы Azure Stack, их использование относится на счет подписки, которая применялась при первоначальной регистрации Azure Stack.

### <a name="parameters"></a>Параметры

| Параметр                  | ОПИСАНИЕ          |
|---                         | ---                  |
| registrationSubscriptionId | Идентификатор подписки для регистрации.   |
| resourceGroup              | Группа ресурсов для регистрации.   |
| registrationName           | Имя регистрации.  |
| customerSubscriptionId     | Идентификатор подписки клиента.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Вызов API

Вы можете удалить сопоставления клиента с помощью операции DELETE.

**Операция**: DELETE  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 204, нет содержимого  
**Текст ответа**: отсутствует

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения см. в статье об [использовании ресурсов и выставлении счетов в Azure Stack](/azure-stack-billing-and-chargeback.md).

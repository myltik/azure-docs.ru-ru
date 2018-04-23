---
title: Добавление клиентов для контроля потребления и выставления счетов в Azure Stack | Документация Майкрософт
description: В этой статье описано, как добавить пользователей в Azure Stack под управлением поставщика облачных служб.
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
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: fd5a8afe82bab5ca4e735a2483d90b84e234b8dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Добавление клиентов для контроля потребления и выставления счетов в Azure Stack

*Область применения: интегрированные системы Azure Stack*

В этой статье описывается процесс добавления пользователей в Azure Stack под управлением поставщика облачных служб (CSP). Когда новый клиент потребляет ресурсы, Azure Stack передает информацию об этом в соответствующую подписку CSP.

CSP часто предоставляют службы, развернутые в Azure Stack, сразу нескольким клиентам. Клиентов следует добавить в регистрацию Azure Stack, чтобы информация о потреблении и выставлении счетов правильно передавалась в подписки CSP. Если вы не выполните шаги из этой статьи, плата за потребленные клиентом ресурсы будет взиматься с подписки, которую вы использовали при первоначальной регистрации Azure Stack. Чтобы получить возможность добавлять клиентов в Azure Stack для отслеживания потребления и управления клиентами, необходимо настроить для Azure Stack роль CSP. Этот процесс и необходимые ресурсы описаны в статье [Управление потреблением и оплатой для Azure Stack в роли поставщика облачных служб](azure-stack-add-manage-billing-as-a-csp.md).

На схеме ниже показаны действия, которые позволяют CSP создать нового клиента в Azure Stack и настроить для него отслеживание потребления. Добавив клиента, вы также сможете управлять для него ресурсами Azure Stack. Для управления ресурсами доступны два варианта действий.

1. Вы можете сохранить за собой управление клиентом, передав ему только учетные данные для доступа к локальной подписке Azure Stack.  
2. Или же клиент будет использовать подписку локально, добавив в своей среде гостевой доступ для CSP с правами владельца.  

**Процесс добавления клиента**

![Настройка отслеживания потребления и управления учетной записью клиента для поставщика облачных служб](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Создание клиента в Центре партнеров

В Центре партнеров создайте для клиента новую подписку Azure. Этот процесс описан в статье [Добавление нового клиента](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Создание подписки Azure для клиента

Создав для клиента запись в Центре партнеров, вы сможете продавать ему подписки на продукты, включенные в каталог. Этот процесс описан в статье [Создание новой подписки](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Создание гостевого пользователя в каталоге клиента

Если клиент будет самостоятельно управлять своей учетной записью, создайте в его каталоге гостевого пользователя и передайте сведения о нем представителям клиента. Затем клиент должен добавить гостевую учетную запись в каталог и повысить ее уровень до **владельца** учетной записи CSP Azure Stack.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Добавление подписки клиента в регистрацию

Добавьте сведения о новой подписке клиента в регистрацию. Теперь Azure будет передавать данные клиента о потреблении ресурсов, используя указанный в Центре партнеров идентификатор клиента. Благодаря этому данные каждого клиента о потреблении ресурсов будут относиться к его подписке CSP. Так будет намного проще отслеживать потребление и выставлять счета за него.

> [!Note]  
> Чтобы выполнить этот шаг, нужно [зарегистрировать Azure Stack](azure-stack-register.md).

1. Откройте Windows PowerShell из командной строки с повышенными привилегиями и выполните следующую команду:  
    `Connect-AzureRmAccount`
2. Введите учетные данные Azure.
3. В сеансе PowerShell выполните следующую команду:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```
### <a name="new-azurermresource-powershell-parameters"></a>Параметры команды New-AzureRmResource в PowerShell
| Параметр | ОПИСАНИЕ |
| --- | --- | 
|registrationSubscriptionID | Подписка Azure, которая использовалась для первоначальной регистрации Azure Stack. |
| customerSubscriptionID | Подписка Azure (не Azure Stack), принадлежащая клиенту, для которого выполняется регистрация. Она должна быть создана в предложении поставщика облачных служб, то есть через Центр партнеров. Если для клиента создано несколько учетных записей в Azure Active Directory, подписку нужно создать в той из них, которая будет использоваться для входа в Azure Stack.
| resourceGroup | Группа ресурсов Azure, в которой хранятся данные об этой регистрации. 
| registrationName | Имя регистрации Azure Stack. Это объект, который хранится в Azure. | 


> [!Note]  
> Клиенты должны быть зарегистрированы в каждом используемым ими экземпляре Azure Stack. Если у вас есть два развертывания Azure Stack и клиент будет использовать оба, сведения о подписке клиента нужно внести в начальные регистрации для обоих развертываний.

## <a name="onboard-tenant-to-azure-stack"></a>Подключение клиента к Azure Stack

Настройте в Azure Stack поддержку пользователей из нескольких клиентов Azure AD, чтобы они могли использовать службы в Azure Stack. Дополнительные сведения см. в статье [Поддержка мультитенантности в Azure Stack](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Создание локального ресурса в клиенте Azure Stack для клиента

Когда вы добавите нового клиента в Azure Stack или представители клиента предоставят права владельца вашей гостевой учетной записи, проверьте возможность создавать ресурсы для этого клиента. Например, можно [создать виртуальную машину Windows на портале Azure Stack](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Дополнительная информация

 - Если в процессе регистрации возникнут ошибки, изучите их с помощью этой [инструкции по ошибкам регистрации клиента](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Дополнительные сведения см. в статье о [потреблении ресурсов и выставлении счетов в Azure Stack](/azure-stack-billing-and-chargeback.md).
 - Чтобы помочь клиенту добавить вашу учетную запись (CSP) с правами менеджера в клиенте Azure Stack, изучите статью [Предоставление поставщику облачных служб возможности управлять подпиской Azure Stack](user\azure-stack-csp-enable-billing-usage-tracking.md).

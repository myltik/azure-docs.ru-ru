---
title: "Использование модуля политики Azure Stack | Документация Майкрософт"
description: "Сведения о том,как ограничить подписку Azure, чтобы ее поведение было аналогично подписке Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e505c52a5e1897d5626ee2cacce9fa3eff12fbbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Управление политикой Azure с использованием модуля политики Azure Stack

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

Модуль политики Azure Stack позволяет настроить подписку Azure с такими же возможностями управления версиями и доступными службами, как и в Azure Stack.  Модуль использует командлет **New-AzureRMPolicyAssignment** для создания политики Azure, которая ограничивает типы ресурсов и службы, доступные в подписке.  После завершения подписку Azure можно использовать для разработки приложений, предназначенных для Azure Stack.  

## <a name="install-the-module"></a>Установка модуля
1. Установите необходимую версию модуля PowerShell AzureRM, как описано в шаге 1 статьи об [установке PowerShell для Azure Stack](azure-stack-powershell-install.md).   
2. [Скачайте средства Azure Stack из GitHub](azure-stack-powershell-download.md).  
3. [Configure PowerShell for use with Azure Stack](azure-stack-powershell-configure-user.md) (Настройка PowerShell для использования с Azure Stack)

4. Импортируйте модуль AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>Применение политики к подписке
Следующая команда позволяет применить используемую по умолчанию политику Azure Stack к подписке Azure. Перед выполнением замените *Azure Subscription Name* именем своей подписки Azure.

```PowerShell
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
$rgName = 'AzureStack'
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Применение политики к группе ресурсов
Возможно, вам потребуется применить политики более детализировано.  Например, у вас могут быть другие ресурсы, выполняющиеся той же подписке.  Областью действия приложения политики можно задать определенную группу ресурсов, которая позволит тестировать приложения для работы с Azure Stack с использованием ресурсов Azure. Перед выполнением замените *Azure Subscription Name* именем своей подписки Azure.

```PowerShell
$resourceGroupName = ‘myRG01’
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Политика в действии
После применения политики Azure при попытке развернуть ресурс, который запрещен этой политикой, возникает ошибка.  

![Результат сбоя развертывания ресурса из-за ограничения политики](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Дальнейшие действия
[Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)

[Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)

[Deploy templates in Azure Stack using Visual Studio](azure-stack-deploy-template-visual-studio.md) (Развертывание шаблонов в Azure Stack с помощью Visual Studio)

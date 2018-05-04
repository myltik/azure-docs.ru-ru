---
title: Использование модуля политики Azure Stack | Документация Майкрософт
description: Сведения о том,как ограничить подписку Azure, чтобы ее поведение было аналогично подписке Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: mabrigg
ms.openlocfilehash: 10df87ec6d30e74356b0ff0f44b8745f8c7b8bf3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Управление политикой Azure с использованием модуля политики Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

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
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Применение политики к группе ресурсов
Возможно, вам потребуется применить политики более детализировано.  Например, у вас могут быть другие ресурсы, выполняющиеся той же подписке.  Областью действия приложения политики можно задать определенную группу ресурсов, которая позволит тестировать приложения для работы с Azure Stack с использованием ресурсов Azure. Перед выполнением замените *Azure Subscription Name* именем своей подписки Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Политика в действии
После применения политики Azure при попытке развернуть ресурс, который запрещен этой политикой, возникает ошибка.  

![Результат сбоя развертывания ресурса из-за ограничения политики](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Дополнительная информация
[Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)

[Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)

[Deploy templates in Azure Stack using Visual Studio](azure-stack-deploy-template-visual-studio.md) (Развертывание шаблонов в Azure Stack с помощью Visual Studio)

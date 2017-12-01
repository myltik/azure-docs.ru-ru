---
title: "Регистрация Azure Stack | Документация Майкрософт"
description: "Регистрация Azure Stack в подписке Azure."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Регистрация Azure Stack в подписке Azure

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Вы можете зарегистрировать [Azure Stack](azure-stack-poc.md) в Azure, чтобы скачивать элементы marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. 

> [!NOTE]
>Мы рекомендуем использовать регистрацию, так как она позволяет проверить важные функции Azure Stack, например синдикацию marketplace и отчеты о потреблении. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации. С пользователей пакетов SDK для Azure Stack плата за передаваемые данные о потреблении не взимается.
>


## <a name="get-azure-subscription"></a>Получение подписки Azure

Для регистрации Azure Stack в Azure необходимо следующее:

- Идентификатор подписки Azure. Чтобы узнать этот идентификатор, выполните вход в Azure, щелкните **Дополнительные службы** > **Подписки**, затем выберите нужную подписку и найдите **Идентификатор подписки** в разделе **Основные компоненты**. В настоящее время облачные подписки для правительств Китая, Германии и США не поддерживаются.
- Имя пользователя и пароль учетной записи владельца подписки (поддерживаются учетные записи MSA/2FA).
- Azure Active Directory в подписке Azure. Чтобы получить сведения об Active Directory, наведите курсор на аватар в правом верхнем углу портала Azure. 

Если у вас нет подписки Azure, соответствующей всем этим требованиям, [создайте бесплатную учетную запись](https://azure.microsoft.com/en-us/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Регистрация в Azure поставщика ресурсов для Azure Stack
> [!NOTE] 
> Этот шаг для каждой среды Azure Stack выполняется однократно.
>

1. Запустите сеанс PowerShell от имени администратора.
2. Войдите, с помощью учетной записи владельца подписки Azure. (Для входа можно использовать командлет Login-AzureRmAccount. При этом задайте для параметра -EnvironmentName значение AzureCloud).
3. Зарегистрируйте поставщика ресурсов Azure Microsoft.AzureStack.

**Пример** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure

> [!NOTE]
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке. Для пакета средств разработки Azure Stack это будет главный компьютер. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack.
>

1. Откройте консоль PowerShell от имени администратора и [установите PowerShell для Azure Stack](azure-stack-powershell-install.md).  

2. Добавьте учетную запись Azure, которая будет использоваться для регистрации Azure Stack. Для этого выполните командлет `Add-AzureRmAccount` без параметров. Вам будет предложено ввести учетные данные учетной записи Azure. Также может потребоваться выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.  

3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Удалите все существующие версии модулей PowerShell, связанных с регистрацией, и [скачайте последнюю версию с сайта GitHub](azure-stack-powershell-download.md).  

5. В каталоге AzureStack-Tools-master, созданном на предыдущем шаге, перейдите в папку Registration и импортируйте модуль .\RegisterWithAzure.psm1.  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. В этом же сеансе PowerShell запустите следующий скрипт. Когда появится запрос на ввод учетных данных, в качестве имени пользователя укажите `azurestack\cloudadmin` и введите пароль для локального администратора, который использовался при развертывании.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Параметр | Описание |
   | -------- | ------------- |
   | CloudAdminCredential | Учетные данные для облачного домена, которые используются для [получения доступа к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Имя пользователя вводится в формате \<домен_Azure_Stack\>\cloudadmin. Имя пользователя для пакета средств разработки — azurestack\cloudadmin. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack, чтобы получить это значение.|
   | AzureSubscriptionId | Подписка Azure, которая используется для регистрации Azure Stack.|
   | AzureDirectoryTenantName | Имя каталога клиента Azure, связанного с подпиской Azure. В этом клиенте в каталоге будет создан ресурс регистрации. |
   | PrivilegedEndpoint | Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Для пакета средств разработки привилегированная конечная точка размещается на виртуальной машине AzS-ERCS01. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack, чтобы получить это значение. Дополнительные сведения см. в руководстве по [использованию привилегированной конечной точки](azure-stack-privileged-endpoint.md).|
   | BillingModel | Модель выставления счетов, которая используется в подписке. Для этого параметра допустимы такие значения: Capacity, PayAsYouUse и Development. Для пакета средств разработки используется значение Development. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack, чтобы получить это значение. |

7. По завершении скрипта появится такое сообщение: Activating Azure Stack (this step may take up to 10 minutes to complete) (Активация Azure Stack (эта операция может занять до 10 минут)). 

## <a name="verify-the-registration"></a>Проверка регистрации

1. Войдите на портал администратора Azure Stack (https://adminportal.local.azurestack.external).
2. Щелкните **Дополнительные службы** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).
3. Если отобразится список элементов, доступных в Azure (например, WordPress), значит, активация прошла успешно.

> [!NOTE]
> По завершении регистрации активное предупреждение об отсутствии регистрации больше не будет отображаться.

## <a name="next-steps"></a>Дальнейшие действия

[Подключение к Azure Stack](azure-stack-connect-azure-stack.md)


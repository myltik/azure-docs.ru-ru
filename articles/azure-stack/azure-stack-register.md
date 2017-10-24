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
ms.date: 10/10/2017
ms.author: erikje
ms.openlocfilehash: b5f112f2d5b96843e7863aa664eec4847c58e950
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Регистрация Azure Stack в подписке Azure

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

В развертываниях Azure Active Directory вы можете зарегистрировать [Azure Stack](azure-stack-poc.md) в Azure, чтобы скачивать элементы marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. 

> [!NOTE]
>Мы рекомендуем использовать регистрацию, так как она позволяет проверить важные функции Azure Stack, например синдикацию marketplace и отчеты о потреблении. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации. С пользователей пакетов SDK для Azure Stack плата за передаваемые данные о потреблении не взимается.
>


## <a name="get-azure-subscription"></a>Получение подписки Azure

Для регистрации Azure Stack в Azure необходимо следующее:

- Идентификатор подписки Azure. Чтобы узнать этот идентификатор, выполните вход в Azure, щелкните **Дополнительные службы** > **Подписки**, затем выберите нужную подписку и найдите **Идентификатор подписки** в разделе **Основные компоненты**. В настоящее время облачные подписки для правительств Китая, Германии и США не поддерживаются.
- Имя пользователя и пароль учетной записи владельца подписки (поддерживаются учетные записи MSA/2FA).
- Azure Active Directory в подписке Azure. Чтобы получить сведения об Active Directory, наведите курсор на аватар в правом верхнем углу портала Azure. 
- [Зарегистрированный поставщик ресурсов для Azure Stack](#register-azure-stack-resource-provider-in-azure).

Если у вас нет подписки Azure, соответствующей всем этим требованиям, [создайте бесплатную учетную запись](https://azure.microsoft.com/en-us/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Регистрация в Azure поставщика ресурсов для Azure Stack
> [!NOTE] 
> Этот шаг для каждой среды Azure Stack выполняется однократно.
>

1. Запустите PowerShell ISE от имени администратора.
2. Выполните вход с учетной записью владельца подписки Azure, указав параметр -EnvironmentName с ключом AzureCloud.
3. Зарегистрируйте поставщика ресурсов Azure "Microsoft.AzureStack".

Пример: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack -Force
```


## <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure

> [!NOTE]
>Выполните следующие действия на главном компьютере среды.
>

1. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md). 
2. Скопируйте файл скрипта [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) в любую локальную папку (например, C:\Temp).
3. Запустите интегрированную среду PowerShell ISE с правами администратора и импортируйте модуль RegisterWithAzure.    
4. Запустите модуль Add-AzsRegistration из скрипта RegisterWithAzure.psm1. Замените следующие заполнители: 
    - *YourCloudAdminCredential* — обозначает объект PowerShell, который содержит учетные данные в локальном домене для domain\cloudadmin (для пакета разработки это azurestack\cloudadmin).
    - *YourAzureSubscriptionID* — обозначает идентификатор подписки Azure, которую вы намерены использовать для регистрации Azure Stack.
    - *YourAzureDirectoryTenantName* — обозначает имя каталога клиента Azure, в котором вы создаете ресурс регистрации.
    - *YourPrivilegedEndpoint* — обозначает имя компьютера с минимальным уровнем доступа ( также называется виртуальной машиной аварийной консоли).

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
 
5. Нажмите клавишу ВВОД для каждого из двух запросов на подтверждение.
6. Во всплывающем окне введите учетные данные подписки Azure.



## <a name="verify-the-registration"></a>Проверка регистрации

1. Войдите на портал администратора Azure Stack (https://adminportal.local.azurestack.external).
2. Щелкните **Дополнительные службы** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).
3. Если отобразится список элементов, доступных в Azure (например, WordPress), значит, активация прошла успешно.

## <a name="next-steps"></a>Дальнейшие действия

[Подключение к Azure Stack](azure-stack-connect-azure-stack.md)


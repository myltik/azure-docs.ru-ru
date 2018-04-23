---
title: Регистрация ASDK в Azure | Документация Майкрософт
description: В этой статье объясняется, как зарегистрировать Azure Stack в Azure, чтобы включить синдикацию Marketplace и создание отчетов о потреблении.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0ee18a7038ea9a4eebb76bc6648b94940d1db5c7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure
Вы можете зарегистрировать устанавливаемый Пакет средств разработки Azure Stack (ASDK) в Azure, чтобы скачивать элементы Marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. Рекомендуем использовать регистрацию, так как она позволяет протестировать важные функции Azure Stack, например синдикацию Marketplace и отчеты о потреблении. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации. Но с пользователей ASDK не взимается плата на основе отчетов о потреблении.


## <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure 
Чтобы зарегистрировать ASDK в Azure, следуйте приведенным ниже инструкциям.

> [!NOTE]
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке. Для ASDK этот компьютер является главным компьютером, на котором размещается пакет средств разработки. 

Перед выполнением инструкций по регистрации ASDK в Azure установите PowerShell для Azure Stack и скачайте средства Azure Stack, как описано в статье о [настройке, выполняемой после установки ASDK](asdk-post-deploy.md). 

1. Откройте консоль PowerShell от имени администратора.  

2. Выполните следующие команды PowerShell, чтобы зарегистрировать установленный пакет ASDK в Azure (потребуется войти в подписку Azure и в учетную запись для локальной установки ASDK):

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Connect-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)

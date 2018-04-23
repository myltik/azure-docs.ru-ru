---
title: Добавление элемента Azure Stack Marketplace из локального источника | Документация Майкрософт
description: В этой статье объясняется, как добавить образ локальной операционной системы в Azure Stack Marketplace.
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
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 815dc055e19a38a61cbb3e927c3d7e92393b17dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Руководство. Добавление элемента Azure Stack Marketplace из локального источника

Чтобы пользователи могли развернуть виртуальную машину, вам как оператору Azure Stack нужно добавить образ виртуальной машины в Azure Stack Marketplace. По умолчанию в Azure Stack Marketplace ничего не публикуется, но вы можете отправить ISO-образы виртуальной машины, чтобы они стали доступными для пользователей. Используйте этот вариант, если Azure Stack развернут в сценарии без подключения или в сценариях с ограниченным подключением.

В этом руководстве вы скачаете образ виртуальной машины Windows Server 2016 на странице ознакомительной версии Windows Server и отправите его в Azure Stack Marketplace.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * добавление образа виртуальной машины Windows Server 2016;
> * проверка доступности образа виртуальной машины; 
> * Тестирование образа виртуальной машины

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

- Установите [совместимые с Azure Stack модули Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell).
- Скачайте [средства Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).
- Скачайте [ISO-образ виртуальной машины Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) на странице ознакомительной версии Windows Server.

## <a name="add-a-windows-server-vm-image"></a>Добавление образа виртуальной машины Windows Server
Вы можете опубликовать образ Windows Server 2016 в Azure Stack Marketplace, добавив ранее скачанный ISO-образ с помощью PowerShell. 

Используйте этот вариант, если Azure Stack развернут в сценарии без подключения или в сценариях с ограниченным подключением.

1. Импортируйте модули PowerShell `Connect` и `ComputeAdmin` для Azure Stack, которые содержатся в каталоге средств Azure Stack, используя следующие команды:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Выполните один из следующих скриптов на главном компьютере для ASDK, в зависимости от того, с помощью чего развернута среда Azure Stack: Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS).

  - Команды для **развертываний Azure AD**: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - Команды для **развертываний AD FS**:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Добавьте образ Windows Server 2016 в Azure Stack Marketplace. (Замените *fully_qualified_path_to_ISO* путем к скачанному ISO-образу Windows Server 2016.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>Проверка доступности элемента Marketplace
Чтобы проверить доступность нового образа виртуальной машины в Azure Stack Marketplace, выполните указанные ниже действия.

1. Войдите на [портал администратора ASDK](https://adminportal.local.azurestack.external).

2. Щелкните **Больше служб** > **Marketplace Management (Управление Marketplace)**. 

3. Убедитесь, что образ виртуальной машины Windows Server 2016 Datacenter успешно добавлен.

   ![Скачанный из Azure образ](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Тестирование образа виртуальной машины
Как оператор Azure Stack, вы можете создать тестовую виртуальную машину на [портале администратора](https://adminportal.local.azurestack.external), чтобы убедиться, что образ стал доступен. 

1. Войдите на [портал администратора ASDK](https://adminportal.local.azurestack.external).

2. Последовательно выберите **Создать** > **Вычисления** > **Windows Server 2016 Datacenter** > **Создать**.  
 ![Создание виртуальной машины из образа Marketplace](media/asdk-marketplace-item/new-compute.png)

3. В колонке **Основные сведения** задайте следующие параметры и нажмите кнопку **OK**:
  - **Имя** — test-vm-1;
  - **Имя пользователя** — AdminTestUser;
  - **Пароль** — AzS-TestVM01;
  - **Подписка** — оставьте подписку поставщика по умолчанию;
  - **Группа ресурсов** — test-vm-rg;
  - **Расположение** — local.

4. В колонке **Выбор размера** выберите **A1 Standard**, а затем щелкните **Выбрать**.  

5. В колонке **Параметры** оставьте значения по умолчанию и нажмите кнопку **ОК**.

6. В колонке **Сводка** нажмите кнопку **ОК**, чтобы создать виртуальную машину.  
> [!NOTE]
> Развертывание виртуальной машины занимает несколько минут.

7. Чтобы просмотреть данные новой виртуальной машины, выберите раздел **Виртуальные машины**, выполните поиск по имени **test-vm-1** и щелкните его.
    ![Отображение данных первой тестовой виртуальной машины](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда вы войдете на виртуальную машину и убедитесь, что тестовый образ работает правильно, удалите тестовую группу ресурсов. Таким образом вы освободите ограниченные ресурсы, доступные для установки ASDK на одном узле.

Когда группа ресурсов, виртуальная машина и все связанные ресурсы будут не нужны, удалите их. Для этого выполните следующие действия:

1. Войдите на [портал администратора ASDK](https://adminportal.local.azurestack.external).
2. Выберите **Группы ресурсов** > **test-vm-rg** > **Удалить группу ресурсов**.
3. Введите имя группы ресурсов **test-vm-rg** и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * добавление образа виртуальной машины Windows Server 2016;
> * проверка доступности образа виртуальной машины; 
> * тестирование образа виртуальной машины.

Перейдите к следующему руководству, чтобы узнать, как создать предложение и план Azure Stack.

> [!div class="nextstepaction"]
> [Предложение служб IaaS для Azure Stack](asdk-offer-services.md)

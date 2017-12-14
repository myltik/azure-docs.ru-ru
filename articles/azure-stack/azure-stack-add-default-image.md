---
title: "Добавление образа виртуальной машины по умолчанию в Azure Stack Marketplace | Документация Майкрософт"
description: "Добавление образа виртуальной машины Windows Server 2016 по умолчанию в Azure Stack Marketplace."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2849E53F-3D58-48A5-8007-3238FC39F630
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: ed62f2f8441220eb37aea7f4c848702e9821698b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Добавление образа виртуальной машины Windows Server 2016 в Azure Stack Marketplace

По умолчанию в Azure Stack Marketplace недоступны никакие образы виртуальных машин. Оператор Azure Stack должен добавить образ в Marketplace, чтобы предоставить пользователям доступ к нему. Образ Windows Server 2016 можно добавить в Azure Stack Marketplace с помощью одного из следующих методов:

* [Скачивание образа из Azure Marketplace.](#add-the-image-by-downloading-it-from-the-azure-marketplace) Используйте этот вариант, если вы работаете в сценарии с подключением и экземпляр Azure Stack зарегистрирован в Azure.

* [Добавление образа с помощью PowerShell.](#add-the-image-by-using-powershell) Используйте этот вариант, если Azure Stack развернут в сценарии без подключения или в сценариях с ограниченным подключением.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Добавление образа путем скачивания его из Azure Marketplace

1. Разверните Azure Stack и затем войдите в свой пакет средств разработки Azure Stack.

2. Щелкните **Больше служб** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure). 

3. Найдите образ **Windows Server 2016 Datacenter — Eval** и щелкните **Скачать**.

   ![Скачивание образа из Azure](media/azure-stack-add-default-image/download-image.png)

После завершения скачивания этот образ станет доступен в разделе **Marketplace Management** (Управление Marketplace). Этот образ также доступен в разделе **Виртуальные машины**.

## <a name="add-the-image-by-using-powershell"></a>Добавление образа с помощью PowerShell

### <a name="prerequisites"></a>Предварительные требования 

Выполните следующие предварительные требования с помощью либо [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), либо внешнего клиента на базе Windows (при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)).

1. Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  

2. Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

3. На странице Windows Server Evaluations (Ознакомительная версия Windows Server) [скачайте ознакомительную версию Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). При появлении запроса выберите для скачивания версию ISO. Запишите путь к расположению для скачивания, который будет использоваться в дальнейшем в действиях, описанных в этой статье. На этом шаге необходимо подключение к Интернету.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Добавления образа в Azure Stack Marketplace
   
1. Импортируйте модули Azure Stack Connect и ComputeAdmin с помощью следующих команд:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Войдите в среду Azure Stack. Выполните один из следующих скриптов, в зависимости от того, с помощью чего развернута среда Azure Stack (Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS)). (Замените Azure AD значение `tenantName`, конечную точку `GraphAudience` и значение `ArmEndpoint` в соответствии с конфигурацией своей среды.)  

   * **Azure Active Directory**. Выполните следующий командлет:

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Службы федерации Active Directory.** Выполните следующий командлет:
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

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

    Login-AzureRmAccount `
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

Чтобы убедиться, что образ виртуальной машины Windows Server 2016 имеет последний накопительный пакет обновления, укажите параметр `IncludeLatestCU` при выполнении командлета `New-AzsServer2016VMImage`. Дополнительные сведения о разрешенных параметрах для командлета  см. в `New-AzsServer2016VMImage` [этом разделе](#parameters). Публикация образа в Azure Stack Marketplace займет около часа. 

## <a name="parameters"></a>Параметры

|Параметры New-AzsServer2016VMImage|Обязательный?|Описание|
|-----|-----|------|
|ISOPath|Да|Полный путь к скачанному образу ISO Windows Server 2016.|
|Net35|Нет|Устанавливает среду выполнения .NET 3.5 в образе Windows Server 2016. По умолчанию установлено значение **true**.|
|Version (версия)|Нет|Указывает образы Windows Server 2016 **Базовый**, **Полный** или **Оба**. По умолчанию установлено значение **Полный**.|
|VHDSizeInMB|Нет|Задает размер (в МБ) образа VHD для добавления в среду Azure Stack. По умолчанию установлено значение 40 960 МБ.|
|CreateGalleryItem|Нет|Указывает, следует ли создать элемент Marketplace для образа Windows Server 2016. По умолчанию установлено значение **true**.|
|location |Нет |Указывает расположение, в которое должны публиковаться образы Windows Server 2016.|
|IncludeLatestCU|Нет|Применяет последнее накопительное обновление Windows Server 2016 для нового виртуального жесткого диска.|
|CUUri |Нет |Задает запуск накопительного обновления Windows Server 2016 из указанного URI. |
|CUPath |Нет |Задает запуск накопительного обновления Windows Server 2016 из локального пути. Этот вариант целесообразно использовать, если экземпляр Azure Stack развернут в отключенной среде.|

## <a name="next-steps"></a>Дальнейшие действия

[Подготовка виртуальной машины](azure-stack-provision-vm.md)

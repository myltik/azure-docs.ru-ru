---
title: "Добавление образа виртуальной машины по умолчанию в Azure Stack Marketplace | Документация Майкрософт"
description: "Добавление образа виртуальной машины Windows Server 2016 по умолчанию в Azure Stack Marketplace."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 43781cb025865df1d228376f57412f3d482d3ad0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Добавление образа виртуальной машины Windows Server 2016 в Azure Stack Marketplace

По умолчанию в Azure Stack Marketplace недоступны никакие образы виртуальных машин. Оператор Azure Stack должен добавить образ в Marketplace, чтобы пользователи могли использовать их. Образ Windows Server 2016 можно добавить в Azure Stack Marketplace с помощью одного из следующих методов:

* [Добавить образ, скачав его из Azure Marketplace](#add-the-image-by-downloading-it-from-the-Azure-marketplace). Используйте этот вариант, если вы работаете в сценарии с подключением и если экземпляр Azure Stack зарегистрирован в Azure.

* [Добавить образ с помощью PowerShell](#add-the-image-by-using-powershell). Используйте этот вариант, если Azure Stack развернут в сценарии без подключения или в сценариях с ограниченным подключением.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Добавление образа путем скачивания его из Azure Marketplace

1. После развертывания Azure Stack войдите в комплект разработки Azure Stack.

2. Щелкните **Больше служб** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure). 

3. Найдите образ **Windows Server 2016 Datacenter – Eval** и щелкните **Скачать**.

   ![Скачивание образа из Azure](media/azure-stack-add-default-image/download-image.png)

После завершения скачивания образ будет добавлен в колонку **Marketplace Management** (Управление Marketplace) и также будет доступен в колонке **Виртуальные машины**.

## <a name="add-the-image-by-using-powershell"></a>Добавление образа с помощью PowerShell

### <a name="prerequisites"></a>Предварительные требования 

Выполните следующие предварительные требования из [комплекта разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) или из внешнего клиента для Windows при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  

* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

* Перейдите по адресу https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016 и скачайте ознакомительную версию Windows Server 2016. При появлении запроса выберите для скачивания версию **ISO**. Запишите путь к расположению скачивания. Он используется далее в этом пошаговом руководстве. На этом шаге необходимо подключение к Интернету.  

Теперь выполните следующие действия, чтобы добавить образ в Azure Stack Marketplace:
   
1. Импортируйте модули Azure Stack Connect и ComputeAdmin с помощью следующих команд:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # import the Connect and ComputeAdmin modules   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Войдите в среду Azure Stack. Выполните следующий скрипт в зависимости от того, развертывается ли среда Azure Stack с помощью AAD или AD FS (обязательно замените значения AAD tenantName, GraphAudience и ArmEndpoint в соответствии с конфигурацией среды):  

   а. **Azure Active Directory**. Используйте следующий командлет:

   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"
   
   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
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

   b. **Службы федерации Active Directory (AD FS)**. Используйте следующий командлет:
    
   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
     -Name "AzureStackAdmin" `
     -GraphAudience $GraphAudience `
     -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS 
     -EnvironmentName AzureStackAdmin 

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```
   
3. Добавьте образ Windows Server 2016 в Azure Stack Marketplace (не забудьте заменить *Path_to_ISO* путем к скачанному ISO WS2016):

   ```PowerShell
   $ISOPath = "<Fully_Qualified_Path_to_ISO>"

   # Add a Windows Server 2016 Evaluation VM Image.
   New-AzsServer2016VMImage `
     -ISOPath $ISOPath

   ```

Чтобы убедиться, что образ виртуальной машины Windows Server 2016 имеет последний накопительный пакет обновления, укажите параметр `IncludeLatestCU` при выполнении командлета `New-AzsServer2016VMImage`. Дополнительные сведения о разрешенных параметрах для командлета `New-AzsServer2016VMImage` см. в разделе [Параметры](#parameters). Публикация образа в Azure Stack Marketplace займет около часа. 

## <a name="parameters"></a>Параметры

|Параметры New-AzsServer2016VMImage|Обязательный?|Описание|
|-----|-----|------|
|ISOPath|Да|Полный путь к скачанному образу ISO Windows Server 2016.|
|Net35|Нет|Этот параметр позволяет установить среду выполнения .NET 3.5 в образе Windows Server 2016. По умолчанию установлено значение true.|
|Version (версия)|Нет|Этот параметр позволяет выбрать, следует ли добавлять **базовый**, **полный** или **оба** образа Windows Server 2016. По умолчанию установлено значение Full.|
|VHDSizeInMB|Нет|Задает размер (в МБ) образа VHD для добавления в среду Azure Stack. По умолчанию установлено значение 40960 МБ.|
|CreateGalleryItem|Нет|Указывает, следует ли создать элемент Marketplace для образа Windows Server 2016. По умолчанию установлено значение true.|
|location |Нет |Указывает расположение, в которое должны публиковаться образы Windows Server 2016.|
|IncludeLatestCU|Нет|Установите этот переключатель, чтобы применить последнее накопительное обновление Windows Server 2016 для нового виртуального жесткого диска.|
|CUUri |Нет |Установите это значение для выбора накопительного обновления Windows Server 2016 из определенного универсального кода ресурса (URI). |
|CUPath |Нет |Установите это значение для выбора накопительного обновления Windows Server 2016 из определенного локального пути. Этот вариант целесообразно использовать, если экземпляр Azure Stack развернут в отключенной среде.|

## <a name="next-steps"></a>Дальнейшие действия

[Подготовка виртуальной машины](azure-stack-provision-vm.md)

---
title: "Установка PowerShell для Azure Stack | Документация Майкрософт"
description: "Узнайте, как установить PowerShell для Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 5555ee022e74b5d4791c5826ef80b166c4990a61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="install-powershell-for-azure-stack"></a>Установка PowerShell для Azure Stack  

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

Для работы с Azure Stack необходимы модули Azure PowerShell, совместимые с Azure Stack. В этом руководстве мы рассмотрим процесс установки PowerShell для Azure Stack. Приведенные в этой статье инструкции можно применять либо из пакета SDK для Azure Stack, либо из внешнего клиента на базе Windows (при подключении через VPN).

В этой статье содержатся подробные инструкции по установке PowerShell для Azure Stack. Если вам нужно быстро установить и настроить PowerShell, используйте скрипт из статьи [о начале работы с PowerShell](azure-stack-powershell-configure-quickstart.md). 

> [!NOTE]
> Для следующих действий требуется версия PowerShell 5.0. Чтобы проверить используемую версию, выполните $PSVersionTable.PSVersion и сравните номер основной версии.

Команды PowerShell для Azure Stack устанавливаются через коллекцию PowerShell. Чтобы зарегистрировать репозиторий PSGallery, откройте сеанс PowerShell с повышенными привилегиями из пакета SDK или из внешнего клиента на базе Windows (при подключении через VPN) и выполните следующую команду:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Удаление существующих версий PowerShell

Прежде чем устанавливать нужную версию, обязательно удалите все существующие модули Azure PowerShell. Есть два способа их удаления:

* Для удаления всех существующих модулей PowerShell выполните вход в пакет SDK или во внешний клиент на базе Windows (если нужно создать VPN-подключение). Закройте все активные сеансы PowerShell и выполните следующую команду: 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Выполните вход в пакет SDK или во внешний клиент на базе Windows (если нужно создать VPN-подключение). Удалите все папки, имена которых начинаются с Azure из папок `C:\Program Files (x86)\WindowsPowerShell\Modules` и `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`. При удалении этих папок удаляются все существующие модули PowerShell из областей пользователя AzureStackAdmin и global. 

В следующих разделах мы рассмотрим процесс установки PowerShell для Azure Stack. PowerShell можно установить в инфраструктуре Azure Stack, которая выполняется в подключенном, частично подключенном или автономном режиме. 

## <a name="install-powershell-in-a-connected-scenario"></a>Установка PowerShell в подключенном режиме 

Совместимые с Azure Stack модули AzureRM устанавливаются при помощи профилей версии API. Для использования Azure Stack требуется профиль версии API **2017-03-09-profile**, который можно получить при установке модуля AzureRM.Bootstrapper. Информацию о профилях и доступных в них командлетах см. в статье об [управлении профилями версии API](azure-stack-version-profiles.md). В дополнение к модулям AzureRM нужно установить модули PowerShell, предназначенные для Azure Stack. Выполните следующий скрипт PowerShell, чтобы установить эти модули на рабочей станции разработки:

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.10
  ```

Чтобы проверить установку, выполните следующую команду:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  Если установка выполнена успешно, в выходных данных указываются модули AzureRM и AzureStack.

## <a name="install-powershell-in-a-disconnected-or-in-a-partially-connected-scenario"></a>Установка PowerShell в отключенном или частично подключенном режиме

В отключенном режиме сначала нужно скачать модули PowerShell на компьютер с подключением к Интернету, а затем перенести их в расположение, где будет устанавливаться пакет SDK для Azure Stack.

1. Войдите на компьютер с подключением к Интернету и выполните скрипт ниже, чтобы скачать пакеты AzureRM и AzureStack на локальный компьютер:

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.10

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.10 
   ```

2. Скопируйте загруженные пакеты на USB-устройство.

3. Войдите в пакет SDK и скопируйте пакеты с USB-устройства в любое удобное расположение в пакете SDK. 

4. Затем зарегистрируйте это расположение в качестве репозитория по умолчанию и установите из этого репозитория модули AzureRM и AzureStack:

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>Дальнейшие действия

* [Download Azure Stack tools from GitHub](azure-stack-powershell-download.md) (Скачивание средств Azure Stack из GitHub).
* [Configure the Azure Stack user's PowerShell environment](user/azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack).  
* [Configure the Azure Stack operator's PowerShell environment](azure-stack-powershell-configure-admin.md) (Настройка среды PowerShell для оператора Azure Stack). 
* [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack).  

---
title: "Установка PowerShell для Azure Stack | Документация Майкрософт"
description: "Узнайте, как установить PowerShell для Azure Stack."
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
ms.date: 10/13/2017
ms.author: sngun
ms.openlocfilehash: 1c4c9aa36836398ad87c3655ff039a9dc8730456
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2017
---
# <a name="install-powershell-for-azure-stack"></a>Установка PowerShell для Azure Stack  

Для работы с Azure Stack необходимы модули Azure PowerShell, совместимые с Azure Stack. В этом руководстве мы рассмотрим процесс установки PowerShell для Azure Stack. Приведенные в этой статье инструкции можно применять либо из пакета SDK для Azure Stack, либо из внешнего клиента на базе Windows (при подключении через VPN).

В этой статье содержатся подробные инструкции по установке PowerShell для Azure Stack. Если же вам нужно быстро установить и настроить PowerShell, используйте скрипт из статьи о начале работы с PowerShell. 

> [!NOTE]
> Для следующих действий требуется версия PowerShell 5.0. Чтобы проверить используемую версию, выполните $PSVersionTable.PSVersion и сравните номер основной версии.

Команды PowerShell для Azure Stack устанавливаются с использованием коллекции PowerShell. Чтобы зарегистрировать репозиторий PSGallery, откройте сеанс PowerShell с повышенными привилегиями из пакета SDK или из внешнего клиента на базе Windows (при подключении через VPN) и выполните следующую команду:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Удаление существующих версий PowerShell

Прежде чем устанавливать нужную версию, обязательно удалите все существующие модули Azure PowerShell. Удалить их можно с помощью следующих двух способов.

* Чтобы удалить существующие модули PowerShell, войдите в пакет SDK или во внешний клиент под управлением Windows (если вы намерены создать VPN-подключение). Закройте все активные сеансы PowerShell и выполните следующую команду: 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Выполните вход в пакет SDK или во внешний клиент под управлением Windows (если вы намерены создать VPN-подключение). Удалите все папки, имена которых начинаются с "Azure" из папок `C:\Program Files (x86)\WindowsPowerShell\Modules` и `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`. При удалении этих папок удаляются все существующие модули PowerShell из областей пользователя: глобальной и AzureStackAdmin. 

В следующих разделах мы рассмотрим процесс установки PowerShell для Azure Stack. PowerShell можно установить в инфраструктуре Azure Stack, которая выполняется в подключенном, частично подключенном или автономном режиме. 

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Установка PowerShell в подключенном режиме (с подключением к Интернету)

Совместимые с Azure Stack модули AzureRM устанавливаются с использованием профилей версии API. Для использования Azure Stack требуется профиль версии API **2017-03-09-profile**, который можно получить при установке модуля AzureRM.Bootstrapper. Информацию о профилях версии API и доступных в них командлетах вы найдете в [этом документе](azure-stack-version-profiles.md). В дополнение к модулям AzureRM нужно установить модули PowerShell, предназначенные для Azure Stack. Выполните следующий скрипт PowerShell, чтобы установить эти модули на рабочей станции разработки:

> [!IMPORTANT]
> Выпуск модуля AzureRM 1.2.11 PowerShell поставляется со списком критически важных изменений. Чтобы обновить версию 1.2.10, см. руководство по миграции по адресу [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

Чтобы подтвердить установку, выполните следующую команду:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  Если установка выполнена успешно, в выходных данных указываются модули AzureRM и AzureStack.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Установка PowerShell в отключенном или частично подключенном режиме (с ограниченным подключением к Интернету)

В отключенном или частично подключенном режиме вам следует сначала скачать модули PowerShell на компьютер с подключением к Интернету, а затем перенести их туда, где будет устанавливаться пакет SDK для Azure Stack.

> [!IMPORTANT]
> Выпуск модуля AzureRM 1.2.11 PowerShell поставляется со списком критически важных изменений. Чтобы обновить версию 1.2.10, см. руководство по миграции по адресу [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

1. Войдите на компьютер с подключением к Интернету и выполните следующий скрипт для скачивания пакетов AzureRM и AzureStack на локальный компьютер:

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11 
   ```

2. Скопируйте скачанные пакеты на USB-устройство.

3. Войдите в пакет SDK и скопируйте пакеты с USB-устройства в нужное расположение в пакете SDK. 

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
* [Настройка пользовательской среды PowerShell в Azure Stack](azure-stack-powershell-configure-user.md)  
* [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack)  

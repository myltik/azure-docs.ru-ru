---
title: Установка PowerShell для Azure Stack | Документация Майкрософт
description: Узнайте, как установить PowerShell для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 8fea502457275c89d99084a5b025b620872d796b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075312"
---
# <a name="install-powershell-for-azure-stack"></a>Установка PowerShell для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Для работы с Azure Stack необходимы модули Azure PowerShell, совместимые с Azure Stack. В этом руководстве мы рассмотрим процесс установки PowerShell для Azure Stack.

В этой статье содержатся подробные инструкции по установке PowerShell для Azure Stack.

> [!Note]
> Для следующих действий требуется версия PowerShell 5.0. Чтобы проверить используемую версию, выполните $PSVersionTable.PSVersion и сравните **номер основной версии**.

Команды PowerShell для Azure Stack устанавливаются с использованием коллекции PowerShell. Можно использовать приведенную ниже процедуру, чтобы проверить, зарегистрирована ли коллекция PSGallery в качестве репозитория. Откройте сеанс PowerShell с повышенными привилегиями и выполните следующую команду.

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Если репозиторий не зарегистрирован, откройте сеанс Windows PowerShell с повышенными привилегиями и выполните в нем следующую команду.

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> На этом шаге необходим доступ к Интернету. 

## <a name="uninstall-existing-versions-of-powershell"></a>Удаление существующих версий PowerShell

Прежде чем устанавливать нужную версию, обязательно удалите все установленные ранее модули PowerShell для Azure Stack. Удалить их можно с помощью следующих двух способов.

 - Чтобы удалить модули PowerShell, закройте все активные сеансы PowerShell и выполните следующую команду.

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Удалите все папки, имена которых начинаются с "Azure" из папок `C:\Program Files\WindowsPowerShell\Modules` и `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`. При удалении этих папок удаляются все установленные модули PowerShell.

В следующих разделах мы рассмотрим процесс установки PowerShell для Azure Stack. PowerShell можно установить в инфраструктуре Azure Stack, которая выполняется в подключенном, частично подключенном или автономном режиме.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Установка PowerShell в подключенном режиме (с подключением к Интернету)

Совместимые с Azure Stack модули AzureRM устанавливаются с использованием профилей версии API. Для использования Azure Stack требуется профиль версии API **2017-03-09-profile**, который можно получить при установке модуля AzureRM.Bootstrapper. Информацию о профилях версии API и доступных в них командлетах вы найдете в [этом документе](user/azure-stack-version-profiles.md). В дополнение к модулям AzureRM нужно установить модули PowerShell, предназначенные для Azure Stack. Выполните следующий скрипт PowerShell, чтобы установить эти модули на рабочей станции разработки:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.2.12 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.2.12 

# Install Module Version 1.2.11 if Azure Stack is running a lower version then 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Чтобы подтвердить установку, выполните следующую команду:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Если установка выполнена успешно, в выходных данных указываются модули AzureRM и AzureStack.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Установка PowerShell в отключенном или частично подключенном режиме (с ограниченным подключением к Интернету)

В автономном режиме вам следует сначала скачать модули PowerShell на компьютер с подключением к Интернету, а затем перенести их туда, где будет устанавливаться Пакет средств разработки Azure Stack.

> [!IMPORTANT]
> Выпуск модуля PowerShell AzureRM 1.2.12 поставляется со списком критически важных изменений. Чтобы выполнить обновление с версии 1.2.10, см. [руководство по миграции](https://github.com/bganapa/azure-powershell/blob/stack-migration/documentation/migration-guides/Stack/migration-guide.1.2.12.md).

1. Войдите на компьютер с подключением к Интернету и выполните следующий скрипт для скачивания пакетов AzureRM и AzureStack на локальный компьютер:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.12

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.12 
   ```

> [!Important]  
> Если вы не используете Azure Stack с обновлением 1804 или более поздней версии, измените значение параметра **requiredversion** на `1.2.11`. 

2. Скопируйте скачанные пакеты на USB-устройство.

3. Войдите на рабочую станцию и скопируйте пакеты с USB-устройства в нужное расположение на ней.

4. Затем зарегистрируйте это расположение в качестве репозитория по умолчанию и установите из этого репозитория модули AzureRM и AzureStack:

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Настройка PowerShell для использования прокси-сервера

Если для доступа к Интернету требуется прокси-сервер, необходимо сначала настроить PowerShell для использования существующего прокси-сервера.

1. Откройте командную строку PowerShell с повышенными привилегиями.
2. Выполните следующие команды:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Дополнительная информация

 - [Download Azure Stack tools from GitHub](azure-stack-powershell-download.md) (Скачивание средств Azure Stack из GitHub).
 - [Configure the Azure Stack user's PowerShell environment](user/azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack).  
 - [Configure the Azure Stack operator's PowerShell environment](azure-stack-powershell-configure-admin.md) (Настройка среды PowerShell для оператора Azure Stack). 
 - [Manage API version profiles in Azure Stack](user/azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack).  

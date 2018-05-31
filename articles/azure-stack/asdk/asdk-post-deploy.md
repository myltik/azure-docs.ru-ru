---
title: Настройка Пакета средств разработки Azure Stack (ASDK) после его развертывания | Документация Майкрософт
description: Описание изменений, которые мы рекомендуем внести в конфигурацию после установки Пакета средств разработки Azure Stack (ASDK).
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 4b58f3496b25e4fc04761b9df6e27f8313b35fe9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204638"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Настройка, выполняемая после установки ASDK
После [установки ASDK](asdk-install.md) мы рекомендуем внести некоторые изменения в конфигурацию. 

## <a name="install-azure-stack-powershell"></a>Установка PowerShell для Azure Stack 
Для работы с Azure Stack необходимы модули Azure PowerShell, совместимые с Azure Stack.

Команды PowerShell для Azure Stack устанавливаются с использованием коллекции PowerShell. Чтобы зарегистрировать репозиторий PSGallery, откройте сеанс Windows PowerShell с повышенными привилегиями и выполните в нем следующие команды.

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Совместимые с Azure Stack модули AzureRM устанавливаются с использованием профилей версии API. Для использования Azure Stack требуется профиль версии API 2017-03-09-profile, который можно получить при установке модуля AzureRM.Bootstrapper. 
 
 Последнюю версию модуля PowerShell для Azure Stack можно установить в двух режимах: с подключением главного компьютера ASDK к Интернету или без него.

> [!IMPORTANT]
> Прежде чем устанавливать нужную версию, обязательно [удалите все установленные модули Azure PowerShell](.\.\azure-stack-powershell-install.md#uninstall-existing-versions-of-powershell).

- **При наличии подключения к Интернету** с главного компьютера ASDK. Выполните следующий скрипт PowerShell, чтобы установить эти модули там, где установлен пакет средств разработки.

  ``` PowerShell
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
  Если установка выполнена успешно, в выходных данных указываются модули AzureRM и AzureStack.

- **Без подключения к Интернету** с главного компьютера ASDK. При установке в автономном режиме следует заранее скачать модули PowerShell на компьютер, подключенный к Интернету, с помощью следующих команд PowerShell:

  ```PowerShell
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
  После этого перенесите скачанные пакеты на компьютер ASDK и зарегистрируйте это расположение в качестве репозитория по умолчанию, а затем установите модули AzureRM и AzureStack из этого репозитория:

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

## <a name="download-the-azure-stack-tools"></a>Скачивание средств Azure Stack
[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) — это репозиторий GitHub, в котором размещены модули PowerShell для администрирования и развертывания ресурсов в Azure Stack. Чтобы получить эти средства, клонируйте репозиторий GitHub или скачайте папку AzureStack-Tools, выполнив следующий скрипт:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Проверка установки ASDK
Чтобы убедиться, что развертывание ASDK прошло успешно, примените командлет Test-AzureStack следующим образом:

1. Войдите на узел управления с именем AzureStack\CloudAdmin.
2. Откройте PowerShell от имени администратора (не путайте с интегрированной средой сценариев Windows PowerShell).
3. Выполните команду `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Выполните команду `Test-AzureStack`

Выполнение тестов может занять несколько минут. Если установка выполнена успешно, выходные данные выглядят примерно так:

![Test-AzureStack](media/asdk-post-deploy/test-azurestack.png)

Если операция закончится сбоем, выполните рекомендации из раздела об устранении неполадок.

## <a name="activate-the-administrator-and-tenant-portals"></a>Активация порталов администрирования и клиента
После развертывания с использованием Azure Active Directory необходимо активировать порталы администрирования и клиента Azure Stack. Эта активация заключается в предоставлении порталу Azure Stack и Azure Resource Manager требуемых разрешений (перечисленные на странице принятия условий) для всех пользователей каталога.

- На портале администратора найдите https://adminportal.local.azurestack.external/guest/signup, прочтите информацию и подтвердите согласие кнопкой **Accept** (Принять). После этого вы можете добавить администраторов служб, не являющихся также администраторами каталога клиента.

- На портале администратора найдите https://portal.local.azurestack.external/guest/signup, прочтите информацию и подтвердите согласие кнопкой **Accept** (Принять). После этого пользователи в каталоге смогут войти на портал клиента. 

> [!NOTE] 
> Если порталы не активированы, доступ к ним будут иметь только администраторы. Если на портал войдет другой пользователь, отобразится сообщение о том, что администратор не предоставил другим пользователям необходимые разрешения. Если администратор не принадлежит к каталогу, в котором зарегистрирована инфраструктура Azure Stack, к каталогу Azure Stack необходимо добавить URL-адрес активации. Например, если Azure Stack зарегистрирован на fabrikam.onmicrosoft.com, а полномочия администратора предоставлены пользователю admin@contoso.com, для активации портала нужно открыть адрес https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com. 

## <a name="reset-the-password-expiration-policy"></a>Политика сброса срока действия пароля 
Чтобы убедиться, что срок действия пароля узла комплекта разработки не завершится до окончания периода ознакомления, выполните следующие действия после развертывания ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Для изменения политики срока действия паролей из PowerShell сделайте следующее:
В консоли Powershell с повышенными привилегиями выполните команду:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Для изменения политики срока действия паролей вручную сделайте следующее:
1. На компьютере с пакетом средств разработки откройте **Управление групповыми политиками** (GPMC.MMC) и последовательно выберите **Управление групповой политикой**, **Лес: azurestack.local**, **Домены**, **azurestack.local**.
2. Щелкните правой кнопкой мыши **политику домена по умолчанию** и выберите **Изменить**.
3. В редакторе "Управление групповыми политиками" последовательно выберите **Конфигурация компьютера**, **Политики**, **Параметры Windows**, **Параметры безопасности**, **Политики учетных записей**, **Политика паролей**.
4. В области справа дважды щелкните параметр **Максимальный срок действия пароля**.
5. В диалоговом окне **Maximum password age Properties** (Свойства максимального срока действия пароля) измените значение параметра **Срок истечения действия пароля** на **180** и нажмите кнопку **ОК**.

![Консоль управления групповой политикой](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Дополнительная информация
[Регистрация ASDK в Azure AD](asdk-register.md)

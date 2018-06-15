---
title: Разрешение приложениям получать секреты из хранилища ключей Azure Stack | Документация Майкрософт
description: Использование примера приложения для работы с хранилищем ключей Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: mabrigg
ms.openlocfilehash: 39bce286c756660cd8755358cf98f2c8d35ce351
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807386"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Пример приложения, использующего ключи и секреты, хранящиеся в хранилище ключей

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Выполните инструкции из этой статьи, чтобы запустить пример приложения (HelloKeyVault), которое извлекает ключи и секреты из хранилища ключей в Azure Stack.

## <a name="prerequisites"></a>предварительным требованиям

Вы можете установить указанные ниже обязательные компоненты из [Пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) Azure Stack либо через внешнего клиента для Windows (при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)).

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Создание и получение хранилища ключей и параметров приложения

Чтобы подготовить пример приложения, выполните следующие действия:

* Создайте хранилище ключей в Azure Stack.
* Зарегистрируйте приложение в Azure Active Directory.

Подготовить пример приложения можно с помощью портала Azure или PowerShell. В этой статье показано, как создать хранилище ключей и зарегистрировать приложение с помощью PowerShell.

>[!NOTE]
>По умолчанию этот скрипт PowerShell создает приложение в Active Directory. Но его можно использовать и для регистрации существующего приложения.

 Прежде чем запускать приведенный ниже скрипт, не забудьте предоставить значения для переменных `aadTenantName` и `applicationPassword`. Если вы не укажете значение для переменной `applicationPassword`, скрипт создаст случайный пароль.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

```

На следующем снимке экрана показаны выходные данные скрипта, используемого для создания хранилища ключей:

![Хранилище ключей с ключами доступа](media/azure-stack-kv-sample-app/settingsoutput.png)

Запишите значения **VaultUrl**, **AuthClientId** и **AuthClientSecret**, возвращенные предыдущим скриптом. Используйте их для запуска приложения HelloKeyVault.

## <a name="download-and-configure-the-sample-application"></a>Скачивание и настройка примера приложения

Скачайте пример хранилища ключей со страницы [примеров клиентов хранилища ключей](https://www.microsoft.com/en-us/download/details.aspx?id=45343) Azure. Извлеките содержимое ZIP-файла на рабочей станции разработки. Будет создана папка samples с двумя приложениями. В этой статье мы используем только HelloKeyVault.

Чтобы применить пример HelloKeyVault, сделайте следующее:

* Перейдите к папке **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault**.
* Откройте приложение HelloKeyVault в Visual Studio.

### <a name="configure-the-sample-application"></a>Настройка примера приложения

В Visual Studio:

* Откройте файл HelloKeyVault\App.config и найдите в нем элемент &lt;**appSettings**&gt;.
* Сохраните для ключей **VaultUrl**, **AuthClientId** и **AuthClientSecret** значения, которые были возвращены при создании хранилища ключей. (По умолчанию файл App.config содержит заполнитель для параметра *AuthCertThumbprint*. Замените его значением *AuthClientSecret*.)

  ![Параметры приложения](media/azure-stack-kv-sample-app/appconfig.png)

* Повторно создайте решение.

## <a name="run-the-application"></a>Выполнение приложения

При запуске приложение HelloKeyVault входит в Azure AD и применяет маркер AuthClientSecret для аутентификации в хранилище ключей Azure Stack.

Пример HelloKeyVault позволяет выполнить следующие действия:

* запустить основные операции, например создание, шифрование, заключение в оболочку или удаление ключей и секретных данных;
* передать приложению HelloKeyVault параметры, например *encrypt* и *decrypt*, чтобы применить указанные изменения к хранилищу ключей.

## <a name="next-steps"></a>Дополнительная информация

[Развертывание виртуальной машины с помощью пароля из хранилища ключей](azure-stack-kv-deploy-vm-with-secret.md)

[Create a virtual machine and include certificate retrieved from a key vault](azure-stack-kv-push-secret-into-vm.md) (Создание виртуальной машины с сертификатом хранилища ключей)

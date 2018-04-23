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
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: c32667f240c9e825a82b1e9623c672b00ac999ed
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Пример приложения, использующего ключи и секретные данные, хранящиеся в хранилище ключей

В этой статье показано, как запустить пример приложения (HelloKeyVault), которое извлекает ключи и секреты из хранилища ключей в Azure Stack.

## <a name="prerequisites"></a>предварительным требованиям 

Выполните следующие предварительные требования из [комплекта разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) или из внешнего клиента для Windows при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Создание и получение хранилища ключей и параметров приложения

Сначала необходимо создать хранилище ключей в Azure Stack и зарегистрировать приложение в Azure Active Directory (Azure AD). Вы можете создать и зарегистрировать хранилища ключей с помощью портала Azure или PowerShell. В этой статье используется PowerShell. По умолчанию приведенный ниже скрипт PowerShell создает приложение в Active Directory. Однако можно также использовать одно из имеющихся приложений. Предоставьте значения для переменных `aadTenantName` и `applicationPassword`. Если для переменной `applicationPassword` не указать значение, скрипт создаст случайный пароль. 

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
Connect-AzureRmAccount `
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

# Create a new resource group and a key vault within that resource group.
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

На следующем снимке экрана показаны выходные данные предыдущего скрипта:

![Конфигурация приложения](media/azure-stack-kv-sample-app/settingsoutput.png)

Запишите значения **VaultUrl**, **AuthClientId** и **AuthClientSecret**, возвращенные предыдущим скриптом. Используйте их для запуска приложения HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Скачивание и запуск примера приложения

Скачайте пример хранилища ключей со страницы [примеров клиентов хранилища ключей](https://www.microsoft.com/en-us/download/details.aspx?id=45343) Azure. Извлеките содержимое ZIP-файла на рабочей станции разработки. В папке примеров есть два примера. В этой статье используется пример HelloKeyVault. Перейдите к папке **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault** и откройте приложение HelloKeyVault в Visual Studio. 

Откройте файл HelloKeyVault\App.config и замените значения элемента <appSettings> значениями **VaultUrl**, **AuthClientId** и **AuthClientSecret**, возвращенными предыдущим скриптом. Обратите внимание, что по умолчанию App.config содержит заполнитель для *AuthCertThumbprint*, но использует вместо него *AuthClientSecret*. После замены параметров перестройте решение и запустите приложение.

![Параметры приложения](media/azure-stack-kv-sample-app/appconfig.png)
 
Приложение входит в Azure AD, а затем использует этот маркер для проверки подлинности в хранилище ключей Azure Stack. Приложение выполняет такие операции, как создание, шифрование, оборачивание и удаление ключей и секретов из хранилища ключей. В приложение можно также передать определенные параметры, такие как *encrypt* и *decrypt*, что гарантирует, что приложение выполнит с хранилищем только эти операции. 


## <a name="next-steps"></a>Дополнительная информация
[Развертывание виртуальной машины с помощью пароля из хранилища ключей](azure-stack-kv-deploy-vm-with-secret.md)

[Create a virtual machine and include certificate retrieved from a key vault](azure-stack-kv-push-secret-into-vm.md) (Создание виртуальной машины с сертификатом хранилища ключей)




---
title: "Подключение к Azure Stack при помощи интерфейса командной строки | Документация Майкрософт"
description: "Узнайте, как использовать межплатформенный интерфейс командной строки (CLI) для развертывания ресурсов и управления ими в Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: sngun
ms.openlocfilehash: bd731c32d32063b54d5899db3b3a13a911ca79be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Установка и настройка интерфейса командной строки для работы с Azure Stack

В этом документе мы расскажем вам о процессе использование интерфейса командной строки (CLI) Azure для управления ресурсами пакета SDK для Azure Stack на клиентских платформах Linux и Mac. 

## <a name="export-the-azure-stack-ca-root-certificate"></a>Экспорт корневого сертификата ЦС Azure Stack

Если вы используете CLI на виртуальной машине, на которой выполняется среда пакета SDK для Azure Stack, значит, корневой сертификат Azure Stack уже установлен на ней и вы можете его получить напрямую. Если же CLI выполняется на рабочей станции за пределами пакета SDK, необходимо экспортировать корневой сертификат ЦС Azure Stack из пакета SDK и добавить его в хранилище сертификатов Python на рабочей станции разработки (внешний компьютер на платформе Mac или Linux). 

Войдите в пакет разработки и выполните приведенный ниже скрипт, чтобы экспортировать корневой сертификат Azure Stack в PEM-формате.

```powershell
   $label = "AzureStackSelfSignedRootCert"
   Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
   $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
   if (-not $root)
   {
       Log-Error "Cerficate with subject CN=$label not found"
       return
   }

   Write-Host "Exporting certificate"
   Export-Certificate -Type CERT -FilePath root.cer -Cert $root

   Write-Host "Converting certificate to PEM format"
   certutil -encode root.cer root.pem
```

## <a name="install-cli"></a>Установка CLI

Теперь войдите на рабочую станцию разработки и установите CLI. Для Azure Stack требуется Azure CLI версии 2.0, для установки которой можно использовать инструкции в статье [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Чтобы проверить успешность установки, откройте окно терминала или командной строки и выполните следующую команду:

```azurecli
az --version
```

Отобразятся номера версий Azure CLI и зависимых библиотек, установленных на этом компьютере.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python. Если вы используете CLI на компьютере Linux, который создан в среде Azure Stack, выполните следующую команду bash:

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Если вы используете CLI на компьютере, расположенном за пределами среды Azure Stack, сначала нужно настроить [VPN-подключение к Azure Stack](azure-stack-connect-azure-stack.md). После этого скопируйте PEM-файл сертификата, который вы ранее экспортировали на рабочую станцию разработки, и выполните следующие команды в зависимости от ОС на рабочей станции разработки:

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash=(Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash=(Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash=(Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry  = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Настройка конечной точки псевдонимов для виртуальных машин

Чтобы пользователи могли создавать виртуальные машины с помощью CLI, администратору облака следует настроить общедоступную конечную точку для хранения псевдонимов образов виртуальных машин и зарегистрировать эту конечную точку в облаке. Для этого используется параметр `endpoint-vm-image-alias-doc` в команде `az cloud register`. Прежде чем добавлять образ на конечной точке псевдонимов образов, администратор облака должен передать его в Azure Stack Marketplace.
   
Например, Azure использует следующий адрес URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. Администратор облака создает аналогичную конечную точку для Azure Stack с информацией об образах, которые доступны в Azure Stack Marketplace.

## <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

Для подключения к Azure Stack выполните следующие действия:

1. Зарегистрируйте среду Azure Stack, выполнив команду az cloud register.
   
   а. Чтобы зарегистрировать среду **администратора облака**, используйте следующую команду:

   ```azurecli
   az cloud register \ 
     -n AzureStackAdmin \ 
     --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
     --suffix-storage-endpoint "local.azurestack.external" \ 
     --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
     --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
     --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
   ```

   b. Чтобы зарегистрировать среду **пользователя**, используйте команду:

   ```azurecli
   az cloud register \ 
     -n AzureStackUser \ 
     --endpoint-resource-manager "https://management.local.azurestack.external" \ 
     --suffix-storage-endpoint "local.azurestack.external" \ 
     --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
     --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
     --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
   ```

2. Следующие команды позволяют выбрать активную среду:

   а. В среде **администратора облака** используйте такую команду:

   ```azurecli
   az cloud set \
     -n AzureStackAdmin
   ```

   b. В среде **пользователя** используйте команду:

   ```azurecli
   az cloud set \
     -n AzureStackUser
   ```

3. Укажите в конфигурации среды версию API-интерфейса, специально предназначенную для Azure Stack. Чтобы изменить эту конфигурацию, выполните следующую команду:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Войдите в среду Azure Stack с помощью команды **az login**. Вы можете войти в среду Azure Stack от имени пользователя или [субъекта-службы](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects). 

   * Для входа от имени **пользователя** можно указать имя пользователя и пароль непосредственно в команде az login или выполнить аутентификацию в браузере. Если для вашей учетной записи включена многофакторная идентификация, возможным будет только второй вариант.

   ```azurecli
   az login \
     -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
     --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
   ```

   > [!NOTE]
   > Если в учетной записи пользователя используется многофакторная идентификация, команду az login можно выполнять без параметра -u. При отсутствии этого параметра команда возвращает URL-адрес и код, которые следует использовать для аутентификации.
   
   * Для входа от имени **субъекты-службы** следует заранее [создать субъект-службу с помощью портала Azure](azure-stack-create-service-principals.md) или CLI, а также назначить ему роль. После этого выполните такую команду для входа:

   ```azurecli
   az login \
     --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
     --service-principal \
     -u <Application Id of the Service Principal> \
     -p <Key generated for the Service Principal>
   ```

## <a name="test-the-connectivity"></a>Проверка подключения

После установки всех настроек можно приступить к созданию ресурсов в Azure Stack с помощью CLI. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Если группа ресурсов будет успешно создана, команда выше возвратит следующие свойства созданного ресурса:

![выходные данные при создании группы ресурсов](media/azure-stack-connect-cli/image1.png)

## <a name="next-steps"></a>Дальнейшие действия

[Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)

[Управление разрешениями пользователей](azure-stack-manage-permissions.md)


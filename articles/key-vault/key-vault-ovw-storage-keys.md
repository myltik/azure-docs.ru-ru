---
ms.assetid: ''
title: Ключи учетной записи хранения Azure Key Vault
description: Ключи учетной записи хранения обеспечивают простую интеграцию между Azure Key Vault и доступом по ключу к учетной записи хранения Azure.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179594"
---
# <a name="azure-key-vault-storage-account-keys"></a>Ключи учетной записи хранения Azure Key Vault

До появления ключей учетной записи хранения Azure Key Vault разработчикам приходилось управлять ключами учетной записи хранения Azure (ASA) и сменять их вручную или с помощью внешних средств автоматизации. Сейчас ключи учетной записи хранения Key Vault реализованы в качестве [секретов Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) для проверки подлинности с помощью учетной записи хранения Azure.

Благодаря ключу учетной записи хранения Azure управление сменой секретов происходит автоматически, а также устраняется необходимость прямого контакта с ключом ASA за счет использования подписанных URL-адресов (SAS).

Дополнительные сведения об учетных записях хранения Azure см. в статье [Об учетных записях хранения Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Поддержка интерфейсов

Полный список наших интерфейсов программирования и сценариев, а также ссылки на них см. в [руководстве разработчика Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Чем управляет Key Vault

Служба Key Vault выполняет несколько внутренних функций управления от вашего имени, если вы используете управляемые ключи учетной записи хранения.

- Служба хранилища Azure Key Vault управляет ключами учетной записи хранения Azure (ASA).
    - На внутреннем уровне Azure Key Vault может выводить список (синхронизировать) ключи с помощью учетной записи хранения Azure.
    - Azure Key Vault периодически повторно создает (сменяет) ключи.
    - Значения ключей никогда не возвращаются в ответе вызывающему объекту.
    - Azure Key Vault управляет ключами как учетных записей хранения, так и классических учетных записей хранения.
- Azure Key Vault позволяет владельцу хранилища или объекта создавать определения SAS (учетной записи или службы).
    - Значение SAS, созданное с помощью определения SAS, возвращается в качестве секрета через путь универсального кода ресурса (URI) REST. Дополнительные сведения см. в статье [Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations) (Операции с учетными записями хранения Azure Key Vault).

## <a name="naming-guidance"></a>Рекомендации по именованию

- Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.
- Имя определения SAS должно быть 1–102 символа в длину и содержать только символы 0–9, a–z, A–Z.

## <a name="developer-experience"></a>Возможности для разработчика

### <a name="before-azure-key-vault-storage-keys"></a>До появления ключей Azure Key Vault

Разработчикам приходилось применять следующие методы, чтобы получить доступ к хранилищу Azure с помощью ключа учетной записи хранения.
1. Сохранять строку подключения или маркер SAS в параметрах приложения службы приложений Azure или в другом хранилище.
1. Извлекать строку подключения или маркер при запуске приложения.
1. Создавать [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) для взаимодействия с хранилищем.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>После появления ключей Azure Key Vault

Теперь разработчику нужно создать [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient), который позволяет получить маркер SAS для используемого хранилища. На основе этого маркера он создает [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount).

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Руководство для разработчика

- Разрешите только Key Vault управлять ключами ASA. Не пытайтесь управлять ими самостоятельно. Это повлияет на процессы Key Vault.
- Ключи ASA не должны управляться более чем одним объектом Key Vault.
- Если необходимо вручную повторно создать ключи ASA, рекомендуем делать это через Key Vault.

## <a name="getting-started"></a>Приступая к работе

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Настройка разрешений для управления доступом на основе ролей (RBAC)

Для удостоверения приложения Azure Key Vault необходимы разрешения на *вывод списка* и *повторное создание* ключей учетной записи хранения. Настройте эти разрешения, выполнив следующие действия:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Действующий пример

В следующем примере показано создание учетной записи хранения Azure под управлением Key Vault и связанных определений подписанного URL-адреса (SAS).

### <a name="prerequisite"></a>Предварительные требования

Обязательно выполните инструкции в разделе [Настройка разрешений для управления доступом на основе ролей (RBAC)](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Настройка

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Затем задайте для **учетной записи** разрешения на доступ, позволяющие управлять всеми разрешениями в Key Vault. В приведенном ниже примере используется учетная запись Azure _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Создание управляемой учетной записи хранения в Key Vault

Теперь создайте управляемую учетную запись хранения в Azure Key Vault и маркеры SAS на основе ключей доступа из этой учетной записи хранения.
- `-ActiveKeyName` использует ключ key2 для создания маркеров SAS.
- `-AccountName` определяет управляемую учетную запись хранения. Ниже мы для простоты используем имя, совпадающее с именем учетной записи хранения, но оно может быть любым.
- `-DisableAutoRegenerateKey` указывает, что ключи учетной записи хранения не нужно создавать повторно.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Повторное создание ключей

Если нужно, чтобы хранилище ключей автоматически повторно создавало ключи доступа с некоторой периодичностью, вы можете настроить этот период. Ниже мы указываем для повторного создания период в 3 дня. Через 3 дня хранилище ключей повторно создаст ключ key1 и установит его в качестве активного вместо key2.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Настройка определений SAS

SAS учетной записи позволяет получить доступ к службе BLOB-объектов с другими разрешениями.
Настройте определения SAS в Key Vault для управляемой учетной записи хранения.
- `-AccountName` — это имя управляемой учетной записи хранения в Key Vault.
- `-Name` — идентификатор маркера SAS в этом хранилище.
- `-ValidityPeriod` — дата окончания срока действия для создаваемого маркера SAS.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Получение маркеров SAS

Получите соответствующие токены SAS и выполните вызовы к хранилищу. `-SecretName` создается на основе входных данных `AccountName` и параметров `Name` при выполнении команды [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Создание хранилища

Обратите внимание, что попытка получить доступ с помощью *$readSasToken* завершается ошибкой, но нам удается получить доступ с помощью *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Доступ к содержимому большого двоичного объекта хранилища можно получить с помощью токена SAS с доступом на запись.

### <a name="relevant-powershell-cmdlets"></a>Соответствующие командлеты PowerShell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Подключение учетной записи хранения

Пример: владелец объекта Key Vault добавляет объект учетной записи хранилища в Azure Key Vault, чтобы подключить учетную запись хранения.

Во время подключения Key Vault проверяет, что удостоверение подключаемой учетной записи имеет разрешения на *перечисление* и *повторное создание* ключей хранилища. Чтобы проверить эти разрешения, Key Vault получает токен OBO (от имени) от службы аутентификации, указывает Azure Resource Manager в качестве целевой аудитории, а затем обращается к службе хранилища Azure для *получения списка*. Если вызов *списка* завершается неудачей, создание объекта Key Vault завершается ошибкой с кодом состояния HTTP *Запрещено*. Ключи, указанные таким способом, кэшируются с помощью хранилища сущностей хранилища ключей.

Key Vault необходимо убедиться, что идентификатор имеет разрешения на *повторное создание*, прежде чем предоставить ему права на повторное создание ключей. Чтобы убедиться, что идентификатор (через токен OBO), а также основной идентификатор Key Vault имеют следующие разрешения:

- Key Vault выводит список разрешений RBAC в ресурсе учетной записи хранения.
- Key Vault проверяет ответ через сопоставление регулярных выражений для определения действий и их отсутствия.

Некоторые поддерживаемые примеры вы можете найти в разделе [Key Vault - Managed Storage Account Keys Samples](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167) (Key Vault — управляемая система хранения ключей учетных записей).

Если удостоверение не имеет разрешение на *повторное создание* или основное удостоверение Key Vault не имеет разрешение на *перечисление* или *повторное создание*, тогда запрос на подключение завершается ошибкой с соответствующим кодом и сообщением.

Маркер OBO будет работать только при использовании основных, собственных клиентских приложений PowerShell или CLI.

## <a name="other-applications"></a>Другие приложения

- Маркеры SAS, созданные с помощью ключей учетной записи хранения Key Vault, предоставляют более точный контроль доступа к учетной записи хранения Azure. Дополнительные сведения см. в статье [Использование подписанных URL-адресов (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>См. также

- [Сведения о ключах, секретах и сертификатах](https://docs.microsoft.com/rest/api/keyvault/)
- [Официальный блог команды разработчиков Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

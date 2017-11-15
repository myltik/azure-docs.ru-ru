---
ms.assetid: 
title: "Ключи учетной записи хранения Azure Key Vault"
description: "Ключи учетной записи хранения обеспечивают простую интеграцию между Azure Key Vault и доступом по ключу к учетной записи хранения Azure."
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: a87877f4b213365442400d113a67964ef942341f
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
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
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>После появления ключей Azure Key Vault 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Руководство для разработчика

- Разрешите только Key Vault управлять ключами ASA. Не пытайтесь управлять ими самостоятельно. Это повлияет на процессы Key Vault. 
- Ключи ASA не должны управляться более чем одним объектом Key Vault. 
- Если необходимо вручную повторно создать ключи ASA, рекомендуем делать это через Key Vault. 

## <a name="getting-started"></a>Приступая к работе

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Настройка разрешений для управления доступом на основе ролей (RBAC)

Для удостоверения приложения Azure Key Vault необходимы разрешения на *вывод списка* и *повторное создание* ключей учетной записи хранения. Настройте эти разрешения, выполнив следующие действия:

- Получите ObjectId удостоверения Azure Key Vault: 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- Присвойте идентификатору Azure Key Vault роль оператора учетной записи хранения: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Для классической учетной записи задайте значение параметра роли *Роль службы оператора ключей классических учетных записей хранения*.

## <a name="working-example"></a>Действующий пример

В следующем примере показано создание учетной записи хранения Azure под управлением Key Vault и связанных определений подписанного URL-адреса (SAS).

### <a name="assumptions"></a>Предположения

Далее представлены инструкции для этого примера.

- Ресурс хранения находится в папке */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*.

- Имя хранилища ключей — *yourtest1*.

### <a name="get-a-service-principal"></a>Получение субъекта-службы

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Выходные данные предыдущей команды будут содержать параметр ServicePrincipal, который мы будем называть *yourKeyVaultServicePrincipalId*. 

### <a name="set-permissions"></a>Установка разрешений

Убедитесь, что для разрешений хранилища задано значение *Все*. Вы можете получить youruserPrincipalId и задать разрешения для хранилищ, выполнив указанные ниже команды.

```powershell
$youruserPrincipalId = (Get-AzureRmADUser -SearchString "your user principal name").Id
```
Теперь найдите свое имя и получите связанный ObjectId, который будет использоваться при настройке разрешений в хранилище.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $youruserPrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Разрешить доступ

Прежде чем вы сможете создавать управляемую учетную запись хранения и определения SAS, службе Key Vault необходимо предоставить доступ к учетными записями хранения.

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Создать учетную запись хранения

Теперь создайте управляемую учетную запись хранения и два определения SAS. SAS учетной записи позволяет получить доступ к службе BLOB-объектов с другими разрешениями.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Повторное создание

Настройте период повторного создания с помощью следующих команд.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Настройка определений SAS

Настройте определения SAS в Key Vault для управляемой учетной записи хранения.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Получение токена

Получите соответствующие токены SAS и выполните вызовы к хранилищу.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Создание хранилища

Обратите внимание, что попытка получить доступ с использованием *$sastoken1* завершается ошибкой, но можно получить доступ с помощью *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Пример сводки

Доступ к содержимому большого двоичного объекта хранилища можно получить с помощью токена SAS с доступом на запись.

### <a name="relevant-powershell-cmdlets"></a>Соответствующие командлеты PowerShell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

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

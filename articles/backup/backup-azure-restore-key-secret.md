---
title: "Восстановление ключа и секрета хранилища ключей для зашифрованных виртуальных машин с помощью службы архивации Azure | Документация Майкрософт"
description: "Узнайте, как восстановить ключ и секрет хранилища ключей в службе архивации Azure с помощью PowerShell"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы архивации Azure
В этой статье рассказывается об использовании службы архивации виртуальных машин Azure для восстановления зашифрованных виртуальных машин Azure, когда ключ и секрет отсутствуют в хранилище ключей. Эта процедура будет также полезной, если нужно хранить отдельную копию ключа (ключ шифрования ключа) и секрета (ключ шифрования BitLocker) для восстанавливаемой виртуальной машины.

## <a name="prerequisites"></a>Предварительные требования
* **Резервная копия зашифрованных виртуальных машин** — выполнять резервное копирование зашифрованных виртуальных машин Azure нужно с помощью службы архивации Azure. Резервное копирование зашифрованных виртуальных машин Azure см. в статье [Manage backup and restore of Azure VMs using PowerShell](backup-azure-vms-automation.md) (Управление резервным копированием и восстановлением виртуальных машин Azure с помощью PowerShell).
* **Настроенное хранилище ключей Azure** — убедитесь, что хранилище ключей, в которое нужно восстановить ключи и секреты, существует. Управление хранилищами ключей описано в статье [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* **Восстановление диска** — убедитесь, что запущено задание восстановления дисков для зашифрованной виртуальной машины с помощью [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Это связано с тем, что данное задание создает JSON-файл в учетной записи хранения, которая содержит ключи и секреты для восстанавливаемой зашифрованной виртуальной машины.

## <a name="get-key-and-secret-from-azure-backup"></a>Получение ключа и секрета из службы Azure Backup

> [!NOTE]
> После восстановления диска для зашифрованной виртуальной машины убедитесь, что выполняются следующие требования:
> 1. Переменная $details заполняется сведениями о задании восстановления диска, как описано в [разделе о восстановлении дисков с помощью PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).
> 2. Создавать виртуальную машину из восстановленных дисков следует только **после восстановления ключа и секрета в хранилище ключей**.
>
>

Запросите свойства восстановленного диска для получения сведений о задании.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Задайте контекст хранилища Azure и восстановите файл конфигурации JSON, который содержит ключ и секрет для зашифрованной виртуальной машины.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Ключ восстановления
Когда JSON-файл создан в упомянутом выше конечном пути, создайте из этого JSON-файла ключ в виде файла большого двоичного объекта и укажите его при выполнении командлета восстановления ключа, чтобы поместить ключ (KEK) обратно в хранилище ключей.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Восстановление секрета
Используйте созданный выше JSON-файл для получения имени и значения секрета и укажите его при выполнении командлета настройки секрета, чтобы поместить секрет (BEK) обратно в хранилище ключей. **Воспользуйтесь этими командлетами, если виртуальная машина зашифрована с помощью BEK и KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Если виртуальная машина **зашифрована только с помощью BEK**, то создайте секрет в формате файла большого двоичного объекта на основе JSON-файла и укажите его при выполнении командлета восстановления секрета, чтобы поместить секрет (BEK) обратно в хранилище ключей.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. Значение переменной $secretname можно получить из выходных данных параметра $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl, используя текст после secrets/. Например, выходной URL-адрес секрета — https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163, а имя секрета — B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.
> 2. Значение тега DiskEncryptionKeyFileName совпадает с именем секрета.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Создание виртуальной машины на основе восстановленного диска
Если ранее была создана резервная копия зашифрованной виртуальной машины с помощью службы архивации виртуальных машин Azure, то указанные выше командлеты PowerShell позволяют восстановить ключ и секрет в хранилище ключей. После их восстановления см. статью [Архивация виртуальных машин с помощью командлетов AzureRM.RecoveryServices.Backup](backup-azure-vms-automation.md#create-a-vm-from-restored-disks), в которой описано, как создать зашифрованные виртуальные машины, используя восстановленный диск, ключ и секрет.

## <a name="legacy-approach"></a>Устаревший подход
Описанный выше подход подойдет для всех точек восстановления. Однако более старый подход получения сведений о ключе и секрете из точки восстановления можно по-прежнему использовать для точек восстановления, созданных до 11 июля 2017 г. (для виртуальных машин, зашифрованных с помощью BEK и KEK). После выполнения задания восстановления диска для зашифрованной виртуальной машины с помощью [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) убедитесь, что переменная $rp заполняется допустимым значением.

### <a name="restore-key"></a>Ключ восстановления
Используйте следующие командлеты для получения из точки восстановления сведений о ключе (KEK) и укажите его при выполнении командлета восстановления ключа, чтобы поместить его обратно в хранилище ключей.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Восстановление секрета
Используйте следующие командлеты для получения из точки восстановления сведений о секрете (BEK) и укажите его при выполнении командлета настройки секрета, чтобы поместить его обратно в хранилище ключей.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. Значение переменной $secretname можно получить из выходных данных параметра $rp1.KeyAndSecretDetails.SecretUrl, используя текст после secrets/. Например, выходной URL-адрес секрета — https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163, а имя секрета — B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.
> 2. Значение тега DiskEncryptionKeyFileName совпадает с именем секрета.
> 3. Значение для DiskEncryptionKeyEncryptionKeyURL можно получить из хранилища ключей после восстановления ключей с помощью командлета [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).
>
>

## <a name="next-steps"></a>Дальнейшие действия
После восстановления ключа и секрета в хранилище ключей см. статью [Архивация виртуальных машин с помощью командлетов AzureRM.RecoveryServices.Backup](backup-azure-vms-automation.md#create-a-vm-from-restored-disks), в которой описано, как создать зашифрованные виртуальные машины, используя восстановленный диск, ключ и секрет.


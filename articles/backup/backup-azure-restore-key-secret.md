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
ms.date: 10/18/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c5b2969ddc943b2b15826003f5a9e686e84e1c4


---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы архивации Azure
В этой статье рассказывается об использовании службы архивации виртуальных машин Azure для восстановления зашифрованных виртуальных машин Azure, когда ключ и секрет отсутствуют в хранилище ключей. Эта процедура будет также полезной, если нужно хранить отдельную копию ключа (ключ шифрования ключа) и секрета (ключ шифрования BitLocker) для восстанавливаемой виртуальной машины.

## <a name="pre-requisites"></a>Предварительные требования
1. **Резервная копия зашифрованных виртуальных машин** — выполнять резервное копирование зашифрованных виртуальных машин Azure нужно с помощью службы архивации Azure. Резервное копирование зашифрованных виртуальных машин Azure см. в статье [Manage backup and restore of Azure VMs using PowerShell](backup-azure-vms-automation.md) (Управление резервным копированием и восстановлением виртуальных машин Azure с помощью PowerShell).
2. **Настроенное хранилище ключей Azure** — убедитесь, что хранилище ключей, в которое нужно восстановить ключи и секреты, существует. Управление хранилищами ключей описано в статье [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).

## <a name="setup-recovery-services-vault"></a>Настройка хранилища служб восстановления
Выполните следующие действия, чтобы войти в PowerShell и настроить контекст хранилища службы восстановления.

### <a name="log-in-to-azure-powershell"></a>Вход в Azure PowerShell
Войдите в учетную запись Azure с помощью следующего командлета.

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Настройка контекста для хранилища служб восстановления
Войдя в систему, используйте следующий командлет, чтобы получить список доступных подписок.

```
PS C:\> Get-AzureRmSubscription
```

Выберите подписку, в которой доступны ресурсы.

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Определите контекст хранилища, используя сведения о хранилище служб восстановления, в которое выполняется резервное копирование зашифрованных виртуальных машин.

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Получение точки восстановления
Выберите в хранилище контейнер, который представляет зашифрованную виртуальную машину Azure.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

С помощью этого контейнера получите резервный элемент для соответствующей виртуальной машины.

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Получите массив точек восстановления для выбранного резервного элемента в переменной rp.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Восстановление зашифрованной виртуальной машины
Выполните следующие действия для восстановления зашифрованной виртуальной машины с соответствующими ключом и секретом.

### <a name="restore-key"></a>Ключ восстановления
Упомянутый выше массив $rp сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс 0. Например, $rp[0] обозначает последнюю точку восстановления.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> После успешного выполнения этого командлета в указанной папке на локальном компьютере создается файл большого двоичного объекта. Этот файл большого двоичного объекта содержит ключ шифрования ключа в зашифрованном виде.
> 
> 

Восстановите ключ в хранилище ключей с помощью следующего командлета. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Восстановление секрета
Восстановите секретные данные из точки восстановления, полученной ранее.

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> Текст перед строкой vault.azure.net содержит исходное имя хранилища ключей. Текст после строки secrets/ содержит имя секрета. 
> 
> 

Если вы хотите и далее использовать это же имя секрета, получите имя и значение секрета из выходных данных выполненного выше командлета. В других случаях обновите переменную $secretname, как показано ниже, чтобы установить новое имя секрета. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Определите для секрета теги, если нужно одновременно восстановить виртуальную машину. Значение тега DiskEncryptionKeyFileName должно содержать имя секрета, который вы хотите использовать. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> Значение для DiskEncryptionKeyFileName совпадает с именем секрета, полученным ранее. Значение для DiskEncryptionKeyEncryptionKeyURL можно получить из хранилища ключей после восстановления ключей с помощью командлета [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).    
> 
> 

Поместите секрет обратно в хранилище ключей.

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Восстановление виртуальной машины
Указанные выше командлеты PowerShell позволяют восстановить ключ и секрет в хранилище ключей, если ранее была создана резервная копия зашифрованной виртуальной машины с помощью службы архивации виртуальных машин Azure. После их восстановления переходите к статье [Manage backup and restore of Azure VMs using PowerShell](backup-azure-vms-automation.md) (Управление резервным копированием и восстановлением виртуальных машин Azure с помощью PowerShell), в которой описано, как восстанавливать зашифрованные виртуальные машины.




<!--HONumber=Nov16_HO3-->



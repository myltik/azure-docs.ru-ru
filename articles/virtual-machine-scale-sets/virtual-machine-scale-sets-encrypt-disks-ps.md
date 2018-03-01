---
title: "Шифрование дисков в службе \"Масштабируемые наборы виртуальных машин Azure\" | Документация Майкрософт"
description: "Узнайте, как шифровать подключенные диски в масштабируемых наборах виртуальных машин."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: dddcece9f7566961b256369330661e5dbd5d4665
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Шифрование диска ОС и подключенных дисков данных в масштабируемом наборе виртуальных машин
[Масштабируемые наборы виртуальных машин](/azure/virtual-machine-scale-sets/) Azure поддерживают шифрование дисков Azure (ADE).  Шифрование дисков Azure можно включить для масштабируемых наборов виртуальных машин Windows и Linux, чтобы защитить хранящиеся в них неактивные данные с помощью стандартной отраслевой технологии шифрования. Дополнительные сведения см. в статье "Azure Disk Encryption for Windows and Linux virtual machines" (Использование шифрования дисков Azure для виртуальных машин Windows и Linux).

> [!NOTE]
>  Служба шифрования дисков Azure для масштабируемых наборов виртуальных машин в настоящее время находится на этапе общедоступной предварительной версии, которая доступна во всех общедоступных регионах Azure.

Шифрование дисков Azure поддерживается:
- для масштабируемых наборов, созданных с помощью управляемых дисков, и не поддерживается для масштабируемых наборов на основе собственных (или неуправляемых) дисков;
- для дисков ОС и томов данных в масштабируемых наборах Windows; отключение шифрования поддерживается для томов операционной системы и данных в масштабируемых наборах Windows;
- для томов данных в масштабируемых наборах Linux. Шифрование дисков ОС НЕ поддерживается в текущей предварительной версии для масштабируемых наборов Linux.

В текущей предварительной версии не поддерживаются операции пересоздания образа и обновления виртуальных машин в масштабируемом наборе. Использование предварительной версии службы шифрования дисков Azure для масштабируемых наборов виртуальных машин рекомендуется только в тестовой среде. Не следует с помощью предварительной версии включать шифрование дисков в рабочих средах, в которых может потребоваться обновить образ операционной системы в зашифрованном масштабируемом наборе.

## <a name="prerequisites"></a>предварительным требованиям
Установите последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases), которая содержит команды шифрования.

Для работы предварительной версии службы шифрования дисков Azure для масштабируемых наборов виртуальных машин необходимо самостоятельно зарегистрировать подписку с помощью следующих команд PowerShell. 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Подождите около 10 минут, пока приведенная ниже команда не вернет состояние "Registered" (Зарегистрирована). 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Создание хранилища ключей Azure с поддержкой шифрования дисков
Создайте новое хранилище ключей в тех же подписке и регионе, что и масштабируемый набор, и настройте политику доступа EnabledForDiskEncryption.

```powershell
$rgname="windatadiskencryptiontest"
$VaultName="encryptionvault321"

New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $rgName -Location southcentralus -EnabledForDiskEncryption
``` 

Или включите поддержку шифрования дисков в существующем хранилище ключей, размещенном в тех же подписке и регионе, что и масштабируемый набор.

```powershell
$VaultName="encryptionvault321"
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -EnabledForDiskEncryption
```

## <a name="enable-encryption"></a>Включение шифрования
Приведенные ниже команды шифруют диск данных в работающем масштабируемом наборе с помощью хранилища ключей, размещенного в той же группе ресурсов. Кром того, для шифрования дисков в работающем [масштабируемом наборе Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) или [масштабируемом наборе Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) можно использовать шаблоны.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
$DiskEncryptionKeyVaultUrl="https://encryptionvault321.vault.azure.net"
$KeyVaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/windatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvault321"

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName `
    -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId –VolumeType Data
```

## <a name="check-encryption-progress"></a>Проверка хода выполнения шифрования
Используйте следующие команды для отображения состояния шифрования масштабируемого набора.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName -InstanceId "4"
```

## <a name="disable-encryption"></a>Отключение шифрования
Отключите шифрование в работающем масштабируемом наборе виртуальных машин с помощью следующих команд. Кром того, для отключения шифрования в работающем [масштабируемом наборе Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) или [масштабируемом наборе Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) можно использовать шаблоны.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName
```

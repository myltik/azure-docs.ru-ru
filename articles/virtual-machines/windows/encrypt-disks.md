---
title: Шифрование дисков на виртуальной машине Windows в Azure | Документы Майкрософт
description: Как шифровать диски на виртуальной машине Windows для улучшения уровня безопасности с помощью Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: 442ff942150af8a8dec89164fbc017a9e6f360e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Как шифровать виртуальные диски на виртуальной машине Windows
Для улучшения уровня безопасности и соответствия требованиям виртуальной машины содержание виртуальных дисков в Azure можно зашифровать. Диски можно зашифровать с использованием криптографических ключей, защищенных в хранилище ключей Azure. Вы будете управлять этими криптографическими ключами и проводить аудит их использования. В этой статье описывается шифрование дисков на виртуальной машине Windows с помощью Azure PowerShell. Вы также можете [зашифровать виртуальную машину Linux с помощью Azure CLI 2.0](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Общие сведения о шифровании дисков
Виртуальные диски на виртуальных машинах Windows шифруются в неактивном состоянии с помощью Bitlocker. В Azure за шифрование виртуальных дисков плата не взимается. Криптографические ключи хранятся в хранилище ключей Azure с применением защиты программного обеспечения. В качестве альтернативы можно импортировать или создать ключи аппаратных модулей безопасности (HSM), сертифицированных по стандартам уровня 2 FIPS 140-2. Эти криптографические ключи используются для шифрования и расшифровки виртуальных дисков, подключенных к виртуальной машине. Вы сохраняете контроль над этими криптографическими ключами и можете проводить аудит их использования. Субъект-служба Azure Active Directory предоставляет безопасный механизм для выдачи этих криптографических ключей при включении и отключении виртуальных машин.

Процесс шифрования виртуальной машины выполняется следующим образом.

1. Создайте криптографический ключ в хранилище ключей Azure.
2. Настройте криптографический ключ таким образом, чтобы его можно было использовать для шифрования дисков.
3. Чтобы прочитать криптографический ключ из Azure Key Vault, создайте субъект-службу Azure Active Directory с соответствующими разрешениями.
4. Выполните команду для шифрования виртуальных дисков, указав субъект-службу Azure Active Directory и соответствующий ключ шифрования, который необходимо использовать.
5. Субъект-служба Azure Active Directory запрашивает требуемый криптографический ключ из Azure Key Vault.
6. Виртуальные диски зашифровываются с использованием предоставленного криптографического ключа.

## <a name="encryption-process"></a>Процесс шифрования
Шифрование дисков зависит от следующих дополнительных компонентов.

* **Хранилище ключей Azure.** Используется для защиты криптографических ключей и секретов, используемых для шифрования или расшифровки дисков. 
  * При наличии можно воспользоваться имеющимся хранилищем ключей Azure. Для шифрования дисков не нужно выделять хранилище ключей.
  * Чтобы разделить административные границы и обеспечить видимость ключа, можно создать выделенное хранилище ключей.
* **Azure Active Directory.** Осуществляет безопасный обмен необходимыми криптографическими ключами и проверку подлинности для запрошенных действий. 
  * Как правило, для размещения приложения можно использовать имеющийся экземпляр Azure Active Directory.
  * Субъект-служба обеспечивает безопасный механизм для выполнения запроса и выдачи соответствующих криптографических ключей. При этом вы не разрабатываете фактическое приложение, которое интегрируется с Azure Active Directory.

## <a name="requirements-and-limitations"></a>Требования и ограничения
Поддерживаемые сценарии и требования для шифрования дисков:

* включение шифрования для новых виртуальных машин Windows из образов Azure Marketplace или пользовательского образа виртуального жесткого диска;
* включение шифрования для имеющихся виртуальных машин в Azure;
* включение шифрования для виртуальных машин Windows, использующих дисковые пространства;
* отключение шифрования дисков данных и дисков операционной системы виртуальных машин Windows;
* все ресурсы (такие как хранилище ключей, учетная запись хранения и виртуальная машина) должны относиться к одному региону и одной подписке Azure;
* виртуальные машины уровня "Стандартный", такие как серии A, D, DS, G и GS.

Шифрование дисков сейчас не поддерживается в следующих сценариях:

* виртуальные машины уровня "Базовый";
* виртуальные машины, созданные с использованием классической модели развертывания;
* обновление криптографических ключей на уже зашифрованных виртуальных машинах;
* интеграция с локальной службой управления ключами.

## <a name="create-azure-key-vault-and-keys"></a>Создание Azure Key Vault и ключей
Перед началом работы убедитесь, что у вас установлена последняя версия модуля Azure PowerShell. Подробнее: [Установка и настройка Azure PowerShell](/powershell/azure/overview). В примерах команд замените все примеры параметров собственными именами, расположением и значениями ключей. В следующих примерах используется соглашение *myResourceGroup*, *myKeyVault*, *myVM* и т. д.

Первым делом создайте хранилище ключей Azure для хранения криптографических ключей. В хранилище ключей Azure можно хранить ключи, секреты или пароли, что позволяет безопасно реализовать их в приложениях и службах. Для шифрования виртуальных дисков создайте Key Vault, чтобы хранить криптографический ключ, используемый для шифрования или расшифровки виртуальных дисков. 

Включите поставщик Azure Key Vault в подписке Azure, выполнив команду [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), и создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается имя группы ресурсов *myResourceGroup* в расположении *East US*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Хранилище ключей Azure, содержащее криптографические ключи и связанные вычислительные ресурсы, такие как хранилище и виртуальная машина, должны находиться в одном и том же регионе. Создайте Azure Key Vault, выполнив команду [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault), и включите Key Vault для использования при шифровании дисков. Укажите уникальное имя Key Vault для параметра *keyVaultName*, выполнив следующую команду:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Хранить криптографические ключи можно с помощью программного обеспечения или защиты HSM. Для использования HSM требуется хранилище ключей уровня "Премиум". Создание хранилища ключей уровня "Премиум" осуществляется за дополнительную плату в отличие от хранилища ключей уровня "Стандартный", в котором хранятся ключи, защищенные программным обеспечением. Чтобы создать Key Vault уровня "Премиум", на предыдущем шаге добавьте параметры *-Sku "Premium"* к команде. В следующем примере используются ключи, защищенные программным обеспечением, так как мы создали хранилище ключей уровня "Стандартный". 

Для обеих моделей защиты платформе Azure необходимо предоставить доступ на запрос криптографических ключей при загрузке виртуальной машины для расшифровки виртуальных дисков. Создайте криптографический ключ в своем Key Vault, выполнив команду [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). В следующем примере создается ключ с именем *myKey*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Создание субъекта-службы Azure Active Directory
При шифровании или расшифровке виртуальных дисков укажите учетную запись, чтобы выполнять проверку подлинности и обмен криптографическими ключами из Key Vault. Эта учетная запись, субъект-служба Azure Active Directory, позволяет платформе Azure запрашивать соответствующие криптографические ключи от имени виртуальной машины. Экземпляр Azure Active Directory по умолчанию доступен в вашей подписке, хотя во многих организациях есть выделенные каталоги Azure Active Directory.

Создайте субъект-службу в Azure Active Directory, выполнив команду [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Чтобы указать надежный пароль, выполните указания из статьи [Политики и ограничения для паролей в Azure Active Directory](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Для успешного шифрования или расшифровки виртуальных дисков необходимо настроить разрешения на криптографические ключи, которые хранятся в Key Vault, так чтобы субъект-служба Azure Active Directory могла читать их. Задайте разрешение для Key Vault, выполнив команду [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Создание виртуальной машины
Чтобы проверить процесс шифрования, создайте виртуальную машину с помощью команды [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина *myVM* с использованием образа *Windows Server 2016 Datacenter*. Когда появится запрос на ввод учетных данных, введите имя пользователя и пароль, которые будут использоваться для виртуальной машины.

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Шифрование виртуальной машины
Чтобы шифровать виртуальные диски, объедините предыдущие компоненты.

1. Укажите субъект-службу и пароль Azure Active Directory.
2. Укажите хранилище ключей для хранения метаданных зашифрованных дисков.
3. Укажите криптографические ключи для фактического шифрования и расшифровки.
4. Укажите, что следует шифровать: диск ОС, диски данных или и то, и другое.

Зашифруйте виртуальную машину, выполнив команду [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) и используя ключ Azure Key Vault и учетные данные субъекта-службы Azure Active Directory. В следующем примере извлекаются все сведения о ключе, а затем шифруется виртуальная машина *myVM*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Примите приглашение, чтобы продолжить шифрование виртуальной машины. Во время этого процесса виртуальная машина перезагружается. После завершения процесса шифрования и перезагрузки виртуальной машины просмотрите состояние шифрования, выполнив команду [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об управлении Azure Key Vault см. в разделе [Настройка Key Vault для виртуальных машин](key-vault-setup.md).
* Дополнительные сведения о шифровании дисков, а именно о подготовке зашифрованной настраиваемой виртуальной машины к передаче в Azure, см. в статье [Дисковое шифрование Azure для виртуальных машин IaaS под управлением Windows и Linux](../../security/azure-security-disk-encryption.md).

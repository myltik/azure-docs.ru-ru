---
title: "Шифрование дисков на виртуальной машине Linux в Azure | Документация Майкрософт"
description: "Как шифровать диски на виртуальной машине Linux для улучшения уровня безопасности с помощью Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 814f6c9df0dea145e27a2bf5cc43649bc88e070a
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Как шифровать виртуальные диски на виртуальной машине Linux
Для улучшения уровня безопасности и соответствия требованиям виртуальной машины содержание виртуальных дисков в Azure можно зашифровать. Диски можно зашифровать с использованием криптографических ключей, защищенных в хранилище ключей Azure. Вы будете управлять этими криптографическими ключами и проводить аудит их использования. В этой статье описывается шифрование дисков на виртуальной машине Linux с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для шифрования виртуальных дисков на вашей виртуальной машине. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#overview-of-disk-encryption).

Вам нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure, выполнив команду [az login](/cli/azure/#login). В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `myKey` и `myVM`.

Сначала включите поставщик Azure Key Vault в подписке Azure, выполнив команду [az provider register](/cli/azure/provider#register), и создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается имя группы ресурсов `myResourceGroup` в расположении `WestUS`.

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location WestUS
```

Создайте Azure Key Vault, выполнив команду [az keyvault create](/cli/azure/keyvault#create), и включите Key Vault для использования при шифровании дисков. Укажите уникальное имя Key Vault для параметра `keyvault_name`, выполнив следующую команду:

```azurecli
keyvault_name=myUniqueKeyVaultName
az keyvault create --name $keyvault_name --resource-group myResourceGroup \
  --location WestUS --enabled-for-disk-encryption True
```

Создайте криптографический ключ в своем Key Vault, выполнив команду [az keyvault key create](/cli/azure/keyvault/key#create). В следующем примере создается ключ с именем `myKey`.

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

Создайте субъект-службу с помощью Azure Active Directory, выполнив команду [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac). Субъект-служба осуществляет проверку подлинности и обмен криптографическими ключами из Key Vault. В следующем примере считываются значения для идентификатора и пароля субъекта-службы, которые будут использоваться в последующих командах:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Пароль отображается только при создании субъекта-службы. При необходимости можно просмотреть и записать пароль (`echo $sp_password`). Вы можете вывести список субъектов-служб, выполнив команду [az ad sp list](/cli/azure/ad/sp#list), и просмотреть дополнительные сведения о конкретном субъекте-службе, выполнив [az ad sp show](/cli/azure/ad/sp#show).

Задайте разрешение для Key Vault, выполнив команду [az keyvault set-policy](/cli/azure/keyvault#set-policy). В следующем примере используется идентификатор субъекта-службы из предыдущей команды:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions all \
  --secret-permissions all
```

Создайте виртуальною машину, выполнив команду [az vm create](/cli/azure/vm#create), и подключите диск данных емкостью 5 ГБ. Только некоторые образы Marketplace поддерживают шифрование диска. В следующем примере создается виртуальная машина `myVM` с использованием образа **CentOS 7.2n**:

```azurecli
az vm create -g myResourceGroup -n myVM --image OpenLogic:CentOS:7.2n:7.2.20160629 \
  --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
  --data-disk-sizes-gb 5
```

Установите SSH-подключение к виртуальной машине. Создайте раздел и файловую систему, а затем подключите диск данных. Дополнительные сведения см. в разделе [Подключение к виртуальной машине Linux для подключения нового диска](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Закройте сеанс SSH.

Зашифруйте виртуальную машину, выполнив команду [az vm encryption enable](/cli/azure/vm/encryption#enable). В следующем примере используются переменные `$sp_id` и `$sp_password` из предыдущей команды `ad sp create-for-rbac`:

```azurecli
az vm encryption enable --resource-group myResourceGroup --name myVM \
  --aad-client-id $sp_id \
  --aad-client-secret $sp_password \
  --disk-encryption-keyvault $keyvault_name \
  --key-encryption-key myKey \
  --volume-type all
```

Для завершения шифрования диска потребуется некоторое время. Состояние процесса можно контролировать с помощью команды [az vm encryption show](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Отображается состояние **EncryptionInProgress**. Подождите, пока состояние диска операционной системы изменится на **VMRestartPending**, а затем перезапустите виртуальную машину, выполнив команду [az vm restart](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Процесс шифрования диска завершается во время загрузки, поэтому подождите 5 минут, прежде чем повторно проверять состояние шифрования, и выполните команду **az vm encryption show**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

 Теперь состояние диска операционной системы и диска данных должно быть **Encrypted**.

## <a name="overview-of-disk-encryption"></a>Общие сведения о шифровании дисков
Виртуальные диски на виртуальных машинах Linux шифруются с помощью [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). В Azure за шифрование виртуальных дисков плата не взимается. Криптографические ключи хранятся в хранилище ключей Azure с применением защиты программного обеспечения. В качестве альтернативы можно импортировать или создать ключи аппаратных модулей безопасности (HSM), сертифицированных по стандартам уровня 2 FIPS 140-2. Вы сохраняете контроль над этими криптографическими ключами и можете проводить аудит их использования. Эти криптографические ключи используются для шифрования и расшифровки виртуальных дисков, подключенных к виртуальной машине. Субъект-служба Azure Active Directory предоставляет безопасный механизм для выдачи этих криптографических ключей при включении и отключении виртуальных машин.

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

* SKU серверов Linux — Ubuntu, CentOS, SUSE, SUSE Linux Enterprise Server (SLES) и Red Hat Enterprise Linux;
* все ресурсы (такие как хранилище ключей, учетная запись хранения и виртуальная машина) должны относиться к одному региону и одной подписке Azure;
* стандартные серии A, D, DS, G и GS виртуальных машин.

Шифрование дисков сейчас не поддерживается в следующих сценариях:

* виртуальные машины уровня "Базовый";
* виртуальные машины, созданные с использованием классической модели развертывания;
* отключение шифрования дисков ОС на виртуальных машинах Linux;
* обновление криптографических ключей на уже зашифрованных виртуальных машинах Linux.

## <a name="create-azure-key-vault-and-keys"></a>Создание Azure Key Vault и ключей
Вам нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure, выполнив команду [az login](/cli/azure/#login). В примерах команд замените все примеры параметров собственными именами, расположением и значениями ключей. В следующих примерах используется соглашение `myResourceGroup`, `myKeyVault`, `myAADApp` и т. д.

В примерах команд замените все примеры параметров собственными именами, расположением и значениями ключей. В следующих примерах используется соглашение `myResourceGroup`, `myKey`, `myVM` и т. д.

Первым делом создайте хранилище ключей Azure для хранения криптографических ключей. В хранилище ключей Azure можно хранить ключи, секреты или пароли, что позволяет безопасно реализовать их в приложениях и службах. Для шифрования виртуальных дисков используйте хранилище ключей, чтобы хранить криптографический ключ, используемый для шифрования или расшифровки виртуальных дисков. 

Включите поставщик Azure Key Vault в подписке Azure, выполнив команду [az provider register](/cli/azure/provider#register), и создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается имя группы ресурсов `myResourceGroup` в расположении `WestUS`.

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location WestUS
```

Хранилище ключей Azure, содержащее криптографические ключи и связанные вычислительные ресурсы, такие как хранилище и виртуальная машина, должны находиться в одном и том же регионе. Создайте Azure Key Vault, выполнив команду [az keyvault create](/cli/azure/keyvault#create), и включите Key Vault для использования при шифровании дисков. Укажите уникальное имя Key Vault для параметра `keyvault_name`, выполнив следующую команду:

```azurecli
keyvault_name=myUniqueKeyVaultName
az keyvault create --name $keyvault_name --resource-group myResourceGroup \
  --location WestUS --enabled-for-disk-encryption True
```

Хранить криптографические ключи можно с помощью программного обеспечения или защиты HSM. Для использования HSM требуется хранилище ключей уровня "Премиум". Создание хранилища ключей уровня "Премиум" осуществляется за дополнительную плату в отличие от хранилища ключей уровня "Стандартный", в котором хранятся ключи, защищенные программным обеспечением. Чтобы создать хранилище ключей уровня "Премиум", на предыдущем шаге добавьте `--sku Premium` к команде. В следующем примере используются ключи, защищенные программным обеспечением, так как мы создали хранилище ключей уровня "Стандартный". 

Для обеих моделей защиты платформе Azure необходимо предоставить доступ на запрос криптографических ключей при загрузке виртуальной машины для расшифровки виртуальных дисков. Создайте криптографический ключ в своем Key Vault, выполнив команду [az keyvault key create](/cli/azure/keyvault/key#create). В следующем примере создается ключ с именем `myKey`.

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Создание субъекта-службы Azure Active Directory
При шифровании или расшифровке виртуальных дисков укажите учетную запись, чтобы выполнять проверку подлинности и обмен криптографическими ключами из Key Vault. Эта учетная запись, субъект-служба Azure Active Directory, позволяет платформе Azure запрашивать соответствующие криптографические ключи от имени виртуальной машины. Экземпляр Azure Active Directory по умолчанию доступен в вашей подписке, хотя во многих организациях есть выделенные каталоги Azure Active Directory.

Создайте субъект-службу с помощью Azure Active Directory, выполнив команду [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac). В следующем примере считываются значения для идентификатора и пароля субъекта-службы, которые будут использоваться в последующих командах:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Пароль отображается только при создании субъекта-службы. При необходимости можно просмотреть и записать пароль (`echo $sp_password`). Вы можете вывести список субъектов-служб, выполнив команду [az ad sp list](/cli/azure/ad/sp#list), и просмотреть дополнительные сведения о конкретном субъекте-службе, выполнив [az ad sp show](/cli/azure/ad/sp#show).

Для успешного шифрования или расшифровки виртуальных дисков необходимо настроить разрешения на криптографические ключи, которые хранятся в Key Vault, так чтобы субъект-служба Azure Active Directory могла читать их. Задайте разрешение для Key Vault, выполнив команду [az keyvault set-policy](/cli/azure/keyvault#set-policy). В следующем примере используется идентификатор субъекта-службы из предыдущей команды:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions all \
  --secret-permissions all
```


## <a name="create-virtual-machine"></a>Создание виртуальной машины
Чтобы зашифровать некоторые виртуальные диски, создайте виртуальную машину и добавьте диск данных. Создайте виртуальною машину, которую необходимо зашифровать, выполнив команду [az vm create](/cli/azure/vm#create), и подключите диск данных емкостью 5 ГБ. Только некоторые образы Marketplace поддерживают шифрование диска. В следующем примере создается виртуальная машина `myVM` с использованием образа **CentOS 7.2n**:

```azurecli
az vm create -g myResourceGroup -n myVM --image OpenLogic:CentOS:7.2n:7.2.20160629 \
  --data-disk-sizes-gb 5
```

Подключитесь к своей виртуальной машине по протоколу SSH, чтобы создать раздел и файловую систему, и подключите диск данных. Дополнительные сведения см. в разделе [Подключение к виртуальной машине Linux для подключения нового диска](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Закройте сеанс SSH.


## <a name="encrypt-virtual-machine"></a>Шифрование виртуальной машины
Чтобы шифровать виртуальные диски, объедините предыдущие компоненты.

1. Укажите субъект-службу и пароль Azure Active Directory.
2. Укажите хранилище ключей для хранения метаданных зашифрованных дисков.
3. Укажите криптографические ключи для фактического шифрования и расшифровки.
4. Укажите, что следует шифровать: диск ОС, диски данных или и то, и другое.

Зашифруйте виртуальную машину, выполнив команду [az vm encryption enable](/cli/azure/vm/encryption#enable). В следующем примере используются переменные `$sp_id` и `$sp_password` из предыдущей команды `ad sp create-for-rbac`:

```azurecli
az vm encryption enable --resource-group myResourceGroup --name myVM \
  --aad-client-id $sp_id \
  --aad-client-secret $sp_password \
  --disk-encryption-keyvault $keyvault_name \
  --key-encryption-key myKey \
  --volume-type all
```

Для завершения шифрования диска потребуется некоторое время. Состояние процесса можно контролировать с помощью команды [az vm encryption show](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Вы должны увидеть результат, аналогичный сокращенному примеру ниже.

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

Подождите, пока состояние диска операционной системы изменится на **VMRestartPending**, а затем перезапустите виртуальную машину, выполнив команду [az vm restart](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Процесс шифрования диска завершается во время загрузки, поэтому подождите 5 минут, прежде чем повторно проверять состояние шифрования, и выполните команду **az vm encryption show**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Теперь состояние диска операционной системы и диска данных должно быть **Encrypted**.


## <a name="add-additional-data-disks"></a>Добавление дополнительных дисков данных
После того как вы зашифруете диски данных, позже вы сможете добавить дополнительные виртуальные диски в виртуальную машину, а также зашифровать их. При выполнении команды `az vm encryption enable` увеличьте версию последовательности с помощью параметра `--sequence-version`. Этот параметр версии последовательности позволяет выполнять последовательные операции на одной и той же виртуальной машине.

Например, добавим второй виртуальный диск к виртуальной машине следующим образом:

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

Повторно выполните команду для шифрования виртуальных дисков, в этот раз добавив параметр `--sequence-version` и увеличив значение, применяемое при первом выполнении, следующим образом:

```azurecli
az vm encryption enable --resource-group myResourceGroup --name myVM \
  --aad-client-id $sp_id \
  --aad-client-secret $sp_password \
  --disk-encryption-keyvault $keyvault_name \
  --key-encryption-key myKey \
  --volume-type all \
  --sequence-version 2
```


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об управлении хранилищем ключей Azure, а также об удалении криптографических ключей и хранилищ см. в статье [Управление хранилищем ключей с помощью интерфейса командной строки](../../key-vault/key-vault-manage-with-cli.md).
* Дополнительные сведения о шифровании дисков, а именно о подготовке зашифрованной настраиваемой виртуальной машины к передаче в Azure, см. в статье [Дисковое шифрование Azure для виртуальных машин IaaS под управлением Windows и Linux](../../security/azure-security-disk-encryption.md).



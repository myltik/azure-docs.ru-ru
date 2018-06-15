---
title: Шифрование дисков виртуальной машины Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Сведения о шифровании дисков в виртуальной машине Linux с использованием Azure CLI 1.0 и модели развертывания Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: d1704ba37b1d2acc595cb0c354b22bfcf1c57036
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911693"
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Шифрование дисков на виртуальной машине Linux с помощью Azure CLI 1.0
Для улучшения уровня безопасности и соответствия требованиям виртуальной машины содержание виртуальных дисков в Azure можно зашифровать при хранении. Диски можно зашифровать с использованием криптографических ключей, защищенных в хранилище ключей Azure. Вы будете управлять этими криптографическими ключами и проводить аудит их использования. В этой статье подробно описывается шифрование дисков на виртуальной машине Linux с использованием Azure CLI 1.0 и модели развертывания Resource Manager.

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для шифрования виртуальных дисков на вашей виртуальной машине. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#overview-of-disk-encryption).

Кроме того, потребуется установить [последнюю версию Azure CLI 1.0](../../xplat-cli-install.md) и выполнить вход в систему в режиме Resource Manager, как показано ниже.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `myKeyVault` и `myVM`.

Сначала включите поставщик хранилища ключей Azure в подписке Azure и создайте группу ресурсов. В следующем примере создается имя группы ресурсов `myResourceGroup` в расположении `WestUS`.

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Создайте хранилище ключей Azure. В следующем примере создается хранилище ключей с именем `myKeyVault`.

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Создайте криптографический ключ в своем хранилище ключей и включите его для шифрования дисков. В следующем примере создается ключ с именем `myKey`.

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Создайте конечную точку с помощью Azure Active Directory для выполнения проверки подлинности и обмена криптографическими ключами из хранилища ключей. `--home-page` и `--identifier-uris` не обязательно должны быть фактическими маршрутизируемыми адресами. Для обеспечения высшего уровня безопасности вместо паролей следует использовать секреты клиента. Сейчас интерфейс командной строки Azure не создает секреты клиента. Секреты клиента могут создаваться только на портале Azure. В следующем примере создается конечная точка Azure Active Directory с именем `myAADApp` и используется пароль `myPassword`. Укажите собственный пароль следующим образом:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Запишите `applicationId`, который содержится в выходных данных предыдущей команды. Этот идентификатор приложения используется при выполнении следующих действий:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Добавьте диск данных в имеющуюся виртуальную машину. В следующем примере показано, как добавить диск данных в виртуальную машину с именем `myVM`.

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Просмотрите сведения о своем хранилище ключей и созданном ключе. На последнем шаге вам понадобится идентификатор хранилища ключей, универсальный код ресурса (URI) и URL-адрес ключа. В следующем примере просматриваются данные о хранилище ключей с именем `myKeyVault` и ключе с именем `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Зашифруйте диски следующим образом, введя собственные имена параметров в следующих расположениях:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Интерфейс командной строки Azure не предоставляет подробные сообщения об ошибках в процессе шифрования. Дополнительные сведения об устранении неполадок см. в файле `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Так как в приведенной выше команде много переменных и могут отсутствовать сведения о том, почему процесс завершается ошибкой, пример всей команды будет выглядеть следующим образом:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Наконец, просмотрите состояние шифрования, чтобы убедиться, что виртуальные диски зашифрованы. В следующем примере проверяется состояние виртуальной машины с именем `myVM` в группе ресурсов `myResourceGroup`:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Общие сведения о шифровании дисков
Виртуальные диски на виртуальных машинах Linux шифруются с помощью [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). В Azure за шифрование виртуальных дисков плата не взимается. Криптографические ключи хранятся в хранилище ключей Azure с применением защиты программного обеспечения. В качестве альтернативы можно импортировать или создать ключи аппаратных модулей безопасности (HSM), сертифицированных по стандартам уровня 2 FIPS 140-2. Вы сохраняете контроль над этими криптографическими ключами и можете проводить аудит их использования. Эти криптографические ключи используются для шифрования и расшифровки виртуальных дисков, подключенных к виртуальной машине. Конечная точка Azure Active Directory предоставляет безопасный механизм для выдачи этих криптографических ключей при включении и отключении виртуальных машин.

Процесс шифрования виртуальной машины выполняется следующим образом.

1. Создайте криптографический ключ в хранилище ключей Azure.
2. Настройте криптографический ключ таким образом, чтобы его можно было использовать для шифрования дисков.
3. Чтобы прочитать криптографический ключ из хранилища ключей Azure, создайте конечную точку, используя Azure Active Directory с соответствующими разрешениями.
4. Выполните команду для шифрования виртуальных дисков, указав конечную точку Azure Active Directory и соответствующий ключ шифрования, который необходимо использовать.
5. Конечная точка Azure Active Directory запрашивает требуемый криптографический ключ из хранилища ключей Azure.
6. Виртуальные диски зашифровываются с использованием предоставленного криптографического ключа.

## <a name="supporting-services-and-encryption-process"></a>Вспомогательные службы и процесс шифрования
Шифрование дисков зависит от следующих дополнительных компонентов.

* **Хранилище ключей Azure.** Используется для защиты криптографических ключей и секретов, используемых для шифрования или расшифровки дисков.
  * При наличии можно воспользоваться имеющимся хранилищем ключей Azure. Для шифрования дисков не нужно выделять хранилище ключей.
  * Чтобы разделить административные границы и обеспечить видимость ключа, можно создать выделенное хранилище ключей.
* **Azure Active Directory.** Осуществляет безопасный обмен необходимыми криптографическими ключами и проверку подлинности для запрошенных действий.
  * Как правило, для размещения приложения можно использовать имеющийся экземпляр Azure Active Directory.
  * Службы хранилища ключей и виртуальных машин запрашивают и получают соответствующие криптографические ключи с помощью приложения, выступающего в качестве конечной точки. При этом вы не разрабатываете фактическое приложение, которое интегрируется с Azure Active Directory.

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

## <a name="create-the-azure-key-vault-and-keys"></a>Создание хранилища ключей Azure и ключей
Для выполнения указаний из оставшейся части этого руководства потребуется установить [последнюю версию Azure CLI 1.0](../../xplat-cli-install.md) и выполнить вход в систему в режиме Resource Manager, как показано ниже.

```azurecli
azure config mode arm
```

В примерах команд замените все примеры параметров собственными именами, расположением и значениями ключей. В следующих примерах используется соглашение `myResourceGroup`, `myKeyVault`, `myAADApp` и т. д.

Первым делом создайте хранилище ключей Azure для хранения криптографических ключей. В хранилище ключей Azure можно хранить ключи, секреты или пароли, что позволяет безопасно реализовать их в приложениях и службах. Для шифрования виртуальных дисков используйте хранилище ключей, чтобы хранить криптографический ключ, используемый для шифрования или расшифровки виртуальных дисков.

Включите поставщик хранилища ключей Azure в подписке Azure и создайте группу ресурсов. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUS`:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Хранилище ключей Azure, содержащее криптографические ключи и связанные вычислительные ресурсы, такие как хранилище и виртуальная машина, должны находиться в одном и том же регионе. В следующем примере создается хранилище ключей Azure с именем `myKeyVault`.

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Хранить криптографические ключи можно с помощью программного обеспечения или защиты HSM. Для использования HSM требуется хранилище ключей уровня "Премиум". Создание хранилища ключей уровня "Премиум" осуществляется за дополнительную плату в отличие от хранилища ключей уровня "Стандартный", в котором хранятся ключи, защищенные программным обеспечением. Чтобы создать хранилище ключей уровня "Премиум", на предыдущем шаге добавьте `--sku Premium` к команде. В следующем примере используются ключи, защищенные программным обеспечением, так как мы создали хранилище ключей уровня "Стандартный".

Для обеих моделей защиты платформе Azure необходимо предоставить доступ на запрос криптографических ключей при загрузке виртуальной машины для расшифровки виртуальных дисков. Создайте ключ шифрования в своем хранилище ключей, а затем включите его для шифрования виртуальных дисков. В следующем примере создается ключ с именем `myKey`, а затем он включается для шифрования дисков.

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Создание приложения Azure Active Directory
При шифровании или расшифровке виртуальных дисков проверка подлинности и обмен криптографическими ключами из хранилища ключей осуществляется с помощью конечной точки. Эта конечная точка, приложение Azure Active Directory, позволяет платформе Azure запрашивать соответствующие криптографические ключи от имени виртуальной машины. Экземпляр Azure Active Directory по умолчанию доступен в вашей подписке, хотя во многих организациях есть выделенные каталоги Azure Active Directory.

Так как вы не создаете полноценное приложение Azure Active Directory, параметры `--home-page` и `--identifier-uris` в следующем примере не обязательно должны быть фактическими маршрутизируемыми адресами. Кроме того, в следующем примере также вместо создания ключей на портале Azure указывается секрет на основе пароля. Сейчас создавать ключи из командной строки Azure невозможно.

Создайте приложение Azure Active Directory. В следующем примере создается приложение с именем `myAADApp` и используется пароль `myPassword`. Укажите собственный пароль следующим образом:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Запишите `applicationId`, который возвращается в выходных данных из предыдущей команды. Этот идентификатор приложения используется в некоторых из оставшихся шагов. Далее создайте имя субъекта-службы, чтобы приложение было доступным в вашей среде. Для успешного шифрования или расшифровки виртуальных дисков необходимо настроить разрешения на криптографические ключи, которые хранятся в хранилище ключей, так, чтобы приложение Azure Active Directory могло читать их.

Создайте имя субъекта-службы и настройте соответствующие разрешения следующим образом:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Добавление виртуального диска и просмотр состояния шифрования
Чтобы зашифровать некоторые виртуальные диски, добавьте диск в имеющуюся виртуальную машину. Добавьте диск данных емкостью 5 ГБ в имеющуюся виртуальную машину следующим образом:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Виртуальные диски сейчас не зашифрованы. Просмотрите текущее состояние шифрования виртуальной машины следующим образом:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Шифрование виртуальных дисков
Чтобы шифровать виртуальные диски, объедините предыдущие компоненты.

1. Укажите приложение и пароль Azure Active Directory.
2. Укажите хранилище ключей для хранения метаданных зашифрованных дисков.
3. Укажите криптографические ключи для фактического шифрования и расшифровки.
4. Укажите, что следует шифровать: диск ОС, диски данных или и то, и другое.

Просмотрите сведения о хранилище ключей Azure и созданном ключе, так как вам понадобится идентификатор ключа хранилища, универсальный код ресурса (URI) и URL-адрес ключа на последнем шаге.

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Зашифруйте виртуальные диски с использованием выходных данных из команд `azure keyvault show` и `azure keyvault key show` следующим образом:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Так как в приведенной выше команде много переменных, в следующем примере приведена вся команда для справки:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Интерфейс командной строки Azure не предоставляет подробные сообщения об ошибках в процессе шифрования. Дополнительные сведения об устранении неполадок см. в файле `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` на виртуальной машине, на которой вы включаете шифрование.

Наконец, просмотрите состояние шифрования снова, чтобы убедиться, что виртуальные диски зашифрованы.

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Добавление дополнительных дисков данных
После того как вы зашифруете диски данных, позже вы сможете добавить дополнительные виртуальные диски в виртуальную машину, а также зашифровать их. При выполнении команды `azure vm enable-disk-encryption` увеличьте версию последовательности с помощью параметра `--sequence-version`. Этот параметр версии последовательности позволяет выполнять последовательные операции на одной и той же виртуальной машине.

Например, добавим второй виртуальный диск к виртуальной машине следующим образом:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Повторно выполните команду для шифрования виртуальных дисков, в этот раз добавив параметр `--sequence-version` и увеличив значение, применяемое при первом выполнении, следующим образом:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об управлении хранилищем ключей Azure, а также об удалении криптографических ключей и хранилищ см. в статье [Управление хранилищем ключей с помощью интерфейса командной строки](../../key-vault/key-vault-manage-with-cli2.md).
* Дополнительные сведения о шифровании дисков, а именно о подготовке зашифрованной настраиваемой виртуальной машины к передаче в Azure, см. в статье [Дисковое шифрование Azure для виртуальных машин IaaS под управлением Windows и Linux](../../security/azure-security-disk-encryption.md).

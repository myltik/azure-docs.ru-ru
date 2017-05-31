---
title: "Настройка виртуальной машины Linux с помощью cloud-init | Документация Майкрософт"
description: "Как с помощью cloud-init и Azure CLI 2.0 настроить создаваемую виртуальную машину Linux"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a7a6daad34525683579e25b9591ed28f2bf29c04
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation"></a>Настройка виртуальной машины Linux во время создания с помощью cloud-init
В этой статье демонстрируется создание сценария cloud-init для задания имени узла, обновления установленных пакетов и управления учетными записями пользователей с помощью Azure CLI 2.0. Скрипты cloud-init вызываются во время создания виртуальной машины с помощью Azure CLI. Дополнительные сведения по установке приложений, записи файлов конфигурации и включении ключей из хранилища Key Vault см. в [этом руководстве](tutorial-automate-vm-deployment.md). Эти действия можно также выполнить с помощью [Azure CLI 1.0](using-cloud-init-nodejs.md).

## <a name="quick-commands"></a>Быстрые команды
Создайте сценарий cloud-init.txt, который задает имя узла, обновляет все пакеты и добавляет в Linux пользователя sudo.

```yaml
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```

Создайте группу ресурсов для запуска виртуальных машин с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем *myResourceGroup*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Выполните команду [az vm create](/cli/azure/vm#create) с параметром `--custom-data`, чтобы создать и настроить (при загрузке) виртуальную машину Linux с помощью cloud-init.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство
При запуске новой виртуальной машины Linux вы получаете стандартную виртуальную машину, не настроенную под ваши потребности. [Cloud-init](https://cloudinit.readthedocs.org) — это стандартный способ добавления сценария или параметров конфигурации в виртуальную машину Linux при ее первоначальной загрузке.

В Azure есть три способа внесения изменений в виртуальную машину Linux в процессе ее развертывания или загрузки.

* Внедрить сценарии с помощью cloud-init.
* Внедрить сценарии с помощью [расширения VMAccess](using-vmaccess-extension.md)Azure.
* Шаблон Azure с использованием cloud-init.
* Шаблон Azure с использованием расширения [CustomScriptExtention](extensions-customscript.md).

Для внедрения сценариев в любой момент после загрузки можно воспользоваться одним из ниже приведенных способов.

* Подключиться по SSH для выполнения команд напрямую.
* Внедрить сценарии с помощью [расширения VMAccess](using-vmaccess-extension.md)Azure принудительно или через шаблон Azure.
* Воспользоваться средствами управления, такими как Ansible, Salt, Chef или Puppet.

> [!NOTE]
> Расширение VMAccess выполняет сценарий от имени привилегированного пользователя, так же как при использовании протокола SSH. Однако применение расширения для виртуальных машин обеспечивает ряд предлагаемых Azure возможностей, которые могут быть полезны в некоторых сценариях.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Доступность cloud-init для псевдонимов быстрого создания образов виртуальных машин Azure:
| Alias | Издатель | ПРЕДЛОЖЕНИЕ | SKU | Version (версия) | cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7,2 |последних |Нет |
| CoreOS |CoreOS |CoreOS |Stable |последних |Да |
| Debian |credativ |Debian |8 |последних |Нет |
| openSUSE |SUSE |openSUSE |13.2 |последних |Нет |
| RHEL |Redhat |RHEL |7,2 |последних |Нет |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |последних |Да |

Мы и наши партнеры работаем над тем, чтобы сценарии cloud-init были добавлены в образы, предоставляемые для Azure.

## <a name="add-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Добавление сценария cloud-init в создаваемую виртуальную машину с помощью Azure CLI
Чтобы запустить сценарий cloud-init при создании виртуальной машины в Azure, укажите файл cloud-init с помощью параметра `--custom-data` интерфейса командной строки Azure.

Создайте группу ресурсов для запуска виртуальных машин с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем *myResourceGroup*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Выполните команду [az vm create](/cli/azure/vm#create) для создания виртуальной машины Linux, используя cloud-init для ее настройки в процессе загрузки.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="create-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Создание сценария cloud-init для задания имени узла виртуальной машины Linux
Один из самых простых, но наиболее важных параметров для любой виртуальной машины Linux — это имя узла. Его можно легко задать с помощью приведенного ниже сценария cloud-init.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Пример сценария cloud-init с именем `cloud_config_hostname.txt`
```yaml
#cloud-config
hostname: myservername
```

Во время первоначального запуска виртуальной машины этот скрипт cloud-init задает имя узла *myservername*. Выполните команду [az vm create](/cli/azure/vm#create) для создания виртуальной машины Linux, используя cloud-init для ее настройки в процессе загрузки.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Выполните вход и проверьте имя узла новой виртуальной машины.

```bash
ssh myVM
hostname
myservername
```

## <a name="create-a-cloud-init-script"></a>Создание скрипта cloud-init
В целях безопасности виртуальную машину Ubuntu следует обновить при первой загрузке. Это можно сделать с помощью приведенного ниже сценария (сценарий зависит от используемого дистрибутива Linux).

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Пример сценария cloud-init `cloud_config_apt_upgrade.txt` для семейства Debian
```yaml
#cloud-config
apt_upgrade: true
```

После загрузки Linux все установленные пакеты обновляются с помощью команды **apt-get**. Выполните команду [az vm create](/cli/azure/vm#create) для создания виртуальной машины Linux, используя cloud-init для ее настройки в процессе загрузки.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_config_apt_upgrade.txt
```

Выполните вход и проверьте, обновились ли все пакеты.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="create-a-cloud-init-script-to-add-a-user-to-linux"></a>Создание сценария cloud-init для добавления пользователя в Linux
Одна из первых задач, которую необходимо выполнить на любой из новых виртуальных машин Linux, — добавить пользователя для себя, чтобы не использовать учетную запись *root*. Ключи SSH наиболее эффективны для обеспечения безопасности и удобства использования. Они добавляются в файл *~/.ssh/authorized_keys* с помощью этого скрипта cloud-init.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Пример сценария cloud-init `cloud_config_add_users.txt` для семейства Debian
```yaml
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

После загрузки Linux все внесенные в список пользователи создаются и добавляются в группу sudo. Выполните команду [az vm create](/cli/azure/vm#create) для создания виртуальной машины Linux, используя cloud-init для ее настройки в процессе загрузки.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_config_add_users.txt
```

Выполните вход и проверьте созданного пользователя.

```bash
ssh myVM
cat /etc/group
```

Выходные данные

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Дальнейшие действия
Сценарии cloud-init становятся стандартным способом изменения виртуальной машины Linux при загрузке. Azure также поддерживает расширения виртуальных машин, которые позволяют изменить виртуальную машину Linux при загрузке или во время ее выполнения. Например, можно воспользоваться расширением VMAccessExtension, предоставляемым в Azure, для сброса данных SSH или сведений о пользователях во время выполнения виртуальной машины. При использовании cloud-init для сброса пароля требуется перезагрузка.

[Обзор расширений и компонентов виртуальной машины](extensions-features.md)

[Управление пользователями, SSH и проверка или восстановление дисков в виртуальных машинах Azure с помощью расширения VMAccess](using-vmaccess-extension.md)

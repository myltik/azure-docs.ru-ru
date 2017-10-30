---
title: "Настройка виртуальной машины Linux с помощью cloud-init | Документация Майкрософт"
description: "Как с помощью cloud-init и Azure CLI 2.0 настроить создаваемую виртуальную машину Linux"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Настройка виртуальной машины Linux в Azure с помощью cloud-init
В этой статье показано, как с помощью [cloud-init](https://cloudinit.readthedocs.io) задать имя узла, обновить установленные пакеты и управлять учетными записями пользователей на виртуальной машине Azure. Эти скрипты cloud-init выполняются во время загрузки, когда вы создаете виртуальную машину из Azure CLI 2.0. Дополнительные сведения о том, как устанавливать приложения, создавать файлы конфигурации и внедрять ключи из хранилища Key Vault см. в [этом руководстве](tutorial-automate-vm-deployment.md). Эти действия можно также выполнить с помощью [Azure CLI 1.0](using-cloud-init-nodejs.md).


## <a name="cloud-init-overview"></a>Обзор cloud-Init
[Пакет cloud-init](https://cloudinit.readthedocs.io) — широко используемое средство, используемое для настройки виртуальной машины Linux при ее первой загрузке. Вы можете использовать cloud-init для установки пакетов, записи файлов или настройки пользователей и параметров безопасности. Так как cloud-init выполняется при начальной загрузке, для применения вашей конфигурации не требуются какие-либо дополнительные действия или обязательные агенты.

Кроме того, cloud-init работает с разными дистрибутивами. Например, для установки пакета не используется **apt-get install** или **yum install**. Вместо этого можно определить список пакетов для установки. Файл cloud-init автоматически использует собственный инструмент управления пакетами из выбранного дистрибутива.

Мы и наши партнеры работаем над тем, чтобы сценарии cloud-init были добавлены в образы, предоставляемые для Azure. В следующей таблице приведены сведения о текущей доступности cloud-init в образах платформы Azure.

| Alias | Издатель | ПРЕДЛОЖЕНИЕ | SKU | Version (версия) |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |последних |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |последних |
| CoreOS |CoreOS |CoreOS |Stable |последних |


## <a name="set-the-hostname-with-cloud-init"></a>Указание имени узла с помощью cloud-init
Файлы cloud-init создаются в [YAML](http://www.yaml.org). Чтобы скрипт cloud-init выполнялся автоматически при создании виртуальной машины в Azure с помощью команды [az vm create](/cli/azure/vm#create), укажите файл cloud-init с параметром `--custom-data`. Давайте рассмотрим некоторые примеры настройки с помощью файла cloud-init. Довольно часто требуется задать имя узла для виртуальной машины. По умолчанию имя узла совпадает с именем виртуальной машины. 

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

В текущей оболочке создайте файл *cloud_init_hostname.txt* и вставьте в него следующую конфигурацию. Например, создайте файл в Cloud Shell, не на локальном компьютере. Вы можете использовать любой редактор. В Cloud Shell введите `sensible-editor cloud_init_hostname.txt`, чтобы создать файл и просмотреть список доступных редакторов. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка:

```yaml
#cloud-config
hostname: myhostname
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create) и укажите файл cloud-init с помощью `--custom-data cloud_init_hostname.txt`, как показано ниже.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

Когда создание завершится, в Azure CLI отобразятся сведения о виртуальной машине. Используйте `publicIpAddress`, чтобы подключиться к виртуальной машине по протоколу SSH. Введите свой адрес, как показано здесь:

```bash
ssh azureuser@publicIpAddress
```

Чтобы увидеть имя виртуальной машины, используйте команду `hostname` следующим образом:

```bash
hostname
```

Виртуальная машина должна вернуть имя узла, заданное в файле cloud-init, как показано в этом примере выходных данных:

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Обновление виртуальной машины с помощью cloud-init
В целях безопасности вы можете настроить автоматическую установку последних обновлений на виртуальной машине при первом запуске. Так как cloud-init одинаково работает в разных дистрибутивах Linux, нет необходимости указывать `apt` или `yum` для диспетчера пакетов. Вместо этого следует указать `package_upgrade`, а процесс cloud-init самостоятельно определит правильный механизм для используемого дистрибутива. Этот рабочий процесс позволяет использовать один и тот же скрипт cloud-init с разными дистрибутивами.

Чтобы увидеть процесс обновления в действии, создайте файл cloud-init с именем *cloud_init_upgrade.txt* и вставьте в него следующую конфигурацию:

```yaml
#cloud-config
package_upgrade: true
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create) и укажите файл cloud-init с помощью `--custom-data cloud_init_upgrade.txt`, как показано ниже.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

Подключитесь по протоколу SSH к общедоступному IP-адресу виртуальной машины, который указан в выходных данных предыдущей команды. Введите свой общедоступный IP-адрес, как показано здесь:

```bash
ssh azureuser@publicIpAddress
```

Запустите средство управления пакетами и проверьте наличие обновлений. В этом примере используется `apt-get` на виртуальной машине Ubuntu.

```bash
sudo apt-get upgrade
```

После того как cloud-init проверит и установит обновления в процессе загрузки, доступных для установки обновлений не должно оставаться, как показано в следующем примере выходных данных:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Добавление пользователя в виртуальную машину с помощью cloud-init
Одна из первых задач, которую необходимо выполнить на любой из новых виртуальных машин Linux, — добавить пользователя для себя, чтобы не использовать учетную запись *root*. Для безопасности и удобства мы рекомендуем применять ключи SSH. Их можно добавить в файл *~/.ssh/authorized_keys* с помощью следующего скрипта cloud-init.

Чтобы добавить пользователя на виртуальной машине Linux, создайте файл cloud-init с именем *cloud_init_add_user.txt* и вставьте в него следующую конфигурацию: В качестве значения для *ssh-authorized-keys* укажите собственный открытый ключ SSH, например содержимое файла *~/.ssh/id_rsa.pub*.

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create) и укажите файл cloud-init с помощью `--custom-data cloud_init_add_user.txt`, как показано ниже.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

Подключитесь по протоколу SSH к общедоступному IP-адресу виртуальной машины, который указан в выходных данных предыдущей команды. Введите свой общедоступный IP-адрес, как показано здесь:

```bash
ssh myadminuser@publicIpAddress
```

Чтобы проверить, добавлен ли пользователь и правильное ли указаны группы на виртуальной машине, просмотрите содержимое файла */etc/groups*, как показано ниже.

```bash
cat /etc/group
```

В следующем примере выходных данных демонстрируется, что пользователь из файла *cloud_init_add_user.txt* был успешно создан на виртуальной машине и добавлен в соответствующую группу.

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Дальнейшие действия
Сценарии cloud-init — один из стандартных способов внесения изменений в виртуальную машину Linux при запуске. В Azure вы также можете использовать расширения виртуальной машины, чтобы вносить изменения в виртуальную машину Linux во время запуска или в процессе работы. Например, расширение виртуальной машины Azure можно применить для выполнения скрипта на работающей виртуальной машине, а не только при первом запуске системы. Дополнительные сведения о расширениях виртуальной машины см. в статье [Обзор расширений и компонентов виртуальных машин под управлением Linux](extensions-features.md), а примеры использования этих расширений — в статье [Управление пользователями с правами администратора, SSH и проверка или восстановление дисков на виртуальных машинах Linux с помощью расширения VMAccess и Azure CLI 2.0](using-vmaccess-extension.md).
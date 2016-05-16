<properties
    pageTitle="Настройка виртуальной машины Linux во время создания с помощь cloud-init | Microsoft Azure"
    description="Настройка виртуальной машины Linux во время создания с помощь cloud-init."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# Настройка виртуальной машины Linux во время создания с помощь cloud-init

В этой статье демонстрируется создание сценария cloud-init для задания имени узла, обновления установленных пакетов и управления учетными записями пользователей. Такие сценарии cloud-init затем будут использоваться во время создания виртуальной машины с помощью [интерфейса командной строки Azure](../xplat-cli-install.md).

## Предварительные требования

Необходимые компоненты: [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/), [открытый и закрытый ключи SSH](virtual-machines-linux-mac-create-ssh-keys.md), группа ресурсов Azure, в которой будут запускаться виртуальные машины Linux, и интерфейс командной строки Azure, установленный и переведенный в режим ARM с помощью `azure config mode arm`.

## Введение

При запуске новой виртуальной машины Linux вы получаете стандартную виртуальную машину, не настроенную под ваши потребности. [Cloud-init](https://cloudinit.readthedocs.org) — это стандартный способ добавления сценария или параметров конфигурации в виртуальную машину Linux при ее первоначальной загрузке.

В Azure есть три разных способа внесения изменений в запускаемую виртуальную машину Linux.

- Вы можете добавить сценарии с помощью cloud-init.
- Вы можете добавить сценарии с помощью Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).
- Вы можете задать пользовательские параметры в шаблоне Azure и использовать его для запуска и настройки виртуальной машины Linux. Шаблоны поддерживают cloud-init, расширение CustomScript для виртуальных машин и многие другие возможности.

Для добавления сценариев в любой момент вы можете:

- использовать SSH для выполнения команд напрямую, использовать Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) принудительно или в шаблоне Azure либо использовать стандартные средства управления конфигурацией, такие как Ansible, Salt, Chef и Puppet, работающие по протоколу SSH, после завершения загрузки виртуальной машины.

Примечание. Хотя расширение [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) просто выполняет сценарий от имени привилегированного пользователя, так же как при использовании протокола SSH, применение расширения для виртуальных машин обеспечивает ряд предлагаемых Azure возможностей, которые могут быть полезны в некоторых сценариях.

## Быстрые команды

Создание сценария cloud-init создания имени узла

```bash
#cloud-config
hostname: exampleServerName
```

Создание сценария cloud-init обновления Linux при первой загрузке для семейства Debian

```bash
#cloud-config
apt_upgrade: true
```

Создание сценария cloud-init добавления пользователя

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

## Подробное пошаговое руководство

### Добавление сценария cloud-init в создаваемую виртуальную машину с помощью интерфейса командной строки Azure

Чтобы запустить сценарий cloud-init при создании виртуальной машины в Azure, укажите файл cloud-init с помощью параметра `--custom-data` интерфейса командной строки Azure.

Примечание. Хотя в этой статье рассматривается использование параметра `--custom-data` для файлов cloud-init, с его помощью можно передавать произвольный код или файлы. Если виртуальная машина Linux уже знает, что делать с такими файлами, они выполняются автоматически.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
```

### Создание сценария cloud-init для задания имени узла виртуальной машины Linux

Один из самых простых, но наиболее важных параметров для любой виртуальной машины Linux — это имя узла. Его можно легко задать с помощью приведенного ниже сценария cloud-init.

#### Пример сценария cloud-init с именем `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: exampleServerName
```

Во время первоначального запуска виртуальной машины этот сценарий cloud-init задает имя узла `exampleServerName`.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_hostname.txt
```

Выполните вход и проверьте имя узла новой виртуальной машины.

```bash
ssh exampleVM
hostname
exampleServerName
```

### Создание сценария cloud-init для обновления виртуальной машины Linux

В целях безопасности виртуальную машину Ubuntu следует обновить при первой загрузке. Это можно сделать с помощью приведенного ниже сценария (сценарий зависит от используемого дистрибутива Linux).

#### Пример сценария cloud-init `cloud_config_apt_upgrade.txt` для семейства Debian

```bash
#cloud-config
apt_upgrade: true
```

После загрузки новой виртуальной машины Linux все установленные пакеты мгновенно обновляются с помощью `apt-get`.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_apt_upgrade.txt
```

Выполните вход и проверьте, обновились ли все пакеты.

```bash
ssh exampleVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Создание сценария cloud-init для добавления пользователя в Linux

Одной из первых задач, которую нужно выполнить в любой новой виртуальной машине Linux, является добавление пользователя для себя или для того, чтобы не использовалась учетная запись `root`. Это целесообразно и по соображениям безопасности, и с точки зрения удобства работы, так как добавление открытого ключа SSH в файл `~/.ssh/authorized_keys` этого пользователя позволяет выполнять безопасный вход по протоколу SSH, не вводя пароль.

#### Пример сценария cloud-init `cloud_config_add_users.txt` для семейства Debian

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

После загрузки новой виртуальной машины Linux он создаст пользователя и добавит его в группу sudo.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_add_users.txt
```

Выполните вход и проверьте созданного пользователя.

```bash
cat /etc/group
```

Выходные данные

```bash
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

<!---HONumber=AcomDC_0504_2016-->
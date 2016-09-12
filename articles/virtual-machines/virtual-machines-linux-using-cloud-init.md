.<properties
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
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# Настройка виртуальной машины Linux во время создания с помощь cloud-init

В этой статье демонстрируется создание сценария cloud-init для задания имени узла, обновления установленных пакетов и управления учетными записями пользователей. Сценарии cloud-init вызываются во время создания виртуальной машины с помощью интерфейса командной строки Azure (Azure CLI).

## Предварительные требования

Необходимые компоненты: [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/), [открытый и закрытый ключи SSH](virtual-machines-linux-mac-create-ssh-keys.md) и [интерфейс командной строки Azure](../xplat-cli-install.md), переведенный в режим Azure Resource Manager с помощью `azure config mode arm`.

## Быстрые команды

Создайте сценарий cloud-init.txt, который задает имя узла, обновляет все пакеты и добавляет в Linux пользователя sudo.

```bash
#cloud-config
hostname: exampleServerName
apt_upgrade: true
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Создайте виртуальную машину Linux, используя cloud-init для ее настройки в процессе загрузки.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

## Введение

При запуске новой виртуальной машины Linux вы получаете стандартную виртуальную машину, не настроенную под ваши потребности. [Cloud-init](https://cloudinit.readthedocs.org) — это стандартный способ добавления сценария или параметров конфигурации в виртуальную машину Linux при ее первоначальной загрузке.

В Azure есть три разных способа внесения изменений в виртуальную машину Linux в процессе ее развертывания или загрузки.

- Внедрить сценарии с помощью cloud-init.
- Внедрить сценарии с помощью [расширения VMAccess](virtual-machines-linux-using-vmaccess-extension.md) Azure.
- Шаблон Azure с использованием cloud-init.
- Шаблон Azure с использованием расширения [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Для внедрения сценариев в любой момент после загрузки можно воспользоваться одним из ниже приведенных способов.

- Подключиться по SSH для выполнения команд напрямую.
- Внедрить сценарии с помощью [расширения VMAccess](virtual-machines-linux-using-vmaccess-extension.md) Azure принудительно или через шаблон Azure.
- Воспользоваться средствами управления, такими как Ansible, Salt, Chef или Puppet.

>[AZURE.NOTE]Расширение VMAccess выполняет сценарий от имени привилегированного пользователя, так же как при использовании протокола SSH. Однако применение расширения для виртуальных машин обеспечивает ряд предлагаемых Azure возможностей, которые могут быть полезны в некоторых сценариях.

### Доступность cloud-init для псевдонимов быстрого создания образов виртуальных машин Azure:

| Alias | Издатель | ПРЕДЛОЖЕНИЕ | SKU | Version (версия) | cloud-init |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS | OpenLogic | CentOS | 7,2 | последних | Нет |
| CoreOS | CoreOS | CoreOS | Stable | последних | Да |
| Debian | credativ | Debian | 8 | последних | Нет |
| openSUSE | SUSE | openSUSE | 13\.2 | последних | Нет |
| RHEL | Redhat | RHEL | 7,2 | последних | Нет |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | последних | Да |

Корпорация Майкрософт работает со своими партнерами над тем, чтобы сценарии cloud-init были добавлены в образы, предоставляемые ими для Azure.


## Подробное пошаговое руководство

### Добавление сценария cloud-init в создаваемую виртуальную машину с помощью интерфейса командной строки Azure

Чтобы запустить сценарий cloud-init при создании виртуальной машины в Azure, укажите файл cloud-init с помощью параметра `--custom-data` интерфейса командной строки Azure.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

### Создание сценария cloud-init для задания имени узла виртуальной машины Linux

Один из самых простых, но наиболее важных параметров для любой виртуальной машины Linux — это имя узла. Его можно легко задать с помощью приведенного ниже сценария cloud-init.

#### Пример сценария cloud-init с именем `cloud_config_hostname.txt`

``` bash
#cloud-config
hostname: exampleServerName
```

Во время первоначального запуска виртуальной машины этот сценарий cloud-init задает имя узла `exampleServerName`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
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

После загрузки Linux все установленные пакеты обновляются через `apt-get`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
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

Одна из первых задач, которую необходимо выполнить на любой из новых виртуальных машин Linux, — это добавление пользователя для себя или для того, чтобы не использовалась учетная запись `root`. Ключи SSH наиболее эффективны для обеспечения безопасности и удобства использования. Они добавляются в файл `~/.ssh/authorized_keys` с помощью данного сценария cloud-init.

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

После загрузки Linux все внесенные в список пользователи создаются и добавляются в группу sudo.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
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

## Дальнейшие действия

Сценарии cloud-init становятся стандартным способом изменения виртуальной машины Linux при загрузке. Azure также поддерживает расширения виртуальных машин, которые позволяют изменить виртуальную машину Linux при загрузке или во время ее выполнения. Например, можно воспользоваться расширением VMAccessExtension, предоставляемым в Azure, для сброса данных SSH или сведений о пользователях во время выполнения виртуальной машины. При использовании cloud-init для сброса пароля требуется перезагрузка.

[Обзор расширений и компонентов виртуальной машины](virtual-machines-linux-extensions-features.md)

[Управление пользователями, SSH и проверка или восстановление дисков в виртуальных машинах Azure с помощью расширения VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0831_2016-->
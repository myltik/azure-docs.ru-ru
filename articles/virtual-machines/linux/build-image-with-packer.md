---
title: "Создание образов виртуальных машин Linux в Azure с помощью Packer | Документация Майкрософт"
description: "Сведения об использовании Packer для создания образов виртуальных машин Linux в Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fa30f7b9aebf3b9a3fb1e037983e8460aa76442e
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Создание образов виртуальных машин Linux в Azure с помощью Packer
Каждая виртуальная машина в Azure создается из образа, определяющего дистрибутив Linux и версию операционной системы. Образы могут содержать предварительно установленные приложения и конфигурации. Azure Marketplace предоставляет большое количество образов Майкрософт и сторонних разработчиков для наиболее распространенных операционных систем и приложений. Кроме того, вы можете создать собственные настраиваемые образы, отвечающие конкретным потребностям. В этой статье описывается определение и создание пользовательских образов в Azure с использованием средства с открытым кодом [Packer](https://www.packer.io/).


## <a name="create-supporting-azure-resources"></a>Создание вспомогательных ресурсов Azure
В процессе сборки исходной виртуальной машины Packer создает временные ресурсы Azure. Чтобы сохранить эту исходную виртуальную машину для использования в качестве образа, необходимо определить группу ресурсов и учетную запись хранения. Выходные данные процесса сборки Packer хранятся в этой группе ресурсов и учетной записи хранения.

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create -n myResourceGroup -l eastus
```

Создайте учетную запись хранения с помощью команды [az storage account create](/cli/azure/storage/account#create). Имя учетной записи хранения должно быть уникальным, содержать от 3 до 24 символов и состоять только из цифр и строчных букв. В следующем примере создается учетная запись хранения с именем *mystorageaccount*:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --sku Standard_LRS
```


## <a name="create-azure-credentials"></a>Создание учетных данных Azure
Packer выполняет проверку подлинности с помощью субъекта-службы Azure. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и средствами автоматизации, такими как Packer. Вы можете определять разрешения на то, какие операции может выполнять субъект-служба в Azure, и управлять ими.

Создайте субъект-службу с помощью командлета [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) и выведите учетные данные, необходимые для Packer:

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Ниже приведен пример выходных данных предыдущей команды:

```azurecli
"f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
"0e760437-bf34-4aad-9f8d-870be799c55d",
"72f988bf-86f1-41af-91ab-2d7cd011db47"
```

Для проверки подлинности в Azure получите идентификатор подписки Azure с помощью команды [az account show](/cli/azure/account#show) следующим образом:

```azurecli
az account show --query [id] --output tsv
```

Используйте выходные данные этих двух команд на следующем шаге.


## <a name="define-packer-template"></a>Определение шаблона Packer
Чтобы собрать образы, создайте шаблон в формате JSON. В шаблоне определите средства разработки и подготовки, выполняющие процесс сборки. Packer использует [средство подготовки для Azure](https://www.packer.io/docs/builders/azure.html), которое позволяет определить ресурсы Azure, такие как учетные данные субъекта-службы, созданные на предыдущем шаге.

Создайте файл с именем *ubuntu.json* и вставьте следующее содержимое. Введите свои значения следующим образом:

| Параметр       | Где можно получить |
|-----------------|----------------------------------------------------|
| *client_id*      | Первая строка выходных данных из `az ad sp` создает команду *appId* |
| *client_secret*  | Вторая строка выходных данных из `az ad sp` создает команду *password* |
| *tenant_id*      | Третья строка выходных данных из `az ad sp` создает команду *tenant* |
| *subscription_id* | Выходные данные команды `az account show` |
| *storage_account* | Имя, указанное в `az storage account create` |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04.0-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Этот шаблон создает образ Ubuntu 16.04 LTS, устанавливает NGINX, а затем отзывает виртуальную машину.

> [!NOTE]
> Если вы расширите этот шаблон, чтобы подготовить учетные данные пользователя, настройте команду средства подготовки, которая отзывает агент Azure для чтения `-deprovision` вместо `deprovision+user`.
> Флаг `+user` удаляет все учетные записи пользователей из исходной виртуальной машины.


## <a name="build-packer-image"></a>Создание образа Packer
Если средство Packer еще не установлено на локальном компьютере, [следуйте инструкциям по его установке](https://www.packer.io/docs/install/index.html).

Создайте образ, указав файл шаблона Packer следующим образом:

```bash
./packer build ubuntu.json
```

Ниже приведен пример выходных данных предыдущей команды:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : engineering
==> azure-arm:  ->> task : image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '13.90.250.248'
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /tmp/packer-shell529418469
    azure-arm: Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
    azure-arm: Hit:2 http://azure.archive.ubuntu.com/ubuntu xenial InRelease
    azure-arm: Get:3 http://azure.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
    azure-arm: Get:4 http://azure.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
    [snip]
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd?se=2017-07-08T20%3A57%3A53Z&sig=yl1yl3I2gKnO0I%2B7paw%2FQzKT5dawf5i%2B
LPmATMt5ot4%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json?se=2017-07-08T20%3A57%3A53Z&sig=GB1iSl0hhw1ZYG4nl%2BCfR9WEaquCF
OEhNtKlvp%2B5TdE%3D&sp=r&sr=b&sv=2015-02-21
```


## <a name="create-azure-image"></a>Создание образа Azure
Выходными данными процесса сборки Packer является виртуальный жесткий диск (VHD) в указанной учетной записи хранения. Создайте образ Azure из этого виртуального жесткого диска с помощью команды [az image create](/cli/azure/image#create) и укажите путь `OSDiskUri`, определенный в конце выходных данных сборки Packer. В следующем примере создается образ с именем `myImage`:

```azurecli
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --os-type linux \
    --source https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
```

Этот образ можно использовать для создания виртуальных машин в своей подписке Azure. Вы можете создавать виртуальные машины не только в той же группе ресурсов, где расположен исходный образ.


## <a name="create-vm-from-azure-image"></a>Создание виртуальной машины на основе образа Azure
Теперь можно создать виртуальную машину из образа с помощью команды [az vm create](/cli/azure/vm#create). Укажите образ, созданный с помощью параметра `--image`. В следующем примере создаются виртуальная машина с именем *myVM* из *myImage* и ключи SSH, если они не существуют.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Создание виртуальной машины занимает несколько минут. Когда виртуальная машина создана, запишите значение `publicIpAddress`, отображаемое в Azure CLI. Это адрес для доступа к сайту NGINX через веб-браузер.

Чтобы разрешить передачу веб-трафика для виртуальной машины, откройте порт 80 для Интернета командой [az vm open-port](/cli/azure/vm#open-port).

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Тестирование виртуальной машины и NGINX
Теперь можно открыть веб-браузер и ввести в адресной строке `http://publicIpAddress`. Укажите собственный общедоступный IP-адрес, настроенный при создании виртуальной машины. Отобразится страница NGINX по умолчанию, как показано ниже.

![Сайт nginx по умолчанию](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Дальнейшие действия
В этом примере с помощью Packer вы создали образ виртуальной машины с уже установленным NGINX. Этот образ виртуальной машины можно использовать наряду с имеющимися рабочими процессами развертывания, как например развертывание приложений на виртуальных машинах, созданных из образа с помощью Ansible, Chef или Puppet.

Дополнительный пример шаблонов Packer для других дистрибутивов Linux см. в этом [репозитории GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).

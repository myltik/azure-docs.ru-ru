---
title: Использование Terraform для создания готовой виртуальной машины Linux в Azure | Документация Майкрософт
description: Узнайте, как использовать Terraform для создания готовой среды виртуальных машин Linux в Azure и управления ею
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: 6b2dc2e8859efdcc57c45831381bc1870495ecf6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776029"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform

Terraform позволяет определить и создать развертывания комплексной инфраструктуры в Azure. Шаблоны Terraform нужно создавать в понятном формате, чтобы их можно было использовать для создания и настройки ресурсов Azure в согласованном и воспроизводимом виде. В этой статье показано, как создать готовую среду Linux и вспомогательные ресурсы с помощью Terraform. Узнайте, как [установить и настроить Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Создание подключения Azure и группы ресурсов

Мы разберем каждый раздел шаблона Terraform. Здесь также приведена полная версия [шаблона Terraform](#complete-terraform-script), который можно скопировать и вставить.

В разделе `provider` указывается, что Terraform нужно использовать поставщик Azure. Чтобы получить значения для *subscription_id*, *client_id*, *client_secret* и *tenant_id*, см. сведения в статье [Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure](terraform-install-configure.md). 

> [!TIP]
> При создании переменных среды для значений или использовании [Azure Cloud Shell Bash](/azure/cloud-shell/overview) не нужно включать объявления переменных в этом разделе.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

В следующем разделе создается группа ресурсов с именем `myResourceGroup` в расположении `eastus`:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}
```

В дополнительных разделах нужно ссылаться на группу ресурсов, используя шаблон *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Создание виртуальной сети
В следующем разделе создается виртуальная сеть с именем *myVnet* в адресном пространстве *10.0.0.0/16*.

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```
В следующем разделе создается подсеть с именем *mySubnet* в виртуальной сети *myVnet*.

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Создание общедоступного IP-адреса
Для доступа к ресурсам через Интернет создайте общедоступный IP-адрес и назначьте его виртуальной машине. В следующем разделе создается общедоступный IP-адрес с именем *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Создание группы безопасности сети
Группы безопасности сети контролируют передачу исходящего и входящего сетевого трафика виртуальной машины. В следующем разделе создается группа безопасности сети с именем *myNetworkSecurityGroup* и определяется правило, разрешающее передачу трафика SSH через TCP-порт 22:

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Создание виртуальных сетевых адаптеров
Виртуальный сетевой адаптер позволяет виртуальной машине подключаться к определенной виртуальной сети, общедоступному IP-адресу и группе безопасности сети. В следующем разделе шаблона Terraform создается виртуальный сетевой адаптер с именем *myNIC*, подключенный к созданным ресурсам виртуальной сети:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Создание учетной записи хранения для диагностики
Чтобы сохранить диагностику загрузки для виртуальной машины, необходима учетная запись хранения. Диагностика загрузки может помочь в устранении неполадок и наблюдении за состоянием виртуальной машины. Создаваемая учетная запись хранения используется только для хранения данных диагностики загрузки. Каждая учетная запись хранения должна иметь уникальное имя, поэтому в следующем разделе генерируется некоторый произвольный текст:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Теперь создайте учетную запись хранения. В следующем разделе создается учетная запись хранения с именем, основанным на произвольном тексте, созданном на предыдущем шаге:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Создание виртуальной машины

Последним шагом является создание виртуальной машины и использование всех созданных ресурсов. В следующем разделе создается виртуальная машина с именем *myVM* и подключается виртуальный сетевой адаптер с именем *myNIC*. Используется образ последней версии *Ubuntu 16.04-LTS*, а пользователь с именем *azureuser* создается с отключенной проверкой подлинности по паролю.

 Данные ключа SSH предоставлены в разделе *ssh_keys*. Укажите допустимый открытый ключ SSH в поле *key_data*.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Полный скрипт Terraform

Чтобы объединить все эти разделы и увидеть Terraform в действии, создайте файл с именем *terraform_azure.tf* и вставьте следующее содержимое:

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Создание и развертывание инфраструктуры
После создания шаблона Terraform необходимо сразу инициализировать Terraform. Это действие гарантирует, что в Terraform имеются все необходимые компоненты для создания шаблона в Azure.

```bash
terraform init
```

Далее необходима проверка шаблона в Terraform. Этот шаг сравнивает запрашиваемые ресурсы со сведениями о состоянии, сохраненными Terraform, а затем выводит запланированное выполнение. Ресурсы *не* создаются в Azure.

```bash
terraform plan
```

После выполнения предыдущей команды вы должны увидеть что-то вроде этого:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Если все выглядит правильно и вы готовы создать инфраструктуру в Azure, примените шаблон в Terraform:

```bash
terraform apply
```

После завершения действий в Terraform инфраструктура виртуальной машины будет готова. Получите общедоступный IP-адрес виртуальной машины с помощью команды [az vm show](/cli/azure/vm#az_vm_show).

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

После этого можно подключиться по SSH к виртуальной машине:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Дополнительная информация
Вы создали базовую инфраструктуру в Azure с помощью Terraform. Для более сложных сценариев, включая примеры с использованием подсистемы балансировки нагрузки и масштабируемых наборов виртуальных машин, см. многочисленные [примеры Terraform для Azure](https://github.com/hashicorp/terraform/tree/master/examples). Обновленный список поддерживаемых поставщиков Azure см. в [документации Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

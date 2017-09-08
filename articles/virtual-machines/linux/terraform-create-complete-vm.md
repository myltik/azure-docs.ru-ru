---
title: "Создание базовой инфраструктуры в Azure с помощью Terraform | Документация Майкрософт"
description: "Узнайте, как создать ресурсы Azure с помощью Terraform"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: aa0926de32dd85f4460bbfa84b7a6007e2199d51
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>Создание базовой инфраструктуры в Azure с помощью Terraform
В этой статье описано, какие шаги необходимо выполнить для подготовки виртуальной машины вместе с базовой инфраструктурой в Azure. Вы узнаете, как написать скрипты Terraform и визуализировать изменения перед их внесением в облачной инфраструктуре. Вы также узнаете, как создать инфраструктуру в Azure с помощью Terraform.

Чтобы начать работу, в любом текстовом редакторе (Visual Studio Code, Sublime, Vim или др.) создайте файл с именем \terraform_azure101.tf. Точное имя файла не имеет значения, так как Terraform принимает имя папки в качестве параметра — выполняются все скрипты в папке. Скопируйте приведенный ниже код и вставьте его в новый файл:

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_appId_from_script_execution"
  client_secret   = "your_password_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
В разделе `provider` скрипта вы указываете Terraform использовать поставщик Azure для подготовки ресурсов в скрипте. Воспользуйтесь руководством по [установке и настройке Terraform](terraform-install-configure.md), чтобы получить значения для параметров subscription_id, appId, password и tenant_id. Если вы создали переменные среды для значений в этом блоке, то не следует его включать. 

Ресурс `azurerm_resource_group` указывает Terraform создать группу ресурсов. Ниже приведены другие типы ресурсов, которые также доступны в Terraform.

## <a name="execute-the-script"></a>Выполнение скрипта
Когда скрипт сохранен, перейдите в консоль или командную строку и введите следующую команду:
```
terraform init
```
 для инициализации поставщика Terraform для Azure. Затем перейдите в каталог **terraformscripts** и выполните следующую команду:
```
terraform plan
```
Мы использовали команду Terraform `plan`, которая просматривает ресурсы, определенные в скрипте. Она сравнивает их со сведениями о состоянии, сохраненными Terraform, а затем выводит запланированное выполнение _без_ фактического создания ресурсов в Azure. 

После выполнения предыдущей команды вы должны увидеть что-то вроде этого:

![План Terraform](./media/terraform/tf_plan2.png)

Если все выглядит правильно, подготовьте эту новую группу ресурсов в Azure, выполнив следующее: 
```
terraform apply
```
На портале Azure вы должны увидеть новую пустую группу ресурсов с именем `terraformtest`. В следующем разделе показано, как добавить виртуальную машину и всю поддерживающую инфраструктуру для этой виртуальной машины в группу ресурсов.

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Подготовка виртуальной машины Ubuntu с помощью Terraform
Давайте расширим скрипт Terraform, который мы создали выше, добавив в него сведения, необходимые для подготовки виртуальной машины под управлением Ubuntu. В следующих разделах вы подготовите такие ресурсы:

* сеть с одной подсетью;
* сетевая карта; 
* учетная запись хранения для диагностических данных виртуальной машины;
* общедоступный IP-адрес;
* виртуальная машина, использующая все перечисленные выше ресурсы. 

Подробную документацию по каждому из ресурсов Azure Terraform см. в разделе [документация Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

Для удобства также предоставляется полная версия [сценария подготовки](#complete-terraform-script).

### <a name="extend-the-terraform-script"></a>Расширение скрипта Terraform
Вы можете расширить ранее созданный скрипт, используя следующие ресурсы: 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
Предыдущий скрипт создает виртуальную сеть и подсеть внутри этой виртуальной сети. Обратите внимание на ссылку на уже созданную группу ресурсов ("${azurerm_resource_group.helloterraform.name}"), которая есть в определениях виртуальной сети и подсети.

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Предыдущие фрагменты скрипта создают общедоступный IP-адрес и сетевой интерфейс, который использует созданный публичный IP-адрес. Обратите внимание на ссылки на subnet_id и public_ip_address_id. Terraform обладает встроенной системой аналитики, позволяющей понять, что сетевой интерфейс имеет зависимость от ресурсов, которые должны быть созданы раньше него.

~~~~
# create a random id
resource "random_id" "randomId" {
  keepers = {
    # Generate a new id only when a new resource group is defined
    resource_group = "${azurerm_resource_group.helloterraform.name}"
  }

  byte_length = 8
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "West US"
    account_type = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Вы создали учетную запись хранения. В этой учетной записи хранения виртуальная машина будет хранить свои сведения о диагностике.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_DS1_v2"

    os_profile {
        computer_name = "hostname"
        admin_username = "azureuser"
        admin_password = ""
    }

    os_profile_linux_config {
        disable_password_authentication = true

        ssh_keys {
            path = "/home/azureuser/.ssh/authorized_keys"
            key_data = "... INSERT OPENSSH PUBLIC KEY HERE ..."
        }
    }

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "16.04.0-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        managed_disk_type = "Premium_LRS"
        create_option = "FromImage"
    } 

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Наконец, предыдущий фрагмент создает виртуальную машину, которая использует все подготовленные ресурсы. Это учетная запись хранения для диагностических данных виртуальной машины, сетевой интерфейс с указанными общедоступным IP-адресом и подсетью и группа ресурсов, которую вы уже создали. Обратите внимание на свойство vm_size, в котором сценарий указывает номер SKU Azure Standard (DS1v2).

Необходимо предоставить открытый ключ SSH. Вставьте значение открытого ключа в приведенный выше раздел **... INSERT OPENSSH PUBLIC KEY HERE ...** (Вставьте сюда значение открытого ключа OpenSSH). Можно использовать существующую пару ключей SSH или следовать инструкциям в документации для [Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows) или [Linux/macOS](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys), чтобы создать пару ключей.

### <a name="execute-the-script"></a>Выполнение скрипта
Когда скрипт будет сохранен, перейдите в консоль или командную строку и введите следующее:
```
terraform apply
```
Через некоторое время ресурсы, включая виртуальную машину, появятся в группе ресурсов `terraformtest` на портале Azure.

## <a name="complete-terraform-script"></a>Полный скрипт Terraform

Для удобства ниже приведен полный скрипт Terraform, который подготавливает всю инфраструктуру, рассмотренную в этой статье.

```
# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}

# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# create a random id
resource "random_id" "randomId" {
  keepers = {
    # Generate a new id only when a new resource group is defined
    resource_group = "${azurerm_resource_group.helloterraform.name}"
  }

  byte_length = 8
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "West US"
    account_type = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_DS1_v2"

    os_profile {
        computer_name = "hostname"
        admin_username = "azureuser"
        admin_password = ""
    }

    os_profile_linux_config {
        disable_password_authentication = true

        ssh_keys {
            path = "/home/azureuser/.ssh/authorized_keys"
            key_data = "... INSERT OPENSSH PUBLIC KEY HERE ..."
        }
    }

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "16.04.0-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        managed_disk_type = "Premium_LRS"
        create_option = "FromImage"
    } 

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
Вы создали базовую инфраструктуру в Azure с помощью Terraform. Для более сложных сценариев, включая примеры с использованием подсистемы балансировки нагрузки и масштабируемых наборов виртуальных машин, см. многочисленные [примеры Terraform для Azure](https://github.com/hashicorp/terraform/tree/master/examples). Обновленный список поддерживаемых поставщиков Azure см. в [документации Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).


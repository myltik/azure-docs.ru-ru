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
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 5377f84a29482e07ae089c7025dc41b8e12bfa3a
ms.contentlocale: ru-ru
ms.lasthandoff: 08/11/2017

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

# create a resource group 
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
* учетная запись хранения с контейнером хранилища;
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
        environment = "TerraformDemo"
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
}
~~~~
Предыдущие фрагменты скрипта создают общедоступный IP-адрес и сетевой интерфейс, который использует созданный публичный IP-адрес. Обратите внимание на ссылки на subnet_id и public_ip_address_id. Terraform обладает встроенной системой аналитики, позволяющей понять, что сетевой интерфейс имеет зависимость от ресурсов, которые должны быть созданы раньше него.

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
Здесь вы создали учетную запись хранения и определили в ней контейнер хранилища. В этой учетной записи хранения хранятся виртуальные жесткие диски (VHD) для создаваемой виртуальной машины.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
~~~~
Наконец, предыдущий фрагмент создает виртуальную машину, которая использует все подготовленные ресурсы. Это учетная запись хранения и контейнер для VHD, сетевой интерфейс с указанным общедоступным IP-адресом и подсетью и группа ресурсов, которую вы уже создали. Обратите внимание на свойство vm_size, в котором сценарий указывает номер SKU Azure (A0).

### <a name="execute-the-script"></a>Выполнение скрипта
Когда скрипт будет сохранен, перейдите в консоль или командную строку и введите следующее:
```
terraform apply
```
Через некоторое время ресурсы, включая виртуальную машину, появятся в группе ресурсов `terraformtest` на портале Azure.

## <a name="complete-terraform-script"></a>Полный скрипт Terraform

Для удобства ниже приведен полный скрипт Terraform, который подготавливает всю инфраструктуру, рассмотренную в этой статье.

```
variable "resourcesname" {
  default = "helloterraform"
}

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

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
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
}

# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
Вы создали базовую инфраструктуру в Azure с помощью Terraform. Для более сложных сценариев, включая примеры с использованием подсистемы балансировки нагрузки и масштабируемых наборов виртуальных машин, см. многочисленные [примеры Terraform для Azure](https://github.com/hashicorp/terraform/tree/master/examples). Обновленный список поддерживаемых поставщиков Azure см. в [документации Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).


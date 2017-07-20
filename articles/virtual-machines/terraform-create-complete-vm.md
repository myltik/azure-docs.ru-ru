---
title: "Создание базовой инфраструктуры в Azure с помощью Terraform | Документация Майкрософт"
description: "Узнайте, как создать ресурсы Azure с помощью Terraform."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 8b8b3b0b46f79058ee69b9a2014581df433f8d3f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017

---

# <a name="create-basic-infrastructure-in-azure-using-terraform"></a>Создание базовой инфраструктуры в Azure с помощью Terraform
В этой статье описано, какие шаги необходимо выполнить для подготовки в Azure виртуальной машины вместе с базовой инфраструктурой. Вы узнаете, как создать сценарии Terraform и визуализировать изменения перед их внесением в облачной инфраструктуре. Вы также узнаете, как создать инфраструктуру в Azure с помощью Terraform.

Чтобы начать работу, в любом текстовом редакторе (Visual Studio Code, Sublime, Vim или др.) создайте файл с именем _terraform_azure101.tf_. Точное имя файла не имеет значения, так как Terraform принимает имя папки в качестве параметра — выполняются все сценарии в папке. Скопируйте приведенный ниже код и вставьте его в новый файл:

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_client_id_from_script_execution"
  client_secret   = "your_client_secret_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
В разделе "provider" сценария вы указываете Terraform использовать поставщик Azure для подготовки ресурсов в сценарии. Воспользуйтесь руководством по [установке и настройке Terraform](terraform-install-configure.md), чтобы получить значения для параметров subscription_id, client_id, client_secret и tenant_id. Обратите также внимание, что если вы создали переменные среды для значений в этом блоке, то не следует его включать. 

Ресурс "azure_rm_resource_group" указывает Terraform создать группу ресурсов. Ниже приводятся другие типы ресурсов, которые также доступны в Terraform.

## <a name="executing-the-script"></a>Выполнение сценария
Когда сценарий сохранен, перейдите в консоль или командную строку и введите следующую команду:
```
terraform plan terraformscripts
```
В приведенной выше команде предполагается, что "terraformscripts" — это папка, в которой был сохранен сценарий. Мы использовали команду Terraform "plan", которая просматривает ресурсы, определенные в сценарии, сравнивает их со сведениями о состоянии, сохраненными Terraform, а затем выводит запланированное выполнение, фактически _не_ создавая ресурсов в Azure. 

После выполнения приведенной выше команды должно отобразиться нечто вроде этого:

![Снимок экрана Terraform: команда "plan"](linux/media/terraform/tf_plan2.png)

Все выглядит правильно, поэтому можете перейти к подготовке новой группы ресурсов в Azure. Для этого выполните следующую команду: 
```
terraform apply terraformscripts
```
Если теперь взглянуть на портал Azure, то вы должны увидеть новую пустую группу ресурсов с именем "terraformtest". В следующем разделе показано, как добавить виртуальную машину и всю вспомогательную инфраструктуру для виртуальной машины в эту группу ресурсов.

## <a name="provisioning-ubuntu-vm-with-terraform"></a>Подготовка виртуальной машины Ubuntu с помощью Terraform
Давайте расширим сценарий Terraform, который мы создали выше, добавив в него сведения, необходимые для подготовки виртуальной машины под управлением Ubuntu. Список ресурсов, которые будут подготовлены в следующих разделах: сеть с одной подсетью, сетевая карта, учетная запись хранения с контейнером хранилища, общедоступный IP-адрес и виртуальная машина, использующая все выше перечисленные ресурсы. Подробная документация по каждому из ресурсов Azure Terraform доступна в [документации Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

Для удобства также предоставляется полная версия [сценария подготовки](#complete-terraform-script).

### <a name="extending-the-terraform-script"></a>Расширение сценария Terraform
Вы можете расширить ранее созданный сценарий, используя следующие ресурсы: 
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
Приведенный выше сценарий создает виртуальную сеть и подсеть внутри этой виртуальной сети. Обратите внимание на ссылку на уже созданную группу ресурсов ("${azurerm_resource_group.helloterraform.name}"), которая есть в определениях виртуальной сети и подсети.

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
Фрагменты сценария, приведенные выше, создают общедоступный IP-адрес и сетевой интерфейс, который использует созданный общедоступный IP-адрес. Обратите внимание на ссылки на subnet_id и public_ip_address_id. Terraform обладает встроенной системой аналитики, позволяющей понять, что сетевой интерфейс имеет зависимость от ресурсов, которые должны быть созданы до создания сетевого интерфейса.

~~~~
# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
Здесь была создана учетная запись хранения и определен контейнер хранилища в этой учетной записи хранения. Это то место, где хранятся виртуальные жесткие диски для создаваемой виртуальной машины.

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
Наконец, приведенный выше фрагмент создает виртуальную машину, которая использует все ресурсы, которые мы уже подготовили: учетную запись хранения и контейнер для виртуального жесткого диска (VHD), сетевой интерфейс с указанными общедоступным IP-адресом и подсетью, а также созданную вами группу ресурсов. Обратите внимание на свойство vm_size, в котором сценарий указывает номер SKU Azure (A0).

### <a name="executing-the-script"></a>Выполнение сценария
Когда полный сценарий сохранен, перейдите в консоль или командную строку и введите следующую команду:
```
terraform apply terraformscripts
```
Через некоторое время вы должны увидеть ресурсы, включая виртуальную машину, которые отобразятся в группе ресурсов "terraformtest" на портале Azure.

## <a name="complete-terraform-script"></a>Полный сценарий Terraform

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

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
Вы создали базовую инфраструктуру в Azure с помощью Terraform. Более сложные сценарии, включая примеры использования подсистемы балансировки нагрузки и масштабируемых наборов виртуальных машин, доступны в многочисленных [примерах Terraform для Azure](https://github.com/hashicorp/terraform/tree/master/examples). В [документации Terraform](https://www.terraform.io/docs/providers/azurerm/index.html) содержится полный актуальный список поддерживаемых поставщиков Azure.

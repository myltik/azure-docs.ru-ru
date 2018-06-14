---
title: Создание кластера виртуальных машин с помощью Terraform и HCL
description: Применение Terraform и настраиваемого языка шаблонов HCL для создания в Azure кластера виртуальных машин Linux с подсистемой балансировки нагрузки
keywords: terraform, devops, виртуальная машина, сеть, модули
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2017
ms.locfileid: "24518806"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Создание кластера виртуальных машин с помощью Terraform и HCL

В этом руководстве показано, как создать небольшой вычислительный кластер с помощью языка [Hashicorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). Конфигурация создает подсистему балансировки нагрузки, две виртуальные машины Linux в [группе доступности](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) и все необходимые сетевые ресурсы.

Изучив это руководство, вы:

> [!div class="checklist"]
> * Настройка аутентификации Azure
> * Создание файла конфигурации Terraform
> * Инициализация Terraform
> * Создание плана выполнения Terraform
> * Применение плана выполнения Terraform

## <a name="1-set-up-azure-authentication"></a>1. Настройка аутентификации Azure

> [!NOTE]
> Если вы [используете переменные среды Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) или выполняете это руководство в [Azure Cloud Shell](terraform-cloud-shell.md), пропустите этот раздел.

В этом разделе вы создадите субъект-службу Azure и два файла конфигурации Terraform с учетными данными из субъекта безопасности.

1. Настройте [субъект-службу Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure), чтобы предоставить Terraform возможность подготовить ресурсы в Azure. При создании субъекта запишите следующие значения: идентификатор подписки, клиент, идентификатор приложения и пароль.

2. Откройте окно командной строки.

3. Создайте пустой каталог для хранения файлов Terraform.

4. Создайте новый файл, содержащий объявления переменных. Присвойте этому файлу любое удобное имя и расширение `.tf`.

5. Скопируйте в файл объявления переменных следующий код:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Создайте новый файл, содержащий значения для переменных Terraform. Файл переменных Terraform чаще всего получает имя `terraform.tfvars`, поскольку Terraform автоматически загружает все файлы с именем `terraform.tfvars` (или с любым именем формата `*.auto.tfvars`), присутствующие в текущем каталоге. 

7. Скопируйте в файл переменных следующий код: Не забудьте правильно заменить строки заполнителей. Замените `subscription_id` идентификатором подписки Azure, который вы указали при выполнении `az account set`. Вместо `tenant_id` укажите значение `tenant`, полученное от `az ad sp create-for-rbac`. Вместо `client_id` укажите значение `appId`, полученное от `az ad sp create-for-rbac`. Вместо `client_secret` укажите значение `password`, полученное от `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Создание файла конфигурации Terraform

В этом разделе вы создадите файл с определениями ресурсов для инфраструктуры.

1. Создайте файл с именем `main.tf`. 

2. Скопируйте в новый файл `main.tf` следующие примеры определений ресурсов: 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
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

## <a name="3-initialize-terraform"></a>3. Инициализация Terraform 

Команда [terraform init](https://www.terraform.io/docs/commands/init.html) позволяет инициализировать каталог, содержащий файлы конфигурации Terraform, которые вы создали в предыдущих разделах. Команду `terraform init` нужно выполнять каждый раз после изменения конфигурации Terraform. 

> [!TIP]
> Команда `terraform init` является идемпотентной, то есть может выполняться несколько раз, возвращая одинаковые результаты. Таким образом, если в вашей среде применяются методы совместной работы и у вас есть основания полагать, что файлы конфигурации могли измениться, вызов команды `terraform init` перед выполнением или применением плана всегда будет полезным и безопасным.

Чтобы инициализировать Terraform, выполните следующую команду:

  ```cmd
  terraform init
  ```

  ![Инициализация Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Создание плана выполнения Terraform

Команда [terraform plan](https://www.terraform.io/docs/commands/plan.html) создает план выполнения. Чтобы создать план выполнения, Terraform анализирует и обобщает все файлы `.tf` в текущем каталоге. 

Если в вашей среде применяются методы совместной работы и конфигурация может измениться с момента создания плана выполнения до момента применения плана выполнения, используйте команду [terraform plan с параметром -out](https://www.terraform.io/docs/commands/plan.html#out-path), чтобы сохранить план выполнения в файл. Если же в вашей среде нет других администраторов, параметр `-out` можно опустить.

Если для файла переменных Terraform вы указали имя, отличное от `terraform.tfvars` и не соответствующее формату `*.auto.tfvars`, то имя этого файла нужно указать явным образом [в параметре -var команды terraform plan](https://www.terraform.io/docs/commands/plan.html#var-file-foo), когда вы выполняете команду `terraform plan`.

При обработке команды `terraform plan` Terraform выполняет обновление и определяет, какие нужны действия для достижения желаемого состояния, указанного в файлах конфигурации.

Если вы не хотите сохранять план выполнения, выполните следующую команду:

  ```cmd
  terraform plan
  ```

Если план выполнения нужно сохранить, выполните следующую команду (заменив заполнитель &lt;path> на правильный путь для выходных данных):

  ```cmd
  terraform plan -out=<path>
  ```

![Создание плана выполнения Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Применение плана выполнения Terraform

Последним действием в этом руководстве будет команда [terraform apply](https://www.terraform.io/docs/commands/apply.html), которая применяет набор действий, созданных командой `terraform plan`.

Если вы хотите применить последний созданный план выполнения, выполните такую команду:

  ```cmd
  terraform apply
  ```

Если вы хотите применить план выполнения, сохраненный ранее в файл, выполните следующую команду (заменив заполнитель &lt;path> фактическим значением пути, где расположен сохраненный план выполнения):

  ```cmd
  terraform apply <path>
  ```

![Применение плана выполнения Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Дополнительная информация

- Просмотрите список [модулей Azure Terraform](https://registry.terraform.io/modules/Azure).
- Создайте [масштабируемый набор виртуальных машин с помощью Terraform](terraform-create-vm-scaleset-network-disks-hcl.md).
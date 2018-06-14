---
title: Использование модулей Terraform для создания кластера виртуальных машин в Azure
description: Узнайте, как использовать модули Terraform для создания кластера виртуальных машин в Azure
keywords: terraform, devops, виртуальная машина, сеть, модули
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: e33aef252413eeb243b03543f171d5f1e2385b48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29952223"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Создание кластера виртуальных машин с помощью Terraform с использованием реестра модулей

В этой статье описывается создание небольшого кластера виртуальных машин с помощью [модуля вычислений Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) Terraform. Из этого руководства вы узнали, как выполнять такие задачи: 

> [!div class="checklist"]
> * настройка аутентификации в Azure;
> * создание шаблона Terraform;
> * Визуализация изменений с планом
> * применение конфигурации для создания кластера виртуальных машин.

Дополнительные сведения о Terraform см. в [документации по Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Настройка аутентификации в Azure

> [!TIP]
> При [использовании переменной среды Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) или выполнении этого руководства в [Azure Cloud Shell](/azure/cloud-shell/overview) пропустите этот шаг.

 Просмотрите статью об [установке Terraform и настройке доступа к Azure](/azure/virtual-machines/linux/terraform-install-configure), чтобы создать субъект-службу Azure. Используйте этот субъект-службу для заполнения нового файла `azureProviderAndCreds.tf` в пустом каталоге с помощью следующего кода:

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Создание шаблона

Создайте шаблон Terraform с именем `main.tf` со следующим кодом:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Выполните `terraform init` в каталоге конфигурации. При использовании версии Terraform не менее 0.10.6 будут показаны следующие выходные данные:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Визуализация изменений с планом

Выполните команду `terraform plan` для предварительного просмотра инфраструктуры виртуальных машин, созданной шаблоном.

![План Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Создание виртуальных машин с применением

Запустите `terraform apply` для подготовки виртуальных машин в Azure.

![Применение Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Дополнительная информация

- Просмотрите список [модулей Azure Terraform](https://registry.terraform.io/modules/Azure).
- Создайте [масштабируемый набор виртуальных машин с помощью Terraform](terraform-create-vm-scaleset-network-disks-hcl.md).

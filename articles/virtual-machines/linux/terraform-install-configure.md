---
title: Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure | Документация Майкрософт
description: Узнайте, как установить и настроить Terraform для создания ресурсов Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399174"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure
 
Terraform предоставляет простой способ определить, просмотреть и развернуть облачную инфраструктуру, используя [простой настраиваемый язык шаблонов](https://www.terraform.io/docs/configuration/syntax.html). В этой статье описываются шаги, необходимые для подготовки ресурсов Azure с помощью Terraform. 

> [!TIP]
Дополнительные сведения об использовании Terraform с Azure см. в [центре документации по Terraform](/azure/terraform). Средство Terraform установлено в [Cloud Shell](/azure/terraform/terraform-cloud-shell) по умолчанию. Используя Cloud Shell, можно пропустить некоторые части этого документа (а именно установку и настройку).

## <a name="install-terraform"></a>Установка Terraform

Чтобы установить Terraform, [скачайте](https://www.terraform.io/downloads.html) пакет, соответствующий вашей операционной системе, в отдельный каталог установки. Этот пакет содержит один исполняемый файл, для которого также необходимо задать глобальный путь. Инструкции по настройке пути в операционных системах Linux и Mac можно найти на [этой веб-странице](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Инструкции по настройке пути в Windows можно найти на [этой веб-странице](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Проверьте конфигурацию пути, выполнив команду `terraform`. В качестве выходных данных должен отобразиться список доступных параметров Terraform.

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Настройка доступа Terraform в Azure

Настройте [субъект-службу Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) для подготовки ресурсов в Azure с помощью Terraform. Субъект-служба предоставляет сценарии Terraform, используя учетные данные, для подготовки ресурсов в подписке Azure.

Существует несколько способов создания приложения Azure AD и субъекта-службы Azure AD. Самым простым и быстрым способом на сегодняшний день является установка с помощью интерфейса командной строки Azure CLI 2.0, который [можно скачать и установить на Windows, Mac или Linux](/cli/azure/install-azure-cli).

Войдите для администрирования подписки Azure, выполнив следующую команду:

   `az login`

Если у вас имеется несколько подписок Azure, то команда `az login` возвращает сведения о них. Настройте переменную среды `SUBSCRIPTION_ID`, чтобы она хранила значение возвращаемого поля `id` из подписки, которую необходимо использовать. 

Укажите подписку, которую необходимо использовать для этого сеанса.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Опросите учетную запись, чтобы получить значения идентификатора подписки и идентификатора клиента.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Затем создайте отдельные учетные данные для Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Вы получите значения для параметров appId, password, sp_name и tenant. Запишите значения параметров appId и password.

Чтобы проверить учетные данные, откройте новую оболочку и выполните следующую команду, используя возвращаемые значения для sp_name, а также используйте пароль и клиент:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Настройка переменных среды Terraform

Настройте Terraform для использования идентификатора арендатора, подписки и клиента, а также секрета клиента из субъекта-службы при создании ресурсов Azure. Можно также настроить среду, если используется другое облако Azure, а не с общедоступное облако Azure. Задайте следующие переменные среды, которые используются автоматически в [модулях Azure Terraform](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Вы можете использовать этот образец сценария оболочки для установки этих переменных:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Запуск образца сценария

Создайте файл `test.tf` в пустом каталоге и вставьте следующий сценарий. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Сохраните файл и выполните команду `terraform init`. Эта команда скачивает модули Azure, необходимые для создания группы ресурсов Azure. Вы увидите такой результат:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Просмотрите сценарий с помощью команды `terraform plan`, а затем создайте группу ресурсов `testResouceGroup` с помощью команды `terraform apply`:

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Дополнительная информация

Вы установили Terraform и настроили учетные данные Azure, так что вы можете начать развертывание инфраструктуры в свою подписку Azure. Затем вы проверили установку, создав пустую группу ресурсов Azure.

> [!div class="nextstepaction"]
> [Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform](terraform-create-complete-vm.md)


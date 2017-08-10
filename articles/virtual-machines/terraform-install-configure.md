---
title: "Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure | Документация Майкрософт"
description: "Узнайте, как установить и настроить Terraform для создания ресурсов Azure"
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
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 1f26bccf279ebb61fbf77767186d0435e4f4ba40
ms.contentlocale: ru-ru
ms.lasthandoff: 07/24/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure 
В этой статье описано, как установить и настроить Terraform для подготовки ресурсов, таких как виртуальные машины, в Azure. Вы узнаете, как создать и использовать учетные данные Azure, необходимые Terraform для подготовки облачных ресурсов в безопасном режиме.

Terraform от компании HashiCorp предоставляет простой способ определить и развернуть облачную инфраструктуру, используя настраиваемый язык шаблонов HCL. Этот настраиваемый язык [удобен для написания и прост для понимания](terraform-create-complete-vm.md). Кроме того, с помощью команды `terraform plan` вы можете визуализировать изменения, внесенные в инфраструктуру, прежде чем они будут зафиксированы. Чтобы приступить к использованию Terraform с Azure, сделайте следующее.

## <a name="install-terraform"></a>Установка Terraform
Чтобы установить Terraform, [скачайте](https://www.terraform.io/downloads.html) пакет, соответствующий вашей операционной системе, в отдельный каталог установки. Этот пакет содержит один исполняемый файл, для которого также необходимо задать глобальный путь. Инструкции по настройке пути в операционных системах Linux и Mac можно найти на [этой веб-странице](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Инструкции по настройке пути в Windows можно найти на [этой веб-странице](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). Проверьте правильность установки, выполнив команду `terraform`. В качестве выходных данных должен отобразиться список доступных параметров Terraform.

Затем необходимо разрешить Terraform доступ к вашей подписке Azure для выполнения подготовки инфраструктуры.

## <a name="set-up-terraform-access-to-azure"></a>Настройка доступа Terraform в Azure
Чтобы позволить Terraform предоставлять ресурсы в Azure, вам необходимо создать два объекта в Azure Active Directory (Azure AD): приложение Azure AD и субъект-службы Azure AD. Затем идентификаторы этих сущностей используются в сценариях Terraform. Субъект-служба — это локальный экземпляр глобального приложения Azure AD. Субъект-службы позволяет осуществлять детальный контроль локального доступа к глобальным ресурсам.

Существует несколько способов создания приложения Azure AD и субъекта-службы Azure AD. Самым простым и быстрым способом на сегодняшний день является установка с помощью интерфейса командной строки Azure CLI 2.0, который [можно скачать и установить на Windows, Mac или Linux](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Для создания необходимой инфраструктуры безопасности можно также воспользоваться PowerShell или Azure CLI 1.0. Ниже приведены инструкции по настройке Terraform для Azure с применением всех этих подходов.

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Использование Azure CLI 2.0 (для пользователей Windows, Mac и Linux) 
После скачивания и установки [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) войдите в систему для администрирования своей подписки Azure, выполнив следующую команду:

```
az login
```

>[!NOTE]
>Если вы используете облако Azure для Китая, Германии или государственных организаций, то сначала необходимо настроить Azure CLI для работы с соответствующим облаком. Для этого можно выполнить приведенную ниже команду.

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Если у вас имеется несколько подписок Azure, то команда `az login` возвращает сведения о них. Настройте переменную среды `SUBSCRIPTION_ID`, чтобы она хранила значение возвращаемого поля `id` из подписки, которую необходимо использовать. 

Укажите подписку, которую необходимо использовать для этого сеанса.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

Опросите учетную запись, чтобы получить значения идентификатора подписки и идентификатора клиента.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Затем создайте отдельные учетные данные для Terraform.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Вы получите значения для параметров appId, password, sp_name и tenant. Запишите значения параметров appId и password.

Чтобы подтвердить свои учетные данные (субъект-службу), откройте новую оболочку и выполните следующие команды. Замените полученные значения для параметров sp_name, password и tenant:

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>Использование PowerShell (для пользователей Windows) 
Если для записи и выполнения сценариев Terraform нужно использовать компьютер под управлением Windows и необходимо выполнять настройку с помощью PowerShell, настройте компьютер, используя соответствующие инструменты PowerShell. 

1. Установите инструменты PowerShell, выполнив шаги в статье [Установка и настройка Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). 

2. Скачайте и выполните сценарий [azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) из консоли PowerShell.

3. Чтобы запустить сценарий azure-setup.ps1, скачайте его и выполните из консоли PowerShell команду `./azure-setup.ps1 setup`. Затем войдите в свою подписку Azure с правами администратора.

4. При появлении запроса укажите имя приложения (произвольная строка, обязательно). При необходимости при появлении запроса введите надежный пароль. Если не указать пароль, то надежный пароль будет создан автоматически с помощью библиотек безопасности .NET.

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Использование Azure CLI 1.0 (для пользователей Linux или Mac)
Чтобы приступить к работе с Terraform на компьютерах Linux или Mac, используя Azure CLI 1.0, установите на компьютере соответствующие библиотеки.  

1. Для этого установите инструменты кроссплатформенного интерфейса командной строки Azure, выполнив действия в статье [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

2. Скачайте и установите процессор JSON, следуя инструкциям [здесь](https://stedolan.github.io/jq/download/).

3. Скачайте и выполните из консоли сценарий Bash [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh).

4. Чтобы запустить сценарий azure-setup.sh, скачайте его и выполните команду `./azure-setup setup` из консоли. Затем войдите в свою подписку Azure с правами администратора.
 
5. При появлении запроса укажите имя приложения (произвольная строка, обязательно). При необходимости при появлении запроса введите надежный пароль. Если не указать пароль, то надежный пароль будет создан автоматически с помощью библиотек безопасности .NET.

Все приведенные выше сценарии создают приложение Azure AD и субъект-службу. Субъект-служба получает доступ к подписке на уровне участника или владельца. Из-за высокого уровня предоставляемого доступа следует всегда защищать сведения о безопасности, которые создаются этими сценариями. Запишите все четыре блока сведений о безопасности, предоставленные этими скриптами: appId, password, subscription_id и tenant_id.

## <a name="set-environment-variables"></a>Настройка переменных среды
После создания и настройки субъекта-службы Azure AD необходимо разрешить Terraform использовать значения кода клиента, идентификатора подписки, идентификатора клиента и секрета клиента. Вы можете сделать это, вставив эти значения в сценарии Terraform, как описано в статье [Создание базовой инфраструктуры в Azure с помощью Terraform](terraform-create-complete-vm.md). Кроме того, можно задать следующие переменные среды (тем самым избежав случайной записи своих учетных данных или предоставления к ним общего доступа):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Вы можете использовать этот образец сценария оболочки для установки этих переменных:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

Кроме того, при использовании Terraform с облаком Azure для Китая, Azure для государственных организаций или Azure для Германии необходимо правильно задать переменную среды.

## <a name="next-steps"></a>Дальнейшие действия
Вы установили Terraform и настроили учетные данные Azure, так что вы можете начать развертывание инфраструктуры в свою подписку Azure. Теперь вы можете перейти к [созданию инфраструктуры с помощью Terraform](terraform-create-complete-vm.md).


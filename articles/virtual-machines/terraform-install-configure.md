---
title: "Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure | Документация Майкрософт"
description: "Узнайте, как установить и настроить Terraform для создания ресурсов Azure."
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
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure 
В этой статье описано, как установить и настроить Terraform для подготовки ресурсов, таких как виртуальные машины, в Azure. Вы узнаете, как создать и использовать учетные данные Azure, необходимые Terraform для подготовки облачных ресурсов в безопасном режиме.

Terraform от компании HashiCorp предоставляет простой способ определить и развернуть облачную инфраструктуру, используя настраиваемый язык шаблонов HCL. Этот настраиваемый язык [удобен для написания и прост для понимания](terraform-create-complete-vm.md). Кроме того, с помощью команды "terraform plan" приложение Terraform позволяет визуализировать изменения, внесенные в инфраструктуру, прежде чем они будут зафиксированы. Выполните описанные ниже действия, чтобы приступить к использованию Terraform с Azure.

## <a name="installing-terraform"></a>Установка Terraform
Чтобы установить Terraform, [скачайте](https://www.terraform.io/downloads.html) пакет, соответствующий вашей операционной системе, в отдельный каталог установки. Этот пакет содержит один исполняемый файл, для которого также необходимо задать глобальный путь. Инструкции по настройке пути в операционных системах Mac и Linux можно найти на [этой странице](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux), а [эта страница](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) содержит инструкции по настройке пути в Windows. Проверьте правильность установки, выполнив команду "terraform". В качестве выходных данных должен отобразиться список доступных параметров Terraform.

Затем необходимо разрешить Terraform доступ к вашей подписке Azure для выполнения подготовки инфраструктуры.

## <a name="setting-up-terraform-access-to-azure"></a>Настройка доступа Terraform в Azure
Чтобы приложение Terraform могло подготавливать ресурсы в Azure, необходимо создать в Azure Active Directory (AAD) две сущности — приложение AAD и субъект-службу AAD. Затем идентификаторы этих сущностей используются в сценариях Terraform. Субъект-служба — это локальный экземпляр глобального приложения AAD. Наличие субъекта-службы позволяет осуществлять детальный контроль локального доступа к глобальным ресурсам.

Существует несколько способов создания приложения AAD и субъекта-службы AAD. Самым простым и быстрым способом на сегодняшний день является установка с помощью интерфейса командной строки Azure CLI 2.0, который [можно скачать и установить в ОС Windows, Mac или Linux](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Для создания необходимой инфраструктуры безопасности можно также воспользоваться Powershell или Azure CLI 1.0. Ниже приведены инструкции по настройке Terraform для Azure с применением всех этих подходов.

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>Для пользователей Windows, Mac и Linux, использующих Azure CLI 2.0
После скачивания и установки [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), войдите в систему для администрирования своей подписки Azure, выполнив следующую команду:

```
az login
```

>[!NOTE]
>Если вы используете облако Azure для Китая, Германии или государственных организаций, то сначала необходимо настроить Azure CLI для работы с соответствующим облаком. Для этого можно выполнить следующую команду:

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

В выходных данных будут указаны значения client_id, client_secret (пароль), sp_name и tenant. Запишите значения **client_id** и **client_secret**.

Чтобы подтвердить свои учетные данные (субъект-службу), откройте новую оболочку и выполните следующие команды, заменив значения параметров **sp_name**, **client_secret** и **tenant** собственными:

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>Для пользователей Windows, использующих Powershell
Если для записи и выполнения сценариев Terraform вы используете компьютер под управлением Windows и предпочитаете выполнять настройку с помощью Powershell, то необходимо настроить компьютер, используя соответствующие инструменты Powershell. Для этого (1) [установите инструменты Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) и (2) скачайте и выполните [сценарий azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) из консоли Powershell. Чтобы запустить сценарий azure-setup.ps1, скачайте его, выполните из консоли Powershell команду "./azure-setup.ps1 setup" и войдите в свою подписку Azure с правами администратора. Затем при появлении запроса введите (обязательно) имя приложения в виде произвольной строки и (необязательно) укажите надежный пароль. Если не указать пароль, то надежный пароль будет создан автоматически с помощью библиотек безопасности .Net.

### <a name="linuxmac-users-using-azure-cli-10"></a>Для пользователей Linux и Mac, использующих Azure CLI 1.0
Чтобы приступить к работе с Terraform на компьютерах Linux или Mac, используя Azure CLI 1.0, необходимо убедиться, что на компьютере установлены соответствующие библиотеки. Для этого (1) [установите инструменты кроссплатформенного интерфейса командной строки (xPlat CLI) Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), (2) [скачайте и установите jq](https://stedolan.github.io/jq/download/), процессор JSON, а затем (3) скачайте и выполните из консоли сценарий Bash [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh). Чтобы запустить сценарий azure-setup.sh, скачайте его, выполните из консоли команду "./azure-setup setup" и войдите в свою подписку Azure с правами администратора. Затем при появлении запроса введите (обязательно) имя приложения в виде произвольной строки и (необязательно) укажите надежный пароль. Если не указать пароль, то надежный пароль будет создан автоматически с помощью библиотек безопасности .Net.

Все приведенные выше сценарии создают приложение AAD и субъект-службу, предоставляя субъекту-службе доступ к подписке на уровне "Участник" или "Владелец". Из-за высокого уровня предоставляемого доступа следует всегда защищать сведения о безопасности, которые создаются этими сценариями. Запишите все четыре части сведений о безопасности, предоставленные этими сценариями: client_id, client_secret, subscription_id и tenant_id. 

## <a name="setting-environment-variables"></a>Настройка переменных среды
После создания и настройки субъекта-службы AAD необходимо разрешить Terraform использовать значения кода клиента, идентификатора подписки, идентификатора клиента и секрета клиента. Это можно сделать, внедрив данные значения в сценарии Terraform (как описано в [следующем разделе](terraform-create-complete-vm.md)). Кроме того, можно задать следующие переменные среды (тем самым избежав случайной записи своих учетных данных или предоставления к ним общего доступа):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Ниже приведен пример сценария оболочки, который можно использовать для задания этих переменных:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

Кроме того, при использовании Terraform с облаком Azure для государственных организаций, Azure для Германии или Azure для Китая необходимо правильно задать переменную среды.

## <a name="next-steps"></a>Дальнейшие действия
Вы установили Terraform и настроили учетные данные Azure, чтобы начать развертывание инфраструктуры в свою подписку Azure. Теперь вы можете перейти к [созданию инфраструктуры с помощью Terraform](terraform-create-complete-vm.md).

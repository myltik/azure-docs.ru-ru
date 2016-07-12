<properties
	pageTitle="Разные способы создания виртуальной машины Linux | Microsoft Azure"
	description="В этой статье содержатся различные способы создания виртуальной машины Linux в Azure, а также ссылки на инструменты и руководства по каждому из этих способов."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Различные способы создания виртуальной машины Linux с помощью Resource Manager

Azure поддерживает несколько способов создания виртуальных машин с помощью модели развертывания Resource Manager для различных пользователей и целей. В этой статье описаны различия и компоненты, которые можно выбрать при создании виртуальной машины (VM) Linux.

## Инфраструктура CLI Azure 

Интерфейс командной строки Azure доступен на разных платформах с использованием пакета npm, пакетов, предоставленных для дистрибутивов, или контейнера Docker. Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md). В приведенных ниже руководствах содержатся примеры использования интерфейса командной строки Azure. В каждой из этих статей подробно описана соответствующая команда быстрого запуска интерфейса командной строки.

* [Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [Создание виртуальной машины Linux с нуля с помощью Azure CLI](virtual-machines-linux-create-cli-complete.md)

* [Добавление диска к виртуальной машине Linux](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Портал Azure

Графический пользовательский интерфейс [портала Azure](https://portal.azure.com) — это простой способ опробовать виртуальную машину, особенно если вы только начинаете работать с Azure, так как устанавливать что-либо в системе не требуется. Для создания виртуальной машины используйте портал Azure:

* [Создание виртуальной машины Linux в Azure с помощью портала](virtual-machines-linux-quick-create-portal.md)
* [Подключение диска данных к виртуальной машине Linux на портале Azure](virtual-machines-linux-attach-disk-portal.md)

## Операционная система и варианты образов
При создании виртуальной машины можно выбрать образ для операционной системы, которую необходимо запустить. Azure и партнеры предлагают множество образов, некоторые из которых содержат предустановленные приложения и средства. Можно загрузить один из собственных образов (см. ниже).

### Образы Azure
Чтобы просмотреть список доступных издателей, дистрибутивов и сборок, можно использовать команду CLI `azure vm image`.

Отображение списка доступных издателей:

```bash
azure vm image list-publishers --location WestUS
```

Отображение списка доступных продуктов (предложений) нужного издателя:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Отображение списка доступных номеров SKU (для дистрибутивов) требуемого предложения:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Отображение списка всех доступных образов для определенного выпуска:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

В статье [Просмотр и выбор образов виртуальных машин Linux в Azure с помощью интерфейса командной строки или оболочки PowerShell](virtual-machines-linux-cli-ps-findimage.md) содержится больше примеров просмотра и использования доступных образов.

С командами `azure vm quick-create` и `azure vm create` также можно использовать некоторые псевдонимы для быстрого доступа к самым распространенным дистрибутивам и их последним выпускам. Так вам не требуется указывать издателя, предложение, номер SKU и версию каждый раз при создании виртуальной машины:

| Alias | Издатель | ПРЕДЛОЖЕНИЕ | SKU | Version (версия) |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | CentOS | 7,2 | последних |
| CoreOS | CoreOS | CoreOS | Stable | последних |
| Debian | credativ | Debian | 8 | последних |
| openSUSE | SUSE | openSUSE | 13\.2 | последних |
| RHEL | Redhat | RHEL | 7,2 | последних |
| SLES | SLES | SLES | 12-SP1 | последних |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | последних |

### Использование своего образа

Если вам требуются особые настройки, используйте образ на основе имеющейся виртуальной машины Azure. Для этого *запишите* образ такой виртуальной машины или отправьте свой образ, сохраненный на виртуальном жестком диске. Дополнительные сведения о поддерживаемых дистрибутивах и использовании собственных образов см. в следующих статьях.

* [Linux on Azure-Endorsed Distributions (Linux на дистрибутивах, рекомендованных для Azure)](virtual-machines-linux-endorsed-distros.md)

* [Information for Non-Endorsed Distributions (Информация о нерекомендованных дистрибутивах)](virtual-machines-linux-create-upload-generic.md)

* [Запись образа виртуальной машины Linux для его использования в качестве шаблона Resource Manager](virtual-machines-linux-capture-image.md). Команды быстрого запуска:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Дальнейшие действия

* Попробуйте выполнить указания, приведенные в руководствах по созданию виртуальной машины Linux: с помощью [портала](virtual-machines-linux-quick-create-portal.md), [интерфейса командной строки](virtual-machines-linux-quick-create-cli.md) или [шаблона](virtual-machines-linux-cli-deploy-templates.md) Azure Resource Manager.

* Создав виртуальную машину Linux, вы сможете легко [добавить диск данных](virtual-machines-linux-add-disk.md).

* Способы быстрого [сброса пароля или SSH-ключей и управления пользователями](virtual-machines-linux-using-vmaccess-extension.md).

<!---HONumber=AcomDC_0706_2016-->
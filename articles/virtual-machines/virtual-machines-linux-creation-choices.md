<properties
	pageTitle="Разные способы создания виртуальной машины Linux | Microsoft Azure"
	description="Узнайте о различных способах создания виртуальных машин Linux в Azure, а также воспользуйтесь ссылками на инструменты и руководства по каждому из этих способов."
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
	ms.date="09/27/2016"
	ms.author="iainfou"/>

# Различные способы создания виртуальных машин Linux в Azure

Платформа Azure предоставляет гибкие решения по созданию виртуальных машин Linux. Здесь каждый пользователь найдет удобные для себя инструменты и рабочие процессы. В этой статье описаны разные решения и примеры создания виртуальных машин Linux.


## Инфраструктура CLI Azure 

Интерфейс командной строки Azure доступен на разных платформах с использованием пакета npm, пакетов, предоставленных для дистрибутивов, или контейнера Docker. Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md). В приведенных ниже руководствах содержатся примеры использования интерфейса командной строки Azure. В каждой из этих статей подробно описана соответствующая команда быстрого запуска интерфейса командной строки.

- [Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки](virtual-machines-linux-quick-create-cli.md)
	- Следующий пример демонстрирует создание виртуальной машины CoreOS при помощи открытого ключа с именем `azure_id_rsa.pub`.

	```bash
	azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
		--image-urn CoreOS
	```

- [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
	- В следующем примере виртуальная машина создается на основе шаблона, который хранится на GitHub.

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

- [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md).
	- Включает создание балансировщика нагрузки и нескольких виртуальных машин в группе доступности.

- [Добавление диска к виртуальной машине Linux](virtual-machines-linux-add-disk.md)
	- В следующем примере показано, как добавить диск объемом 5 ГБ к существующей виртуальной машине с именем `TestVM`.

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
		--size-in-GB 5
	```

## Портал Azure

С помощью [портала Azure](https://portal.azure.com) можно быстро создать виртуальную машину, так как установка компонентов в локальной системе не требуется. Для создания виртуальной машины используйте портал Azure:

- [Создание виртуальной машины Linux в Azure с помощью портала](virtual-machines-linux-quick-create-portal.md)
- [Подключение диска данных к виртуальной машине Linux на портале Azure](virtual-machines-linux-attach-disk-portal.md)


## Операционная система и варианты образов
При создании виртуальной машины можно выбрать образ для операционной системы, которую необходимо запустить. Azure и партнеры предлагают множество образов, некоторые из которых содержат предустановленные приложения и средства. Вы также можете передать один из созданных вами образов (см. [следующий раздел](#use-your-own-image)).

### Образы Azure
Чтобы просмотреть список доступных издателей, дистрибутивов и сборок, используйте команды интерфейса командной строки `azure vm image`.

Отображение списка доступных издателей:

```bash
azure vm image list-publishers --location WestUS
```

Отображение списка доступных продуктов (предложений) нужного издателя:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Отображение списка доступных номеров SKU (дистрибутивов) требуемого предложения:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Отображение списка всех доступных образов для определенного выпуска:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Дополнительные примеры просмотра и использования доступных образов см. статье [Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)](virtual-machines-linux-cli-ps-findimage.md).

С командами `azure vm quick-create` и `azure vm create` также можно использовать псевдонимы для быстрого доступа к самым распространенным дистрибутивам и их последним выпускам. Как правило, использовать псевдоним быстрее, чем указывать издателя, предложение, номер SKU и версию каждый раз при создании виртуальной машины.

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

Если вам требуются особые настройки, используйте образ на основе имеющейся виртуальной машины Azure. Для этого *запишите* образ такой виртуальной машины. Вы также можете отправить собственный образ, созданный на локальном диске. Дополнительные сведения о поддерживаемых дистрибутивах и использовании собственных образов см. в следующих статьях.

- [Linux on Azure-Endorsed Distributions (Linux на дистрибутивах, рекомендованных для Azure)](virtual-machines-linux-endorsed-distros.md)

- [Information for Non-Endorsed Distributions (Информация о нерекомендованных дистрибутивах)](virtual-machines-linux-create-upload-generic.md)

- [Как записать образ виртуальной машины Linux для его использования в качестве шаблона Resource Manager](virtual-machines-linux-capture-image.md).
	- Примеры команд для быстрой записи существующей виртуальной машины.

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Дальнейшие действия

- Создайте виртуальную машину Linux с помощью [портала](virtual-machines-linux-quick-create-portal.md), [интерфейса командной строки](virtual-machines-linux-quick-create-cli.md) или [шаблона Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md).

- После создания виртуальной машины Linux [добавьте диск данных](virtual-machines-linux-add-disk.md).

- Способы быстрого [сброса пароля или SSH-ключей и управления пользователями](virtual-machines-linux-using-vmaccess-extension.md).

<!---HONumber=AcomDC_0928_2016-->
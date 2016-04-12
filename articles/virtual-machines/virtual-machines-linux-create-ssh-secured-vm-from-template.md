<properties
	pageTitle="Создание защищенной виртуальной машины Linux с помощью шаблона Azure | Microsoft Azure"
	description="Создание в Azure защищенной виртуальной машины Linux с помощью шаблона Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# Создание защищенной виртуальной машины Linux с помощью шаблона Azure

Шаблоны позволяют создавать виртуальные машины в Azure с параметрами, которые требуется настроить во время запуска, такими как имена пользователей и имена узлов. В этой статье мы сосредоточимся на запуске виртуальной машины Ubuntu с помощью шаблона Azure, который создает группу безопасности сети (NSG) только с одним открытым портом (порт 22 для SSH) и требует наличия ключей SSH для входа в систему.

Шаблоны Azure Resource Manager — это JSON-файлы, которые можно использовать для простых одноразовых задач (таких, как запуск виртуальной машины Ubuntu, как в этой статье) или для создания сложных конфигураций для целых сред, например тестовых развертываний, рабочих развертываний или развертываний для разработки — от сети до ОС и развертывания стека приложений.

## Цель

- Создание в Azure виртуальной машины Linux, защищенной с помощью SSH, посредством шаблона Azure

## Предварительные требования

- Вам потребуется учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)), [файл открытого ключа SSH](virtual-machines-linux-mac-create-ssh-keys.md) и [интерфейс командной строки Azure (Azure CLI)](../xplat-cli-install.md), который необходимо перевести в режим ресурсов (введите `azure config mode arm`). Затем войдите в Azure с помощью интерфейса командной строки (введите `azure login`).

## Краткая сводка по командам

Для этого развертывания нужно ввести только одну команду, а также выбрать параметр, указывающий, где находится шаблон Azure Resource Manager. В этом разделе в качестве примера используется шаблон непосредственно из репозитория шаблонов быстрого запуска Azure на сайте Github. Ниже различные параметры просто указывают, где находится шаблон и файлы параметров.

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## Создание виртуальной машины Linux

В следующем примере кода показано, как вызвать `azure group create`, чтобы одновременно создать группу ресурсов и развернуть виртуальную машину Linux, защищенную посредством SSH, используя [этот шаблон Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Помните, что в данном примере необходимо использовать имена, уникальные для вашей среды. В этом примере `quicksecuretemplate` используется как имя группы ресурсов, `securelinux` — как имя виртуальной машины и `quicksecurelinux` — как имя поддомена.

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Подробные пошаговые инструкции

Вы можете создать новую группу ресурсов и развернуть виртуальную машину с помощью параметра `--template-uri` либо загрузить или создать шаблон локально и передать его с помощью параметра `--template-file` с путем к файлу шаблона в качестве аргумента. Azure CLI запрашивает параметры, необходимые для шаблона.

## Дальнейшие действия

После создания виртуальных машин Linux с помощью шаблонов вы можете узнать, какие другие платформы приложений доступны для использования с шаблонами. В [коллекции шаблонов](https://azure.microsoft.com/documentation/templates/) вы сможете найти платформы приложений для дальнейшего развертывания.

<!---HONumber=AcomDC_0406_2016-->
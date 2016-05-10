<properties
	pageTitle="Создание виртуальной машины Linux с помощью шаблона Azure | Microsoft Azure"
	description="Создание в Azure виртуальной машины Linux с помощью шаблона Azure Resource Manager."
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
	ms.date="04/29/2016"
	ms.author="v-livech"/>

# Создание виртуальной машины Linux с помощью шаблона Azure

Из этой статьи вы узнаете, как быстро развернуть в Azure виртуальную машину Linux с помощью шаблона Azure. Для работы вам потребуется учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)) и [интерфейс командной строки Azure](../xplat-cli-install.md) (войдите (`azure login`) в режиме Resource Manager (`azure config mode arm`)). Кроме того, вы можете быстро развернуть виртуальную машину Linux с помощью [портала Azure](virtual-machines-linux-quick-create-portal.md) или [интерфейса командной строки Azure](virtual-machines-linux-quick-create-cli.md).


## Краткий обзор команд

В примерах команд ниже замените значения между &lt; и &gt; значениями, соответствующими вашей среде.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Подробное пошаговое руководство

Шаблоны позволяют создавать виртуальные машины в Azure с параметрами, которые требуется настроить во время запуска, такими как имена пользователей и имена узлов. В этой статье мы сосредоточимся на запуске виртуальной машины Ubuntu с помощью шаблона Azure, который создает группу безопасности сети (NSG) только с одним открытым портом (порт 22 для SSH) и требует наличия ключей SSH для входа в систему.

Шаблоны Azure Resource Manager — это JSON-файлы, которые можно использовать для простых одноразовых задач (таких, как запуск виртуальной машины Ubuntu, как в этой статье) или для создания сложных конфигураций для целых сред, например тестовых развертываний, рабочих развертываний или развертываний для разработки — от сети до ОС и развертывания стека приложений.

## Создание виртуальной машины Linux

В следующем примере кода показано, как вызвать `azure group create`, чтобы одновременно создать группу ресурсов и развернуть виртуальную машину Linux, защищенную с помощью SSH, используя [этот шаблон Azure Resource Manager](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Помните, что в данном примере необходимо использовать имена, уникальные для вашей среды. В этом примере `quicksecuretemplate` используется как имя группы ресурсов, `securelinux` — как имя виртуальной машины и `quicksecurelinux` — как имя поддомена.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Выходные данные

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Вы можете создать новую группу ресурсов и развернуть виртуальную машину с помощью параметра `--template-uri` либо скачать или создать шаблон локально и передать его с помощью параметра `--template-file`, указав в качестве аргумента путь к файлу шаблона. Azure CLI запрашивает параметры, необходимые для шаблона.

## Дальнейшие действия

Теперь когда вы научились создавать виртуальные машины Linux с помощью шаблонов, рекомендуем изучить, какие еще платформы приложений можно развертывать с помощью шаблонов. В [коллекции шаблонов](https://azure.microsoft.com/documentation/templates/) вы сможете найти другие платформы приложений для развертывания.

<!---HONumber=AcomDC_0504_2016-->
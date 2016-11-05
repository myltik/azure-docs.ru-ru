---
title: Создание виртуальной машины Linux с помощью шаблона Azure | Microsoft Docs
description: Создание в Azure виртуальной машины Linux с помощью шаблона Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: v-livech

---
# Создание виртуальной машины Linux с помощью шаблона Azure
Из этой статьи вы узнаете, как быстро развернуть в Azure виртуальную машину Linux с помощью шаблона Azure. Для работы с этой статьей вам потребуется учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)) и [интерфейс командной строки Azure](../xplat-cli-install.md) с выполненным входом (`azure login`) в режиме Resource Manager (`azure config mode arm`). Кроме того, вы можете быстро развернуть виртуальную машину Linux с помощью [портала Azure](virtual-machines-linux-quick-create-portal.md) или [интерфейса командной строки Azure](virtual-machines-linux-quick-create-cli.md).

## Краткая сводка по командам
```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Подробное пошаговое руководство
Шаблоны позволяют создавать виртуальные машины в Azure с параметрами, которые можно настроить во время запуска, включая имена пользователей и имена узлов. В этой статье мы запустим шаблон Azure с использованием виртуальной машины Ubuntu и группы безопасности сети (NSG) с портом 22, открытым для SSH.

Шаблоны Azure Resource Manager — это JSON-файлы, которые можно использовать для простых задач, таких как однократный запуск виртуальной машины Ubuntu, как в нашем примере. Шаблоны Azure можно также использовать при создании сложных конфигураций Azure на уровне среды (для сред тестирования и разработки, а также для стеков развертывания в рабочих средах).

## Создание виртуальной машины Linux
В следующем примере кода показано, как вызвать `azure group create`, чтобы одновременно создать группу ресурсов и развернуть виртуальную машину Linux, защищенную с помощью SSH, используя [этот шаблон Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Помните, что в данном примере необходимо использовать имена, уникальные для вашей среды. В этом примере `quicksecuretemplate` используется как имя группы ресурсов, `securelinux` — как имя виртуальной машины и `quicksecurelinux` — как имя поддомена.

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

В этом примере виртуальная машина развертывается с помощью параметра `--template-uri`. Вы также можете скачать или создать шаблон локально, а затем передать его с помощью параметра `--template-file`, указав в качестве аргумента путь к файлу шаблона. Azure CLI запрашивает параметры, необходимые для шаблона.

## Дальнейшие действия
В [коллекции шаблонов](https://azure.microsoft.com/documentation/templates/) вы сможете найти другие платформы приложений для развертывания.

<!---HONumber=AcomDC_0824_2016-->
---
title: Создание виртуальной машины Linux с помощью шаблона Azure и Azure CLI 1.0 | Документация Майкрософт
description: Создание виртуальной машины Linux в Azure с помощью Azure CLI 1.0 и шаблона Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 620adcba9e136aa56e7a4867196ce5b9249b6072
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Как создать виртуальную машину Linux с помощью Azure CLI 1.0 и шаблона Azure Resource Manager
В этой статье показано, как быстро развернуть виртуальную машину Linux с помощью Azure CLI 1.0 и шаблона Azure Resource Manager. Для работы с этой статьей потребуется:

* Учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)).
* [Azure CLI 1.0](../../cli-install-nodejs.md) с выполненным входом (с помощью команды `azure login`).
* Интерфейс командной строки Azure *нужно* переключить в режим Azure Resource Manager `azure config mode arm`.

Вы также можете быстро развернуть шаблон виртуальной машины Linux с помощью [портала Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-command-summary) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](create-ssh-secured-vm-from-template.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="quick-command-summary"></a>Краткая сводка по командам
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство
Шаблоны позволяют создавать виртуальные машины в Azure с параметрами, которые можно настроить во время запуска, включая имена пользователей и имена узлов. В этой статье мы запустим шаблон Azure с использованием виртуальной машины Ubuntu и группы безопасности сети (NSG) с портом 22, открытым для SSH.

Шаблоны Azure Resource Manager — это JSON-файлы, которые можно использовать для простых задач, таких как однократный запуск виртуальной машины Ubuntu, как в нашем примере.  Шаблоны Azure можно также использовать при создании сложных конфигураций Azure на уровне среды (для сред тестирования и разработки, а также для стеков развертывания в рабочих средах).

## <a name="create-the-linux-vm"></a>Создание виртуальной машины Linux
В следующем примере кода показано, как вызвать `azure group create` , чтобы одновременно создать группу ресурсов и развернуть виртуальную машину Linux, защищенную с помощью SSH, используя [этот шаблон Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Помните, что в данном примере необходимо использовать имена, уникальные для вашей среды. В этом примере *myResourceGroup* используется как имя группы ресурсов и *myVM* — как имя виртуальной машины.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

В результате должен отобразиться следующий блок выходных данных.

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

В этом примере виртуальная машина развертывается с помощью параметра `--template-uri` .  Вы также можете скачать или создать шаблон локально, а затем передать его с помощью параметра `--template-file` , указав в качестве аргумента путь к файлу шаблона. Azure CLI запрашивает параметры, необходимые для шаблона.

## <a name="next-steps"></a>Дополнительная информация
В [коллекции шаблонов](https://azure.microsoft.com/documentation/templates/) вы сможете найти другие платформы приложений для развертывания.


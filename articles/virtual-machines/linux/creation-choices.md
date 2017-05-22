---
title: "Различные способы создания виртуальной машины Linux в Azure | Microsoft Azure"
description: "Узнайте о различных способах создания виртуальных машин Linux в Azure, а также воспользуйтесь ссылками на инструменты и руководства по каждому из этих способов."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756711abb014829971af126c5cb60c12e79c920e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Различные способы создания виртуальной машины Linux
Платформа Azure предоставляет гибкие решения по созданию виртуальных машин Linux. Здесь каждый пользователь найдет удобные для себя инструменты и рабочие процессы. В этой статье описаны разные решения и примеры создания виртуальных машин Linux, в том числе с помощью Azure CLI 2.0. Вы также можете изучить варианты создания, в том числе с помощью [Azure CLI 1.0](creation-choices-nodejs.md).

Интерфейс командной строки [Azure CLI 2.0](/cli/azure/install-az-cli2) доступен на разных платформах в виде пакета npm, включенных в дистрибутив пакетов или контейнера Docker. Установите оптимальную сборку для своей среды и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

* [Создание виртуальной машины Linux с помощью Azure CLI 2.0](quick-create-cli.md).
  
  * С помощью команды [az group create](/cli/azure/group#create) создайте группу ресурсов с именем *myResourceGroup*. 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * С помощью команды [az vm create](/cli/azure/vm#create) создайте виртуальную машину с именем *myVM*, используя последний образ *UbuntuLTS*, и сгенерируйте ключи SSH, если они уже существуют в *~/.ssh*:

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Создание виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md).
  
  * В следующем примере с помощью команды [az group deployment create](/cli/azure/group/deployment#create) создается виртуальная машина на основе шаблона, сохраненного в репозитории GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Создание виртуальной машины Linux и ее настройка с помощью файла cloud-init](tutorial-automate-vm-deployment.md).

* [Создание приложения с балансировкой нагрузки и высоким уровнем доступности на нескольких виртуальных машинах Linux](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Портал Azure
С помощью [портала Azure](https://portal.azure.com) можно быстро создать виртуальную машину, так как установка компонентов в локальной системе не требуется. Для создания виртуальной машины используйте портал Azure:

* [Создание виртуальной машины Linux в Azure с помощью портала](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>Операционная система и варианты образов
При создании виртуальной машины можно выбрать образ для операционной системы, которую необходимо запустить. Azure и партнеры предлагают множество образов, некоторые из которых содержат предустановленные приложения и средства. Вы также можете передать один из созданных вами образов (см. [следующий раздел](#use-your-own-image)).

### <a name="azure-images"></a>Образы Azure
Чтобы просмотреть список доступных издателей, дистрибутивов и сборок, используйте команды [az vm image](/cli/azure/vm/image).

Отображение списка доступных издателей:

```azurecli
az vm image list-publishers --location eastus
```

Отображение списка доступных продуктов (предложений) нужного издателя:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Отображение списка доступных номеров SKU (для дистрибутивов) требуемого предложения:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Отображение списка всех доступных образов для определенного выпуска:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Дополнительные примеры просмотра и использования доступных образов см. в статье [Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)](cli-ps-findimage.md).

Команда [az vm create](/cli/azure/vm#create) содержит псевдонимы, которые можно использовать для быстрого доступа к самым распространенным дистрибутивам и их последним выпускам. Как правило, использовать псевдоним быстрее, чем указывать издателя, предложение, номер SKU и версию каждый раз при создании виртуальной машины.

| Alias | Издатель | ПРЕДЛОЖЕНИЕ | SKU | Версия |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7,2 |последних |
| CoreOS |CoreOS |CoreOS |Stable |последняя |
| Debian |credativ |Debian |8 |последняя |
| openSUSE |SUSE |openSUSE |13.2 |последних |
| RHEL |Redhat |RHEL |7,2 |последних |
| SLES |SLES |SLES |12-SP1 |последних |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |последних |

### <a name="use-your-own-image"></a>Использование своего образа
Если вам требуются особые настройки, используйте образ на основе имеющейся виртуальной машины Azure. Для этого запишите образ такой виртуальной машины. Вы также можете отправить собственный образ, созданный на локальном диске. Дополнительные сведения о поддерживаемых дистрибутивах и использовании собственных образов см. в следующих статьях.

* [Linux on Azure-Endorsed Distributions (Linux на дистрибутивах, рекомендованных для Azure)](endorsed-distros.md)
* [Information for Non-Endorsed Distributions (Информация о нерекомендованных дистрибутивах)](create-upload-generic.md)
* [Создание образа на основе существующей виртуальной машины Azure](tutorial-custom-images.md).
  
  * Примеры команд для быстрого начала работы, с помощью которых можно создать образ на основе существующей виртуальной машины Azure:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>Дальнейшие действия
* Создайте виртуальную машину Linux с помощью [интерфейса командной строки](quick-create-cli.md), [портала](quick-create-portal.md) или [шаблона Azure Resource Manager](../windows/cli-deploy-templates.md).
* После создания виртуальной машины Linux [прочтите статью о дисках и хранилище Azure](tutorial-manage-disks.md).
* Способы быстрого [сброса пароля или SSH-ключей и управления пользователями](using-vmaccess-extension.md).


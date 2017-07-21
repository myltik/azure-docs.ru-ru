---
title: "Разные способы создания виртуальной машины Linux с помощью Azure CLI 1.0 | Документация Майкрософт"
description: "Узнайте о различных способах создания виртуальных машин Linux в Azure, а также воспользуйтесь ссылками на инструменты и руководства по каждому из этих способов."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 1eb90d44797d66f3e09811918ce5a7f4ad4287c6
ms.contentlocale: ru-ru
ms.lasthandoff: 07/14/2017

---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Различные способы создания виртуальных машин Linux в Azure
Платформа Azure предоставляет гибкие решения по созданию виртуальных машин Linux. Здесь каждый пользователь найдет удобные для себя инструменты и рабочие процессы. В этой статье описаны разные решения и примеры создания виртуальных машин Linux.

## <a name="azure-cli"></a>Интерфейс командной строки Azure
Виртуальные машины в Azure можно создавать с помощью одной из следующих версий интерфейса командной строки.

- Azure CLI 1.0 — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](../windows/creation-choices.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

Интерфейс командной строки Azure CLI 1.0 доступен на разных платформах с использованием пакета npm, предоставленных для дистрибутивов пакетов или контейнера Docker. Дополнительные сведения об [установке и настройке интерфейса командной строки Azure](../../cli-install-nodejs.md). В приведенных ниже руководствах содержатся примеры использования Azure CLI 1.0. В каждой из этих статей подробно описана соответствующая команда быстрого запуска интерфейса командной строки.

* [Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки](quick-create-cli-nodejs.md)
  
  * Следующий пример демонстрирует создание виртуальной машины CoreOS при помощи открытого ключа с именем *azure_id_rsa.pub*:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md)
  
  * В следующем примере виртуальная машина создается на основе шаблона, который хранится на сайте GitHub.
    
    ```azurecli
    azure group create --name myResourceGroup --location eastus 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete-nodejs.md)
  
  * Включает создание балансировщика нагрузки и нескольких виртуальных машин в группе доступности.
* [Добавление диска к виртуальной машине Linux](add-disk.md)
  
  * В следующем примере показано, как добавить диск объемом *5* ГБ к существующей виртуальной машине с именем *myVM*:
    
    ```azurecli
    azure vm disk attach-new \
        --resource-group myResourceGroup \
        --vm-name myVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Портал Azure
С помощью [портала Azure](https://portal.azure.com) можно быстро создать виртуальную машину, так как установка компонентов в локальной системе не требуется. Для создания виртуальной машины используйте портал Azure:

* [Создание виртуальной машины Linux в Azure с помощью портала](quick-create-portal.md) 

## <a name="operating-system-and-image-choices"></a>Операционная система и варианты образов
При создании виртуальной машины можно выбрать образ для операционной системы, которую необходимо запустить. Azure и партнеры предлагают множество образов, некоторые из которых содержат предустановленные приложения и средства. Вы также можете передать один из созданных вами образов (см. [следующий раздел](#use-your-own-image)).

### <a name="azure-images"></a>Образы Azure
Чтобы просмотреть список доступных издателей, дистрибутивов и сборок, используйте команды интерфейса командной строки `azure vm image` .

Отображение списка доступных издателей:

```azurecli
azure vm image list-publishers --location eastus
```

Отображение списка доступных продуктов (предложений) нужного издателя:

```azurecli
azure vm image list-offers --location eastus --publisher Canonical
```

Отображение списка доступных номеров SKU (дистрибутивов) требуемого предложения:

```azurecli
azure vm image list-skus --location eastus --publisher Canonical --offer UbuntuServer
```

Отображение списка всех доступных образов для определенного выпуска:

```azurecli
azure vm image list --location eastus --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

С командами `azure vm quick-create` и `azure vm create` также можно использовать псевдонимы для быстрого доступа к самым распространенным дистрибутивам и их последним выпускам. Как правило, использовать псевдоним быстрее, чем указывать издателя, предложение, номер SKU и версию каждый раз при создании виртуальной машины.

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
Если вам требуются особые настройки, используйте образ на основе имеющейся виртуальной машины Azure. Для этого *запишите* образ такой виртуальной машины. Вы также можете отправить собственный образ, созданный на локальном диске. Дополнительные сведения о поддерживаемых дистрибутивах и использовании собственных образов см. в следующих статьях.

* [Linux on Azure-Endorsed Distributions (Linux на дистрибутивах, рекомендованных для Azure)](endorsed-distros.md)
* [Information for Non-Endorsed Distributions (Информация о нерекомендованных дистрибутивах)](create-upload-generic.md)
* [Передача пользовательского образа диска и создание виртуальной машины Linux на его основе](upload-vhd.md)
* [Как записать образ виртуальной машины Linux для его использования в качестве шаблона Resource Manager](capture-image.md).
  
  * Примеры команд для быстрой записи существующей виртуальной машины.
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Дальнейшие действия
* Создайте виртуальную машину Linux с помощью [портала](quick-create-portal.md), [интерфейса командной строки](quick-create-cli.md) или [шаблона Azure Resource Manager](../windows/cli-deploy-templates.md).
* После создания виртуальной машины Linux [добавьте диск данных](add-disk.md).
* Способы быстрого [сброса пароля или SSH-ключей и управления пользователями](using-vmaccess-extension.md)



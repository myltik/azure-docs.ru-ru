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
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Различные способы создания виртуальной машины Linux, в том числе с использованием Azure CLI 2.0 (предварительная версия)
Платформа Azure предоставляет гибкие решения по созданию виртуальных машин Linux. Здесь каждый пользователь найдет удобные для себя инструменты и рабочие процессы. В этой статье описаны разные решения и примеры создания виртуальных машин Linux.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- Azure CLI 1.0 — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager
- [Azure CLI 2.0 (предварительная версия)](../xplat-cli-install.md) — интерфейс командной строки нового поколения для модели развертывания Resource Manager.

Azure CLI 2.0 (предварительная версия) доступен на разных платформах с использованием пакета npm, пакетов, предоставленных для дистрибутивов, или контейнера Docker. Вы должны войти с помощью **az login**.

В приведенных ниже руководствах содержатся примеры использования Azure CLI 2.0 (предварительная версия). В каждой из этих статей подробно описана соответствующая команда.

* [Создание виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В этом примере создается группа ресурсов с именем myResourceGroup. 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * В этом примере создается виртуальная машина в новой группе ресурсов с использованием последнего образа Debian и открытого ключа с именем `id_rsa.pub`.

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mydns \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

* [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В следующем примере виртуальная машина создается на основе шаблона, который хранится на сайте GitHub.
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Включает создание балансировщика нагрузки и нескольких виртуальных машин в группе доступности.

* [Добавление диска к виртуальной машине Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В следующем примере показано, как добавить диск объемом 5 ГБ к существующей виртуальной машине с именем `myVM`.
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Портал Azure
С помощью [портала Azure](https://portal.azure.com) можно быстро создать виртуальную машину, так как установка компонентов в локальной системе не требуется. Для создания виртуальной машины используйте портал Azure:

* [Создание виртуальной машины Linux в Azure с помощью портала](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Подключение диска данных к виртуальной машине Linux на портале Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Операционная система и варианты образов
При создании виртуальной машины можно выбрать образ для операционной системы, которую необходимо запустить. Azure и партнеры предлагают множество образов, некоторые из которых содержат предустановленные приложения и средства. Вы также можете передать один из созданных вами образов (см. [следующий раздел](#use-your-own-image)).

### <a name="azure-images"></a>Образы Azure
Чтобы просмотреть список доступных издателей, дистрибутивов и сборок, используйте команды интерфейса командной строки `az vm image` .

Отображение списка доступных издателей:

```azurecli
az vm image list-publishers -l WestUS
```

Отображение списка доступных продуктов (предложений) нужного издателя:

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

Отображение списка доступных номеров SKU (для дистрибутивов) требуемого предложения:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

Отображение списка всех доступных образов для определенного выпуска:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

Дополнительные примеры просмотра и использования доступных образов см. в статье [Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Команда `az vm create` содержит псевдонимы, которые можно использовать для быстрого доступа к самым распространенным дистрибутивам и их последним выпускам. Как правило, использовать псевдоним быстрее, чем указывать издателя, предложение, номер SKU и версию каждый раз при создании виртуальной машины.

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

* [Linux on Azure-Endorsed Distributions (Linux на дистрибутивах, рекомендованных для Azure)](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Information for Non-Endorsed Distributions (Информация о нерекомендованных дистрибутивах)](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Как записать образ виртуальной машины Linux для его использования в качестве шаблона Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Примеры команд для быстрой записи существующей виртуальной машины.
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Дальнейшие действия
* Создайте виртуальную машину Linux с помощью [портала](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [интерфейса командной строки](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [шаблона Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* После создания виртуальной машины Linux [добавьте диск данных](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Способы быстрого [сброса пароля или SSH-ключей и управления пользователями](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->



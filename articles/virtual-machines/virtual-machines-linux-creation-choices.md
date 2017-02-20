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
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 23862762fcf0939ce84859fdae0274421c0bb5fe


---
# <a name="different-ways-to-create-a-linux-vm-including-the-azure-cli-20-preview"></a>Различные способы создания виртуальной машины Linux, в том числе с использованием Azure CLI 2.0 (предварительная версия)
Платформа Azure предоставляет гибкие решения по созданию виртуальных машин Linux. Здесь каждый пользователь найдет удобные для себя инструменты и рабочие процессы. В этой статье описаны разные решения и примеры создания виртуальных машин Linux.

## <a name="azure-cli"></a>Интерфейс командной строки Azure
Виртуальные машины в Azure можно создавать с помощью одной из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](virtual-machines-linux-creation-choices-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- Azure CLI 2.0 (предварительная версия) — интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).

[Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) доступен на разных платформах с использованием пакета npm, пакетов, предоставленных для дистрибутивов, или контейнера Docker. Установите оптимальную сборку для своей среды и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах используется Azure CLI 2.0 (предварительная версия). В каждой из этих статей подробно описана соответствующая команда. В них также можно найти другие варианты создания в Linux с помощью [Azure CLI 1.0](virtual-machines-linux-creation-choices-nodejs.md).

* [Создание виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В этом примере с помощью команды [az group create](/cli/azure/group#create) создается группа ресурсов с именем `myResourceGroup`: 
    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * В этом примере с помощью команды [az vm create](/cli/azure/vm#create) создается виртуальная машина `myVM` с использованием открытого ключа `id_rsa.pub` и последнего образа Debian для управляемых дисков Azure:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Чтобы использовать неуправляемые диски, добавьте флаг `--use-unmanaged-disks` в предыдущую команду. Учетная запись хранения будет создана автоматически. Дополнительные сведения об управляемых дисках Azure см. в [этой статье](../storage/storage-managed-disks-overview.md).

* [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В следующем примере с помощью команды [az group deployment create](/cli/azure/group/deployment#create) создается виртуальная машина на основе шаблона, сохраненного в репозитории GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Включает создание балансировщика нагрузки и нескольких виртуальных машин в группе доступности.

* [Добавление диска к виртуальной машине Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В следующем примере с помощью команды [az vm disk attach-new](/cli/azure/vm/disk#attach-new) к существующей виртуальной машине с именем `myVM` добавляется неуправляемый диск на 5 ГБ с именем `myDataDisk.vhd`:
  
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://mystorageaccount.blob.core.windows.net/vhds/myDataDisk.vhd
    ```

## <a name="azure-portal"></a>Портал Azure
С помощью [портала Azure](https://portal.azure.com) можно быстро создать виртуальную машину, так как установка компонентов в локальной системе не требуется. Для создания виртуальной машины используйте портал Azure:

* [Создание виртуальной машины Linux в Azure с помощью портала](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Подключение диска данных к виртуальной машине Linux на портале Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Операционная система и варианты образов
При создании виртуальной машины можно выбрать образ для операционной системы, которую необходимо запустить. Azure и партнеры предлагают множество образов, некоторые из которых содержат предустановленные приложения и средства. Вы также можете передать один из созданных вами образов (см. [следующий раздел](#use-your-own-image)).

### <a name="azure-images"></a>Образы Azure
Чтобы просмотреть список доступных издателей, дистрибутивов и сборок, используйте команды [az vm image](/cli/azure/vm/image).

Отображение списка доступных издателей:

```azurecli
az vm image list-publishers --location WestUS
```

Отображение списка доступных продуктов (предложений) нужного издателя:

```azurecli
az vm image list-offers --publisher-name Canonical --location WestUS
```

Отображение списка доступных номеров SKU (для дистрибутивов) требуемого предложения:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer --location WestUS
```

Отображение списка всех доступных образов для определенного выпуска:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Дополнительные примеры просмотра и использования доступных образов см. в статье [Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Команда **az vm create** содержит псевдонимы, которые можно использовать для быстрого доступа к самым распространенным дистрибутивам и их последним выпускам. Как правило, использовать псевдоним быстрее, чем указывать издателя, предложение, номер SKU и версию каждый раз при создании виртуальной машины.

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
  
  * Примеры команд **az vm** для быстрой записи существующей виртуальной машины, использующей неуправляемые диски:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Дальнейшие действия
* Создайте виртуальную машину Linux с помощью [портала](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [интерфейса командной строки](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [шаблона Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* После создания виртуальной машины Linux [добавьте диск данных](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Способы быстрого [сброса пароля или SSH-ключей и управления пользователями](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->



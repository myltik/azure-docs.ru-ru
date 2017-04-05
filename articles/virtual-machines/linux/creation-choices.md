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
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Различные способы создания виртуальной машины Linux
Платформа Azure предоставляет гибкие решения по созданию виртуальных машин Linux. Здесь каждый пользователь найдет удобные для себя инструменты и рабочие процессы. В этой статье описаны разные решения и примеры создания виртуальных машин Linux, в том числе с помощью Azure CLI 2.0. Вы также можете изучить варианты создания, в том числе с помощью [Azure CLI 1.0](creation-choices-nodejs.md).

Интерфейс командной строки [Azure CLI 2.0](/cli/azure/install-az-cli2) доступен на разных платформах в виде пакета npm, включенных в дистрибутив пакетов или контейнера Docker. Установите оптимальную сборку для своей среды и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах используется Azure CLI 2.0. В каждой из этих статей подробно описана соответствующая команда. В них также можно найти другие варианты создания в Linux с помощью [Azure CLI 1.0](creation-choices-nodejs.md).

* [Создание виртуальной машины Linux с помощью Azure CLI 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В этом примере с помощью команды [az group create](/cli/azure/group#create) создается группа ресурсов с именем `myResourceGroup`: 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * В этом примере с помощью команды [az vm create](/cli/azure/vm#create) создается виртуальная машина `myVM` с использованием открытого ключа `id_rsa.pub` и последнего образа Debian для управляемых дисков Azure:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Чтобы использовать неуправляемые диски, добавьте флаг `--use-unmanaged-disks` в предыдущую команду. Учетная запись хранения будет создана автоматически. Дополнительные сведения об управляемых дисках Azure см. в [этой статье](../../storage/storage-managed-disks-overview.md).

* [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В следующем примере с помощью команды [az group deployment create](/cli/azure/group/deployment#create) создается виртуальная машина на основе шаблона, сохраненного в репозитории GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Включает создание балансировщика нагрузки и нескольких виртуальных машин в группе доступности.

* [Добавление диска к виртуальной машине Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * В следующем примере с помощью команды [az vm disk attach-new](/cli/azure/vm/disk#attach-new) в существующую виртуальную машину `myVM` добавляется управляемый диск емкостью в 50 ГБ.
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Портал Azure
С помощью [портала Azure](https://portal.azure.com) можно быстро создать виртуальную машину, так как установка компонентов в локальной системе не требуется. Для создания виртуальной машины используйте портал Azure:

* [Создание виртуальной машины Linux в Azure с помощью портала](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Подключение диска данных к виртуальной машине Linux на портале Azure](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

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
az vm image list-offers --publisher Canonical --location WestUS
```

Отображение списка доступных номеров SKU (для дистрибутивов) требуемого предложения:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Отображение списка всех доступных образов для определенного выпуска:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Дополнительные примеры просмотра и использования доступных образов см. в статье [Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

* [Linux on Azure-Endorsed Distributions (Linux на дистрибутивах, рекомендованных для Azure)](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Information for Non-Endorsed Distributions (Информация о нерекомендованных дистрибутивах)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Как записать образ виртуальной машины Linux для его использования в качестве шаблона Resource Manager](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Примеры команд **az vm** для быстрой записи существующей виртуальной машины, использующей неуправляемые диски:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Дальнейшие действия
* Создайте виртуальную машину Linux с помощью [портала](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [интерфейса командной строки](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [шаблона Azure Resource Manager](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* После создания виртуальной машины Linux [добавьте диск данных](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Способы быстрого [сброса пароля или SSH-ключей и управления пользователями](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


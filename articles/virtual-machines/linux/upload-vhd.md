---
title: Передача или копирование настраиваемой виртуальной машины Linux с помощью Azure CLI 2.0 | Документация Майкрософт
description: Передача или копирование настраиваемой виртуальной машины, используя модель развертывания Resource Manager и Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: fc3d72ace6398b69a5efa5543c590bba166baaf0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918580"
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Создание виртуальной машины Linux на основе настраиваемого диска с помощью Azure CLI 2.0

<!-- rename to create-vm-specialized -->

В этой статье показано, как передать настраиваемый виртуальный жесткий диск (VHD) или скопировать имеющейся VHD в Azure и создавать на основе настраиваемого диска виртуальные машины Linux. Вы можете установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот VHD для быстрого создания виртуальной машины Azure.

Если необходимо создать несколько виртуальных машин на основе настраиваемого диска, создайте образ на основе виртуальной машины или VHD. Дополнительные сведения см. в статье [Создание пользовательского образа виртуальной машины Azure с помощью интерфейса командной строки](tutorial-custom-images.md).

Существует два варианта.
* [Передача VHD](#option-1-upload-a-specialized-vhd)
* [Копирование имеющейся виртуальной машины Azure](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Быстрые команды

При создании виртуальной машины, используя команду [az vm create](/cli/azure/vm#az_vm_create), на основе настраиваемого или специализированного диска, вы **подключаете** диск (--attach-os-disk), вместо того чтобы указать настраиваемый образ или образ Marketplace (--image). В следующем примере создается виртуальная машина с именем *myVM* на основе управляемого диска *myManagedDisk*, созданного из настраиваемого VHD.

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Требования
Чтобы выполнить приведенные ниже действия, требуется:

* Виртуальная машина Linux, которая была подготовлена для использования в Azure. Раздел [Подготовка виртуальной машины](#prepare-the-vm) этой статьи содержит сведения о том, как найти информацию для конкретного дистрибутива касательно установки агента Linux для Azure (waagent), необходимого для правильной работы виртуальной машины в Azure, и сведения о том, как к ней подключиться по протоколу SSH.
* VHD-файл на основе имеющегося [рекомендуемого для Azure дистрибутива Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (или см. [сведения о нерекомендованных дистрибутивах](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) на виртуальном диске в формате VHD. Для создания VHD-файлов существует несколько средств.
  * Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью команды **qemu-img convert**.
  * Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью команды [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или командлета PowerShell [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .
> 
> 


* Обязательно установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *mystorageaccount* и *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Подготовка виртуальной машины

Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure.

* [Подготовка виртуальной машины на основе CentOS для Azure](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуального жесткого диска Debian для Azure](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуальной машины на основе Red Hat для Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуальной машины SLES или openSUSE для Azure](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Информация о нерекомендованных дистрибутивах](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Другие общие советы по подготовке образов Linux для Azure см. в разделе [Общие замечания по установке Linux](create-upload-generic.md#general-linux-installation-notes).

> [!NOTE]
> [Соглашение об уровне обслуживания платформы Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) применяется к виртуальным машинам, работающим под управлением Linux, только в том случае, если используется один из рекомендуемых дистрибутивов из раздела "Поддерживаемые версии" статьи [Linux в Azure — рекомендованные дистрибутивы](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с заданными параметрами конфигурации.
> 
> 

## <a name="option-1-upload-a-vhd"></a>Вариант 1. Передача VHD

Вы можете передать настраиваемый VHD, выполняемый на локальном компьютере или который вы экспортировали из другого облака. Чтобы создать виртуальную машину Azure с помощью VHD, необходимо передать VHD в учетную запись хранения и создать управляемый диск на основе VHD. 

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед отправкой пользовательского диска и созданием виртуальных машин необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create).

В указанном ниже примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*. [Обзор управляемых дисков Azure](../windows/managed-disks-overview.md).
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Создайте учетную запись хранения для пользовательского диска и виртуальных машин с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

В следующем примере создается учетная запись хранения с именем *mystorageaccount* в ранее созданной группе ресурсов:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Вывод списка ключей учетной записи хранения
Azure создает два 512-разрядных ключа доступа для каждой учетной записи хранения. Эти ключи используются при проверке подлинности в учетной записи хранения, например, для выполнения операций записи. Узнайте больше об управлении доступом к хранилищу [здесь](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Просмотрите список ключей доступа с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Просмотрите ключи доступа для созданной учетной записи хранения.

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

Выходные данные должны быть следующего вида.

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Запишите значение **key1**, так как оно будет использоваться для работы с учетной записью хранения на следующих шагах.

### <a name="create-a-storage-container"></a>Создание контейнера хранилища
Точно так же, как вы создаете различные каталоги для логической организации локальной файловой системы, вы создаете контейнеры в учетной записи хранения, чтобы упорядочить диски. Учетная запись хранения может содержать любое количество контейнеров. Создайте контейнер с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create).

В следующем примере создается контейнер с именем *mydisks*.

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Отправка виртуального жесткого диска
Теперь передайте пользовательский диск с помощью команды [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload). Пользовательский диск передается и хранится как страничный BLOB-объект.

Укажите ключ доступа, созданный на предыдущем шаге контейнер и путь к пользовательскому диску на локальном компьютере:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Передача VHD может занять некоторое время.

### <a name="create-a-managed-disk"></a>Создание управляемого диска


Создайте управляемый диск из VHD, выполнив команду [az disk create](/cli/azure/disk#az_disk_create). В следующем примере создается управляемый диск с именем *myManagedDisk* из виртуального жесткого диска, переданного в учетную запись хранения и сохраненного в контейнере:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Вариант 2. Копирование имеющейся виртуальной машины

Вы также можете создать настраиваемую виртуальную машину в Azure, а затем скопировать диск операционной системы и подключить его к новой виртуальной машине для создания другой копии. Это нормально для тестирования, однако если вы хотите использовать имеющуюся виртуальную машину Azure в качестве модели для нескольких новых виртуальных машин, вместе этого вам необходимо будет создать **образ**. Дополнительные сведения о создании образа на основе имеющейся виртуальной машины Azure см. в статье [Создание пользовательского образа виртуальной машины Azure с помощью интерфейса командной строки](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Создание моментального снимка

В этом примере создается моментальный снимок виртуальной машины с именем *myVM* в группе ресурсов *myResourceGroup*, а также моментальный снимок с именем *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Создание управляемого диска

Создайте управляемый диск из моментального снимка.

Получите идентификатор моментального снимка. В этом примере используется моментальный снимок с именем *osDiskSnapshot*, который находится в группе ресурсов *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Создайте управляемый диск. В этом примере мы создадим управляемый диск с именем *myManagedDisk* на основе моментального снимка размером в 128 ГБ, который хранится в хранилище уровня "Стандартный".

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Создание виртуальной машины

Теперь создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm#az_vm_create), и подключите (--attach-os-disk) управляемый диск как диск операционной системы. В следующем примере создается виртуальная машина с именем *myNewVM* на основе управляемого диска, созданного из отправленного ранее виртуального жесткого диска.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Вы должны иметь возможность подключиться к виртуальной машине по протоколу SSH, используя учетные данные исходной виртуальной машины. 

## <a name="next-steps"></a>Дополнительная информация
После подготовки и передачи пользовательского виртуального диска ознакомьтесь с дополнительными сведениями об [использовании Resource Manager и шаблонов](../../azure-resource-manager/resource-group-overview.md). Возможно, вам также потребуется [добавить диск данных](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для новых виртуальных машин. Если на виртуальных машинах запущены приложения, к которым необходим доступ, [откройте порты и конечные точки](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


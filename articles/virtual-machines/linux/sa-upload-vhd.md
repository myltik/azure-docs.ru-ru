---
title: Передача пользовательского диска Linux с помощью Azure CLI 2.0 | Документация Майкрософт
description: Создание и передача в Azure виртуального жесткого диска (VHD) на основе модели развертывания с помощью Resource Manager посредством Azure CLI 2.0.
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
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 5c5c9894127386a05da9825435cfe5ca07b01542
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Передача пользовательского диска и создание на его основе виртуальной машины Linux с помощью Azure CLI 2.0
В этой статье показано, как передать виртуальный жесткий диск (VHD) в учетную запись хранения Azure с помощью Azure CLI 2.0 и создать на основе этого пользовательского диска виртуальные машины Linux. Эти действия можно также выполнить с помощью [Azure CLI 1.0](upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Эта функциональная возможность позволяет установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот VHD для быстрого создания виртуальных машин Azure.

В этой статье используются учетные записи хранения для готовых виртуальных жестких дисков, но вы также можете выполнить эти действия с помощью [управляемых дисков](upload-vhd.md). 

## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для отправки виртуального жесткого диска в Azure. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#requirements).

Обязательно установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `mydisks`.

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Создайте учетную запись хранения для размещения виртуальных дисков с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create). В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Отобразите список ключей доступа к своей учетной записи хранилища с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). Запишите `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Создайте контейнер в своей учетной записи хранения, используя ключ к хранилищу данных, полученный с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create). В следующем примере создается контейнер с именем `mydisks` с использованием значения ключа к хранилищу данных из `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Наконец, отправьте виртуальный жесткий диск в созданный контейнер, используя команду [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload). Укажите локальный путь к виртуальному жесткому диску в `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Укажите универсальный код ресурса (URI) диска (`--image`) с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создается виртуальная машина с именем `myVM` с помощью отправленного ранее виртуального диска:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Целевой должна быть учетная запись хранения, в которую был передан виртуальный диск. Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды **az vm create**, включая виртуальную сеть, общедоступный IP-адрес, имя пользователя и ключи SSH. Дополнительные сведения о доступных параметрах Resource Manager для интерфейса командной строки см. [здесь](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Требования
Чтобы выполнить приведенные ниже действия, требуется:

* **Операционная система Linux, установленная в VHD-файле**. Установите [рекомендуемый для Azure дистрибутив Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (или см. [сведения о нерекомендованных дистрибутивах](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) на виртуальный диск в формате VHD. Для создания VHD-файлов существует несколько средств.
  * Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
  * Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью командлета PowerShell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .
> 
> 

* Виртуальные машины, созданные на основе пользовательского диска, должны находиться в той же учетной записи хранения, что и этот диск.
  * Создайте учетную запись хранения и контейнер для хранения пользовательского диска и созданных виртуальных машин.
  * Когда вы закончите создание всех виртуальных машин, диск можно спокойно удалить.

Обязательно установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Подготовка диска для передачи
Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure.

* **[Дистрибутивы на основе CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES и OpenSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Прочее — нерекомендованные дистрибутивы](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Другие общие советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](create-upload-generic.md#general-linux-installation-notes)**.

> [!NOTE]
> [Соглашение об уровне обслуживания платформы Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) применяется к виртуальным машинам, работающим под управлением Linux, только в том случае, если используется один из рекомендуемых дистрибутивов из раздела "Поддерживаемые версии" статьи [Linux в Azure — рекомендованные дистрибутивы](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с заданными параметрами конфигурации.
> 
> 

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группы ресурсов логически объединяют все ресурсы Azure для обеспечения работы виртуальных машин, в том числе виртуальные сети и хранилища. См. дополнительные сведения о [группах ресурсов](../../azure-resource-manager/resource-group-overview.md). Перед отправкой пользовательского диска и созданием виртуальных машин необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create).

В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Создайте учетную запись хранения для пользовательского диска и виртуальных машин с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create). Все виртуальные машины с неуправляемыми дисками, созданные на основе пользовательского диска, должны находиться в той же учетной записи хранения, что и этот диск. 

В следующем примере создается учетная запись хранения с именем `mystorageaccount` в ранее созданной группе ресурсов:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Вывод списка ключей учетной записи хранения
Azure создает два 512-разрядных ключа доступа для каждой учетной записи хранения. Эти ключи используются при аутентификации в учетной записи хранения, например, для выполнения операций записи. Узнайте больше об управлении доступом к хранилищу [здесь](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Просмотрите список ключей доступа с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Просмотрите ключи доступа для созданной учетной записи хранения.

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Запишите значение `key1`, так как оно будет использоваться для работы с учетной записью хранения на следующих шагах.

## <a name="create-a-storage-container"></a>Создание контейнера хранилища
Точно так же, как вы создаете различные каталоги для логической организации локальной файловой системы, вы создаете контейнеры в учетной записи хранения, чтобы упорядочить диски. Учетная запись хранения может содержать любое количество контейнеров. Создайте контейнер с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create).

В следующем примере создается контейнер с именем `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Передача VHD
Теперь передайте пользовательский диск с помощью команды [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload). Пользовательский диск передается и хранится как страничный BLOB-объект.

Укажите ключ доступа, созданный на предыдущем шаге контейнер и путь к пользовательскому диску на локальном компьютере:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Создание виртуальной машины
Чтобы создать виртуальную машину с неуправляемыми дисками, укажите URI нужного диска (`--image`) с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создается виртуальная машина с именем `myVM` с помощью отправленного ранее виртуального диска:

Используйте параметр `--image` в команде [az vm create](/cli/azure/vm#az_vm_create), чтобы указать пользовательский диск. Убедитесь, что `--storage-account` соответствует учетной записи хранения, в которой находится пользовательский диск. Для хранения виртуальных машин необязательно использовать тот же контейнер, в котором расположен пользовательский диск. Вы можете создать любые дополнительные контейнеры так же, как перед передачей пользовательских дисков.

В следующем примере создается виртуальная машина с именем `myVM` на основе пользовательского диска:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды **az vm create**, включая имя пользователя и ключи SSH.


## <a name="resource-manager-template"></a>Шаблон Resource Manager
Шаблоны Azure Resource Manager — это JSON-файлы, определяющие среду, которую требуется создать. Шаблоны разделяются на различные поставщики ресурсов, например вычислительных ресурсов или сети ресурсов. Можно использовать существующие шаблоны или создать собственный. Узнайте больше об [использовании Resource Manager и шаблонов](../../azure-resource-manager/resource-group-overview.md).

В поставщике `Microsoft.Compute/virtualMachines` шаблона имеется узел `storageProfile`, который содержит сведения о конфигурации виртуальной машины. Необходимо изменить два основных параметра: универсальные коды ресурсов (URI) `image` и `vhd`, указывающие на пользовательский диск и виртуальный диск для новой виртуальной машины. Ниже приведен пример кода JSON, в котором используется пользовательский диск:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Можно использовать [этот существующий шаблон для создания виртуальной машины на основе пользовательского образа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) или [создать собственные шаблоны Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Настроив шаблон, приступайте к созданию виртуальных машин с помощью команды [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) Укажите универсальный код ресурса (URI) шаблона JSON с параметром `--template-uri` .

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Если ваш JSON-файл хранится локально на компьютере, то вместо него можно использовать параметр `--template-file` .

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Дополнительная информация
После подготовки и передачи пользовательского виртуального диска ознакомьтесь с дополнительными сведениями об [использовании Resource Manager и шаблонов](../../azure-resource-manager/resource-group-overview.md). Возможно, вам также потребуется [добавить диск данных](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для новых виртуальных машин. Если на виртуальных машинах запущены приложения, к которым необходим доступ, [откройте порты и конечные точки](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


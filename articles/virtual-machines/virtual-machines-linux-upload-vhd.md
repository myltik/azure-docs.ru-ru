---
title: "Отправка пользовательского диска Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия) | Документация Майкрософт"
description: "Создание и передача в Azure виртуального жесткого диска (VHD) на основе модели развертывания Resource Manager с помощью Azure CLI 2.0 (предварительная версия)"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 25ca54a6514b281417ac71a89dac167c94137b18
ms.openlocfilehash: bbb9a2cd2123a29507f21c11b536ae81368cf8a7


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-by-using-the-azure-cli-20-preview"></a>Отправка пользовательского диска и создание на его основе виртуальной машины Linux с помощью Azure CLI 2.0 (предварительная версия)
В этой статье показано, как передать виртуальный жесткий диск (VHD) в Azure с использованием модели развертывания с помощью Resource Manager и создать на основе этого диска виртуальные машины Linux. Эта функциональная возможность позволяет установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот VHD для быстрого создания виртуальных машин Azure.


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](virtual-machines-linux-upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0 (предварительная версия)](#quick-commands) — интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).


## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для отправки виртуального жесткого диска в Azure. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#requirements).

Обязательно установите последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `mydisks`.

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Создайте учетную запись хранения для размещения виртуальных дисков с помощью команды [az storage account create](/cli/azure/storage/account#create). Даже если вы хотите использовать [управляемые диски Azure](../storage/storage-managed-disks-overview.md), необходимо создать учетную запись хранения, чтобы отправить в нее виртуальный жесткий диск перед его преобразованием в управляемый диск. В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Отобразите список ключей доступа к своей учетной записи хранилища с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#list). Запишите `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Создайте контейнер в своей учетной записи хранения, используя ключ к хранилищу данных, полученный с помощью команды [az storage container create](/cli/azure/storage/container#create). В следующем примере создается контейнер с именем `mydisks` с использованием значения ключа к хранилищу данных из `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Наконец, отправьте виртуальный жесткий диск в созданный контейнер, используя команду [az storage blob upload](/cli/azure/storage/blob#upload). Укажите локальный путь к виртуальному жесткому диску в `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

### <a name="azure-managed-disks"></a>Управляемые диски Azure
Вы можете создать виртуальную машину, которая использует управляемые диски Azure или неуправляемые диски. Управляемые диски полностью контролируются платформой Azure и не требуют никакой подготовки или выделения места. Дополнительные сведения об управляемых дисках Azure см. в [этой статье](../storage/storage-managed-disks-overview.md). Чтобы создать виртуальную машину из виртуального жесткого диска, сначала преобразуйте его в управляемый диск с помощью команды [az disk create](/cli/azure/disk/create):

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

Получите URI управляемого диска, созданного с помощью [az disk list](/cli/azure/disk/list):

```azurecli
az disk list --resource-group myResourceGroup \
  --query '[].{Name:name,URI:creationData.sourceUri}' --output table
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```azurecli
Name               URI
-----------------  ----------------------------------------------------------------------------------------------------
myUMDiskFromVHD    https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create) и укажите URI созданного управляемого диска (`--image`). В следующем примере создается виртуальная машина с именем `myVM` на основе управляемого диска, созданного из отправленного ранее виртуального жесткого диска.

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

### <a name="unmanaged-disks"></a>Неуправляемые диски
Чтобы создать виртуальную машину с неуправляемыми дисками, укажите URI нужного диска (`--image`) с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создается виртуальная машина с именем `myVM` с помощью отправленного ранее виртуального диска:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd
```

Целевой должна быть учетная запись хранения, в которую был передан виртуальный диск. Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды **az vm create**, включая виртуальную сеть, общедоступный IP-адрес, имя пользователя и ключи SSH. Дополнительные сведения о доступных параметрах Resource Manager для интерфейса командной строки см. [здесь](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Требования
Чтобы выполнить приведенные ниже действия, требуется:

* **Операционная система Linux, установленная в VHD-файле**. Установите [рекомендуемый для Azure дистрибутив Linux](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (или см. [сведения о нерекомендованных дистрибутивах](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) на виртуальный диск в формате VHD. Для создания VHD-файлов существует несколько средств.
  * Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
  * Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью командлета PowerShell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .
> 
> 

* Виртуальные машины, созданные на основе пользовательского диска, должны находиться в той же учетной записи хранения, что и этот диск.
  * Создайте учетную запись хранения и контейнер для хранения пользовательского диска и созданных виртуальных машин.
  * Когда вы закончите создание всех виртуальных машин, диск можно спокойно удалить.

Обязательно установите последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Подготовка диска для передачи
Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure.

* **[Дистрибутивы на основе CentOS](virtual-machines-linux-create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES и OpenSUSE](virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Прочее — нерекомендованные дистрибутивы](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Другие общие советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**.

> [!NOTE]
> [Соглашение об уровне обслуживания платформы Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) применяется к виртуальным машинам, работающим под управлением Linux, только в том случае, если используется один из рекомендуемых дистрибутивов из раздела "Поддерживаемые версии" статьи [Linux в Azure — рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с заданными параметрами конфигурации.
> 
> 

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группы ресурсов логически объединяют все ресурсы Azure для обеспечения работы виртуальных машин, в том числе виртуальные сети и хранилища. См. дополнительные сведения о [группах ресурсов](../azure-resource-manager/resource-group-overview.md). Перед отправкой пользовательского диска и созданием виртуальных машин необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group#create).

В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westus` (см. [Azure Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Обзор управляемых дисков)).
```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Для создания виртуальной машины можно использовать управляемые диски Azure или неуправляемые диски. Управляемые диски полностью контролируются платформой Azure и не требуют никакой подготовки или выделения места. Неуправляемые диски хранятся в виде страничных BLOB-объектов в учетной записи хранения. Дополнительные сведения см. в [обзоре управляемых дисков Azure](../storage/storage-managed-disks-overview.md) и в [описании хранилища BLOB-объектов Azure](../storage/storage-introduction.md#blob-storage). Даже если вы хотите использовать управляемые диски, необходимо создать учетную запись хранения, чтобы отправить в нее виртуальный жесткий диск перед его преобразованием в управляемый диск.

Создайте учетную запись хранения для пользовательского диска и виртуальных машин с помощью команды [az storage account create](/cli/azure/storage/account#create). Все виртуальные машины с неуправляемыми дисками, созданные на основе пользовательского диска, должны находиться в той же учетной записи хранения, что и этот диск. Виртуальную машину с управляемыми дисками можно создать в любой группе ресурсов в вашей подписке.

В следующем примере создается учетная запись хранения с именем `mystorageaccount` в ранее созданной группе ресурсов:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Вывод списка ключей учетной записи хранения
Azure создает два 512-разрядных ключа доступа для каждой учетной записи хранения. Эти ключи используются при аутентификации в учетной записи хранения, например, для выполнения операций записи. Узнайте больше об управлении доступом к хранилищу [здесь](../storage/storage-create-storage-account.md#manage-your-storage-account). Просмотрите список ключей доступа с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#list).

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
Запишите значение `key1` , так как оно будет использоваться для работы с учетной записью хранения на следующих шагах.

## <a name="create-a-storage-container"></a>Создание контейнера хранилища
Точно так же, как вы создаете различные каталоги для логической организации локальной файловой системы, вы создаете контейнеры в учетной записи хранения, чтобы упорядочить диски. Учетная запись хранения может содержать любое количество контейнеров. Создайте контейнер с помощью команды [az storage container create](/cli/azure/storage/container#create).

В следующем примере создается новый контейнер с именем `mydisks` с ключом доступа, полученным на предыдущем шаге (`key1`):

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

## <a name="upload-vhd"></a>Передача VHD
Теперь передайте пользовательский диск с помощью команды [az storage blob upload](/cli/azure/storage/blob#upload). Пользовательский диск передается и хранится как страничный BLOB-объект.

Укажите ключ доступа, созданный на предыдущем шаге контейнер и путь к пользовательскому диску на локальном компьютере:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-vm-from-custom-disk"></a>Создание виртуальной машины на основе пользовательского диска
Этот процесс также позволяет создать виртуальную машину, которая использует управляемые диски Azure или неуправляемые диски. При создании виртуальной машины любого из этих типов укажите URI управляемого диска или неуправляемого диска. Если используются неуправляемые диски, целевая учетная запись хранения должна соответствовать расположению, в котором хранится пользовательский диск. Виртуальную машину можно создать с помощью Azure CLI 2.0 (предварительная версия) или шаблона Resource Manager в формате JSON.

### <a name="azure-cli-20-preview---azure-managed-disks"></a>Azure CLI 2.0 (предварительная версия) для управляемых дисков Azure
Чтобы создать виртуальную машину из виртуального жесткого диска, сначала преобразуйте его в управляемый диск с помощью команды [az disk create](/cli/azure/disk/create). В следующем примере создается управляемый диск с именем `myManagedDisk` из виртуального жесткого диска, переданного в учетную запись хранения и сохраненного в контейнере:

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

Получите URI управляемого диска, созданного с помощью [az disk list](/cli/azure/disk/list):

```azurecli
az disk list --resource-group myResourceGroup \
  --query '[].{Name:name,URI:creationData.sourceUri}' --output table
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```azurecli
Name               URI
-----------------  ----------------------------------------------------------------------------------------------------
myUMDiskFromVHD    https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create) и укажите URI созданного управляемого диска (`--image`). В следующем примере создается виртуальная машина с именем `myVM` на основе управляемого диска, созданного из отправленного ранее виртуального жесткого диска.

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

### <a name="azure-20-preview---unmanaged-disks"></a>Azure CLI 2.0 (предварительная версия) для неуправляемых дисков
Чтобы создать виртуальную машину с неуправляемыми дисками, укажите URI нужного диска (`--image`) с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создается виртуальная машина с именем `myVM` с помощью отправленного ранее виртуального диска:

Используйте параметр `--image` в команде [az vm create](/cli/azure/vm#create), чтобы указать пользовательский диск. Убедитесь, что `--storage-account` соответствует учетной записи хранения, в которой находится пользовательский диск. Для хранения виртуальных машин необязательно использовать тот же контейнер, в котором расположен пользовательский диск. Вы можете создать любые дополнительные контейнеры так же, как перед передачей пользовательских дисков.

В следующем примере создается виртуальная машина с именем `myVM` на основе пользовательского диска:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды **az vm create**, включая имя пользователя и ключи SSH.


### <a name="resource-manager-template---unmanaged-disks"></a>Шаблон Resource Manager для неуправляемых дисков
Шаблоны Azure Resource Manager — это JSON-файлы, определяющие среду, которую требуется создать. Шаблоны разделяются на различные поставщики ресурсов, например вычислительных ресурсов или сети ресурсов. Можно использовать существующие шаблоны или создать собственный. Узнайте больше об [использовании Resource Manager и шаблонов](../azure-resource-manager/resource-group-overview.md).

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

Можно использовать [этот существующий шаблон для создания виртуальной машины на основе пользовательского образа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) или [создать собственные шаблоны Azure Resource Manager](../resource-group-authoring-templates.md). 

Настроив шаблон, приступайте к созданию виртуальных машин с помощью команды [az group deployment create](/cli/azure/group/deployment#create) Укажите универсальный код ресурса (URI) шаблона JSON с параметром `--template-uri` .

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Если ваш JSON-файл хранится локально на компьютере, то вместо него можно использовать параметр `--template-file` .

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Дальнейшие действия
После подготовки и передачи пользовательского виртуального диска ознакомьтесь с дополнительными сведениями об [использовании Resource Manager и шаблонов](../azure-resource-manager/resource-group-overview.md). Возможно, вам также потребуется [добавить диск данных](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для новых виртуальных машин. Если на виртуальных машинах запущены приложения, к которым необходим доступ, [откройте порты и конечные точки](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Feb17_HO2-->



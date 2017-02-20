---
title: "Отправка пользовательского образа Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия) | Документация Майкрософт"
description: "Создание и передача в Azure виртуального жесткого диска (VHD) с пользовательским образом Linux на основе модели развертывания Resource Manager с помощью Azure CLI 2.0 (предварительная версия)"
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
ms.date: 12/15/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 24928d9457db46f6976f50227d81c97ff6c202d7
ms.openlocfilehash: ef735c887c1ce42a160a868b6bcc56b32f10aa9e


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-20-preview"></a>Отправка пользовательского образа диска и создание на его основе виртуальной машины Linux с помощью Azure CLI 2.0 (предварительная версия)
В этой статье показано, как передать виртуальный жесткий диск (VHD-файл) в Azure с использованием модели развертывания с помощью Resource Manager и создавать на его основе виртуальные машины Linux. Эта функциональная возможность позволяет установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот VHD для быстрого создания виртуальных машин Azure.


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](virtual-machines-linux-upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0 (предварительная версия)](#quick-commands) — интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).


## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для отправки виртуальной машины в Azure. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#requirements).

Обязательно установите последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `myimages`.

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Создайте учетную запись хранения для размещения виртуальных дисков с помощью команды [az storage account create](/cli/azure/storage/account#create). В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Отобразите список ключей доступа к своей учетной записи хранилища с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#list). Запишите `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --name mystorageaccount
```

Создайте контейнер в своей учетной записи хранения, используя ключ к хранилищу данных, полученный с помощью команды [az storage container create](/cli/azure/storage/container#create). В следующем примере создается контейнер с именем `myimages` с использованием значения ключа к хранилищу данных из `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name myimages
```

Наконец, отправьте виртуальный жесткий диск в созданный контейнер, используя команду [az storage blob upload](/cli/azure/storage/blob#upload). Укажите локальный путь к виртуальному жесткому диску в `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name myimages --type page \
    --file /path/to/disk/mydisk.vhd --name myImage.vhd
```

Теперь можно создать виртуальную машину из переданного виртуального диска [с помощью шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Кроме того, можно указать универсальный код ресурса (URI) для диска в командной строке (`--image-urn`). В следующем примере создается виртуальная машина с именем `myVM` с помощью отправленного ранее виртуального диска:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --custom-os-disk-type linux \
    --admin-username ops --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/myimages/myImage.vhd
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

* Виртуальные машины, созданные на основе пользовательского образа, должны находиться в той же учетной записи хранения, что и образ.
  * Создайте учетную запись хранения и контейнер для хранения пользовательского образа и созданных виртуальных машин.
  * После создания всех виртуальных машин можно спокойно удалить образ.

Обязательно установите последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>подготовка образа для передачи
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
Группы ресурсов логически объединяют все ресурсы Azure для обеспечения работы виртуальных машин, в том числе виртуальные сети и хранилища. См. дополнительные сведения о [группах ресурсов](../azure-resource-manager/resource-group-overview.md). Перед отправкой пользовательского образа и созданием виртуальных машин необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group#create).

В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Виртуальные машины хранятся в виде страничных BLOB-объектов в учетной записи хранения. Узнайте больше о хранилище BLOB-объектов Azure [здесь](../storage/storage-introduction.md#blob-storage). Следует создать учетную запись хранения для пользовательского образа диска и виртуальных машин с помощью команды [az storage account create](/cli/azure/storage/account#create). Все виртуальные машины, созданные на основе пользовательского образа диска, должны находиться в той же учетной записи хранения, что и этот образ.

В следующем примере создается учетная запись хранения с именем `mystorageaccount` в ранее созданной группе ресурсов:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Вывод списка ключей учетной записи хранения
Azure создает два 512-разрядных ключа доступа для каждой учетной записи хранения. Эти ключи используются при аутентификации в учетной записи хранения, например, для выполнения операций записи. Узнайте больше об управлении доступом к хранилищу [здесь](../storage/storage-create-storage-account.md#manage-your-storage-account). Просмотрите список ключей доступа с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#list).

Просмотрите ключи доступа для созданной учетной записи хранения.

```azurecli
az storage account keys list --resource-group myResourceGroup --name mystorageaccount
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
Точно так же, как вы создаете различные каталоги для логической организации локальной файловой системы, вы создаете контейнеры в учетной записи хранения, чтобы упорядочить виртуальные диски и образы. Учетная запись хранения может содержать любое количество контейнеров. Создайте контейнер с помощью команды [az storage container create](/cli/azure/storage/container#create).

В следующем примере создается новый контейнер с именем `myimages` с ключом доступа, полученным на предыдущем шаге (`key1`):

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name myimages
```

## <a name="upload-vhd"></a>Передача VHD
Теперь можно выполнить отправку пользовательского образа диска с помощью команды [az storage blob upload](/cli/azure/storage/blob#upload). Как и любые виртуальные диски виртуальных машин, ваш пользовательский образ диска передается и сохраняется как страничный BLOB-объект.

Укажите ключ доступа к контейнеру, созданному на предыдущем шаге, и путь к пользовательскому образу диска на локальном компьютере.

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name myimages --type page \
    --file /path/to/disk/mydisk.vhd --name myImage.vhd
```

## <a name="create-vm-from-custom-image"></a>Создание виртуальной машины на основе пользовательского образа
При создании виртуальных машин из пользовательского образа диска следует указать универсальный код ресурса (URI) этого образа диска. Убедитесь, что целевая учетная запись хранения соответствует расположению, в котором хранится ваш пользовательский образ диска. Можно создать виртуальную машину с помощью Azure CLI или шаблона Resource Manager в формате JSON.

### <a name="create-a-vm-using-the-azure-cli"></a>Создание виртуальной машины с помощью Azure CLI
Используйте параметр `--image` в команде [az vm create](/cli/azure/vm#create), чтобы указать свой пользовательский образ. Убедитесь, что `--storage-account` соответствует учетной записи хранения, в которой находится пользовательский образ диска. Нет необходимости использовать контейнер, в котором расположен пользовательский образа, для хранения виртуальных машин. Вы можете создать любые дополнительные контейнеры так же, как перед передачей пользовательских образов дисков.

В следующем примере создается виртуальная машина с именем `myVM` из вашего пользовательского образа:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --custom-os-disk-type linux \
    --admin-username ops --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/myimages/myImage.vhd
```

Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды **az vm create**, включая имя пользователя и ключи SSH.


### <a name="create-a-vm-using-a-json-template"></a>Создание виртуальной машины с помощью шаблона JSON
Шаблоны Azure Resource Manager — это JSON-файлы, определяющие среду, которую требуется создать. Шаблоны разделяются на различные поставщики ресурсов, например вычислительных ресурсов или сети ресурсов. Можно использовать существующие шаблоны или создать собственный. Узнайте больше об [использовании Resource Manager и шаблонов](../azure-resource-manager/resource-group-overview.md).

В поставщике `Microsoft.Compute/virtualMachines` шаблона имеется узел `storageProfile`, который содержит сведения о конфигурации виртуальной машины. Два основных параметра, которые нужно изменить, это универсальные коды ресурса (URI) `image` и `vhd`, указывающие на пользовательский образ диска и виртуальный диск новой виртуальной машины. Ниже приведен пример JSON, в котором используется пользовательский образ диска.

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
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




<!--HONumber=Feb17_HO1-->



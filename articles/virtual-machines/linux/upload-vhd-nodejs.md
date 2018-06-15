---
title: Отправка пользовательского образа Linux с помощью интерфейса командной строки Azure 1.0 | Документация Майкрософт
description: Создание и передача в Azure виртуального жесткого диска (VHD) с пользовательским образом Linux на основе модели развертывания Resource Manager с помощью Azure CLI 1.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: 6eb0cae2b70e0cbb9a4fb5fcab3a58d566d0f4d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915316"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Отправка пользовательского образа диска и создание на его основе виртуальной машины Linux с помощью Azure CLI 1.0
В этой статье показано, как передать виртуальный жесткий диск (VHD-файл) в Azure с использованием модели развертывания с помощью Resource Manager и создавать на его основе виртуальные машины Linux. Эта функциональная возможность позволяет установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот VHD для быстрого создания виртуальных машин Azure.


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для отправки виртуальной машины в Azure. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#requirements).

Войдите в [Azure CLI 1.0](../../cli-install-nodejs.md) и перейдите в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `myimages`.

Сначала создайте группу ресурсов. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUs`:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Создайте учетную запись хранения для размещения виртуальных дисков. В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Получите список ключей доступа для учетной записи хранения. Запишите `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Создайте контейнер в своей учетной записи хранения с помощью полученного ключа к хранилищу данных. В следующем примере создается контейнер с именем `myimages` с использованием значения ключа к хранилищу данных из `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Наконец, отправьте виртуальный жесткий диск в созданный контейнер. Укажите локальный путь к виртуальному жесткому диску в `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Теперь можно создать виртуальную машину из переданного виртуального диска [с помощью шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Кроме того, можно указать универсальный код ресурса (URI) для диска в командной строке (`--image-urn`). В следующем примере создается виртуальная машина с именем `myVM` с помощью отправленного ранее виртуального диска:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Целевой должна быть учетная запись хранения, в которую был передан виртуальный диск. Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды `azure vm create`, а именно: виртуальную сеть, общедоступный IP-адрес, имя пользователя и ключи SSH. Дополнительные сведения о доступных параметрах Resource Manager для интерфейса командной строки см. [здесь](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Требования
Чтобы выполнить приведенные ниже действия, требуется:

* **Операционная система Linux, установленная в VHD-файле**. Установите [рекомендуемый для Azure дистрибутив Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (или см. [сведения о нерекомендованных дистрибутивах](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) на виртуальный диск в формате VHD. Для создания VHD-файлов существует несколько средств.
  * Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
  * Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью командлета PowerShell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .
> 
> 

* Виртуальные машины, созданные на основе пользовательского образа, должны находиться в той же учетной записи хранения, что и образ.
  * Создайте учетную запись хранения и контейнер для хранения пользовательского образа и созданных виртуальных машин.
  * После создания всех виртуальных машин можно спокойно удалить образ.

Войдите в [Azure CLI 1.0](../../cli-install-nodejs.md) и перейдите в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>подготовка образа для передачи
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


## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группы ресурсов логически объединяют все ресурсы Azure для обеспечения работы виртуальных машин, в том числе виртуальные сети и хранилища. Узнайте больше о группах ресурсов Azure [здесь](../../azure-resource-manager/resource-group-overview.md). Перед отправкой пользовательского образа и созданием виртуальных машин необходимо создать группу ресурсов. 

В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUS`:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Виртуальные машины хранятся в виде страничных BLOB-объектов в учетной записи хранения. Узнайте больше о хранилище BLOB-объектов Azure [здесь](../../storage/common/storage-introduction.md#blob-storage). Следует создать учетную запись хранения для пользовательского образа диска и виртуальных машин. Все виртуальные машины, созданные на основе пользовательского образа диска, должны находиться в той же учетной записи хранения, что и этот образ.

В следующем примере создается учетная запись хранения с именем `mystorageaccount` в ранее созданной группе ресурсов:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Вывод списка ключей учетной записи хранения
Azure создает два 512-разрядных ключа доступа для каждой учетной записи хранения. Эти ключи используются при аутентификации в учетной записи хранения, например, для выполнения операций записи. Узнайте больше об управлении доступом к хранилищу [здесь](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Просмотреть ключи доступа можно с помощью команды `azure storage account keys list` .

Просмотрите ключи доступа для созданной учетной записи хранения.

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
Точно так же, как вы создаете различные каталоги для логической организации локальной файловой системы, вы создаете контейнеры в учетной записи хранения, чтобы упорядочить виртуальные диски и образы. Учетная запись хранения может содержать любое количество контейнеров. 

В следующем примере создается новый контейнер с именем `myimages` с ключом доступа, полученным на предыдущем шаге (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Передача VHD
Теперь можно передать ваш пользовательский образ диска. Как и любые виртуальные диски виртуальных машин, ваш пользовательский образ диска передается и сохраняется как страничный BLOB-объект.

Укажите ключ доступа к контейнеру, созданному на предыдущем шаге, и путь к пользовательскому образу диска на локальном компьютере.

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Создание виртуальной машины на основе пользовательского образа
При создании виртуальных машин из пользовательского образа диска следует указать универсальный код ресурса (URI) этого образа диска. Убедитесь, что целевая учетная запись хранения соответствует расположению, в котором хранится ваш пользовательский образ диска. Можно создать виртуальную машину с помощью Azure CLI или шаблона Resource Manager в формате JSON.

### <a name="create-a-vm-using-the-azure-cli"></a>Создание виртуальной машины с помощью Azure CLI
Используйте параметр `--image-urn` в команде `azure vm create`, чтобы указать свой пользовательский образ. Убедитесь, что `--storage-account-name` соответствует учетной записи хранения, в которой находится пользовательский образ диска. Нет необходимости использовать контейнер, в котором расположен пользовательский образа, для хранения виртуальных машин. Вы можете создать любые дополнительные контейнеры так же, как перед передачей пользовательских образов дисков.

В следующем примере создается виртуальная машина с именем `myVM` из вашего пользовательского образа:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды `azure vm create` , а именно: виртуальную сеть, общедоступный IP-адрес, имя пользователя и ключи SSH. Узнайте больше о доступных параметрах Resource Manager для интерфейса командной строки [здесь](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Создание виртуальной машины с помощью шаблона JSON
Шаблоны Azure Resource Manager — это JSON-файлы, определяющие среду, которую требуется создать. Шаблоны разделяются на различные поставщики ресурсов, например вычислительных ресурсов или сети ресурсов. Можно использовать существующие шаблоны или создать собственный. Узнайте больше об [использовании Resource Manager и шаблонов](../../azure-resource-manager/resource-group-overview.md).

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

Можно использовать [этот существующий шаблон для создания виртуальной машины на основе пользовательского образа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) или [создать собственные шаблоны Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

После настройки шаблона можно создавать виртуальные машины с помощью команды `azure group deployment create` . Укажите универсальный код ресурса (URI) шаблона JSON с параметром `--template-uri` .

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Если ваш JSON-файл хранится локально на компьютере, то вместо него можно использовать параметр `--template-file` .

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Дополнительная информация
После подготовки и передачи пользовательского виртуального диска ознакомьтесь с дополнительными сведениями об [использовании Resource Manager и шаблонов](../../azure-resource-manager/resource-group-overview.md). Возможно, вам также потребуется [добавить диск данных](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для новых виртуальных машин. Если на виртуальных машинах запущены приложения, к которым необходим доступ, [откройте порты и конечные точки](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


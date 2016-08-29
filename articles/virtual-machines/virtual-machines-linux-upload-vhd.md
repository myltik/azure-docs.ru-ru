.<properties
	pageTitle="Создание и передача пользовательского образа Linux | Microsoft Azure"
	description="Создание и передача в Azure виртуального жесткого диска (VHD) с пользовательским образом Linux, использующим модель развертывания с помощью Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

.<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="iainfou"/>

# Передача пользовательского образа диска и создание виртуальной машины Linux на его основе

В этой статье показано, как передать виртуальный жесткий диск (VHD-файл) в Azure с использованием модели развертывания с помощью Resource Manager и создавать на его основе виртуальные машины Linux. Эта функциональная возможность позволяет установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот VHD для быстрого создания виртуальных машин Azure.

## Быстрые команды
Войдите в [интерфейс командной строки Azure](../xplat-cli-install.md) (Azure CLI) и перейдите в режим Resource Manager (`azure config mode arm`).

Сначала создайте группу ресурсов.

```bash
azure group create TestRG --location "WestUS"
```

Создайте учетную запись хранения для размещения виртуальных дисков.

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

Выведите список ключей доступа для созданной учетной записи хранения и запишите значение `key1`.

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

Создайте контейнер в своей учетной записи хранения с помощью полученного ключа к хранилищу данных.

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

Наконец, передайте VHD в созданный контейнер.

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

Теперь можно создать виртуальную машину из переданного виртуального диска [с помощью шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Кроме того, можно использовать интерфейс командной строки и указать универсальный код ресурса (URI) для диска, как показано ниже.

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

Целевой должна быть учетная запись хранения, в которую был передан виртуальный диск. Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды `azure vm create`, а именно: виртуальную сеть, общедоступный IP-адрес, имя пользователя и ключи SSH. Дополнительные сведения о доступных параметрах Resource Manager для интерфейса командной строки см. [здесь](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).


## Подробные инструкции
Для подготовки пользовательского образа Linux и его передачи в Azure нужно выполнить несколько шагов. В оставшейся части статьи приведены более подробные сведения о каждом шаге, описанном в предыдущем наборе быстрых команд.


## Требования
Чтобы выполнить приведенные ниже действия, требуется:

- **Операционная система Linux, установленная в VHD-файле**. Установите [рекомендуемый для Azure дистрибутив Linux](virtual-machines-linux-endorsed-distros.md) (или см. [сведения о нерекомендованных дистрибутивах](virtual-machines-linux-create-upload-generic.md)) на виртуальный диск в формате VHD. Для создания VHD-файлов существует несколько средств.
	- Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
	- Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью командлета PowerShell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go).

- Виртуальные машины, созданные на основе пользовательского образа, должны находиться в той же учетной записи хранения, что и образ.
	- Создайте учетную запись хранения и контейнер для хранения пользовательского образа и созданных виртуальных машин.
	- После создания всех виртуальных машин можно спокойно удалить образ.


<a id="prepimage"> </a>
## подготовка образа для передачи

Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure.

- **[Дистрибутивы на основе CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES и openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Прочее — нерекомендованные дистрибутивы](virtual-machines-linux-create-upload-generic.md)**

Другие общие советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**.

> [AZURE.NOTE] [Соглашение об уровне обслуживания платформы Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) применяется к виртуальным машинам, работающим под управлением Linux, только если используется один из рекомендуемых дистрибутивов вместе со данными конфигурации, указанными в разделе "Поддерживаемые дистрибутивы и версии" статьи [Linux в Azure — рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md).


## Создание группы ресурсов
Группы ресурсов логически объединяют все ресурсы Azure для обеспечения работы виртуальных машин, в том числе виртуальные сети и хранилища. Узнайте больше о группах ресурсов Azure [здесь](../resource-group-overview.md). Перед передачей пользовательского образа и создания виртуальных машин необходимо создать группу ресурсов.

```bash
azure group create TestRG --location "WestUS"
```

## Создайте учетную запись хранения.
Виртуальные машины хранятся в виде страничных BLOB-объектов в учетной записи хранения. Узнайте больше о хранилище BLOB-объектов Azure [здесь](../storage/storage-introduction.md#blob-storage). Следует создать учетную запись хранения для пользовательского образа диска и виртуальных машин. Все виртуальные машины, созданные на основе пользовательского образа диска, должны находиться в той же учетной записи хранения, что и этот образ.

Создайте учетную запись хранения в созданной группе ресурсов.

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

## Вывод списка ключей учетной записи хранения
Azure создает два 512-разрядных ключа доступа для каждой учетной записи хранения. Эти ключи используются при аутентификации в учетной записи хранения, например, для выполнения операций записи. Узнайте больше об управлении доступом к хранилищу [здесь](../storage/storage-create-storage-account.md#manage-your-storage-account). Просмотреть ключи доступа можно с помощью команды `azure storage account keys list`.

Просмотрите ключи доступа для созданной учетной записи хранения.

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

Выходные данные должны быть следующего вида.

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Запишите значение `key1`, так как оно будет использоваться для работы с учетной записью хранения на следующих шагах.

## Создание контейнера хранилища
Точно так же, как вы создаете различные каталоги для логической организации локальной файловой системы, вы создаете контейнеры в учетной записи хранения, чтобы упорядочить виртуальные диски и образы. Учетная запись хранения может содержать любое количество контейнеров.

Создайте новый контейнер, указав ключ доступа, полученный на предыдущем шаге.

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

## Передача VHD
Теперь можно передать ваш пользовательский образ диска. Как и любые виртуальные диски виртуальных машин, ваш пользовательский образ диска передается и сохраняется как страничный BLOB-объект.

Укажите ключ доступа к контейнеру, созданному на предыдущем шаге, и путь к пользовательскому образу диска на локальном компьютере.

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

## Создание виртуальной машины на основе пользовательского образа
При создании виртуальных машин из пользовательского образа диска следует указать универсальный код ресурса (URI) этого образа диска. Убедитесь, что целевая учетная запись хранения соответствует расположению, в котором хранится ваш пользовательский образ диска. Можно создать виртуальную машину с помощью Azure CLI или шаблона Resource Manager в формате JSON.


### Создание виртуальной машины с помощью Azure CLI
Используйте параметр `--image-urn` (или просто `-Q`) в команде `azure vm create`, чтобы указать свой пользовательский образ. Убедитесь, что `--storage-account-name` (или `-o`) соответствует учетной записи хранения, в которой находится пользовательский образ диска. Нет необходимости использовать контейнер, в котором расположен пользовательский образа, для хранения виртуальных машин. Вы можете создать любые дополнительные контейнеры так же, как перед передачей пользовательских образов дисков.

Создайте виртуальную машину на основе пользовательского образа диска.

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
	-o testuploadedstorage
```

Кроме того, нужно задать или указать в запросах все дополнительные параметры, необходимые для команды `azure vm create`, а именно: виртуальную сеть, общедоступный IP-адрес, имя пользователя и ключи SSH. Узнайте больше о доступных параметрах Resource Manager для интерфейса командной строки [здесь](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### Создание виртуальной машины с помощью шаблона JSON
Шаблоны Azure Resource Manager — это JSON-файлы, определяющие среду, которую требуется создать. Шаблоны разделяются на различные поставщики ресурсов, например вычислительных ресурсов или сети ресурсов. Можно использовать существующие шаблоны или создать собственный. Узнайте больше об [использовании Resource Manager и шаблонов](../resource-group-overview.md).

В поставщике `Microsoft.Compute/virtualMachines` шаблона имеется узел `storageProfile`, который содержит сведения о конфигурации виртуальной машины. Два основных параметра, которые нужно изменить, это универсальные коды ресурса (URI) `image` и `vhd`, указывающие на пользовательский образ диска и виртуальный диск новой виртуальной машины. Ниже приведен пример JSON, в котором используется пользовательский образ диска.

```bash
"storageProfile": {
          "osDisk": {
            "name": "TestVM",
            "osType": "Linux",
            "caching": "ReadWrite",
			"createOption": "FromImage",
            "image": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd"
            },
            "vhd": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Можно использовать [этот существующий шаблон для создания виртуальной машины на основе пользовательского образа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) или [создать собственные шаблоны Azure Resource Manager](../resource-group-authoring-templates.md).

После настройки шаблона можно создавать виртуальные машины с помощью команды `azure group deployment create`. Укажите универсальный код ресурса (URI) шаблона JSON с параметром `--template-uri`.

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-uri https://uri.to.template/yourtemplate.json
```

Если ваш JSON-файл хранится локально на компьютере, то вместо него можно использовать параметр `--template-file`.

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-file /path/to/yourtemplate.json
```


## Дальнейшие действия
После подготовки и передачи пользовательского виртуального диска ознакомьтесь с дополнительными сведениями об [использовании Resource Manager и шаблонов](../resource-group-overview.md). Возможно, вам также потребуется [добавить диск данных](virtual-machines-linux-add-disk.md) для новых виртуальных машин. Если на виртуальных машинах запущены приложения, к которым необходим доступ, [откройте порты и конечные точки](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0817_2016-->
<properties
	pageTitle="Создание и передача виртуального жесткого диска Linux | Microsoft Azure"
	description="Создание и передача виртуального жесткого диска (VHD) в Azure с использованием модели развертывания Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# Передача виртуального жесткого диска

В этой статье показано, как передать виртуальный жесткий диск (VHD) с использованием модели развертывания Resource Manager. Эта функциональная возможность позволяет установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот VHD для быстрого создания виртуальных машин Azure.

## Быстрые команды
Войдите в [интерфейс командной строки Azure](../xplat-cli-install.md) и перейдите в режим Resource Manager (`azure config mode arm`).

Сначала создайте группу ресурсов.

```bash
azure group create TestRG --location "WestUS"
```

Создайте учетную запись хранения для размещения виртуальных дисков.

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

Выведите список ключей к хранилищу данных для только что созданной учетной записи хранения и запишите значение `key1`.

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

Результат должен быть аналогичен приведенному ниже:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Создайте контейнер в своей учетной записи хранения с помощью полученного ключа к хранилищу данных.

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

Затем передайте VHD в только что созданный контейнер.

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

Теперь можно создать виртуальную машину из переданного виртуального диска [с использованием шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) или с помощью интерфейса командной строки, указав универсальный код ресурса на диске, как показано ниже.

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

Обратите внимание, что вам по-прежнему нужны дополнительные параметры, необходимые для команды `azure vm create`, а именно: виртуальная сеть, общедоступный IP-адрес, имя пользователя, ключи SSH и т. д. Дополнительные сведения о доступных параметрах Resource Manager для интерфейса командной строки см. [здесь](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## Требования
Чтобы выполнить описанные выше действия, вам понадобится следующее:

- **Операционная система Linux, установленная в VHD-файле**. Установите [рекомендуемый для Azure дистрибутив Linux](virtual-machines-linux-endorsed-distros.md) (или см. [сведения о нерекомендованных дистрибутивах](virtual-machines-linux-create-upload-generic.md)) на виртуальный диск в формате VHD. Для создания VHD-файлов существует несколько средств.
	- Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
	- Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите исходный формат VHD. При необходимости можно преобразовать VHDX в формат VHD с помощью командлета PowerShell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go).


<a id="prepimage"> </a>
## подготовка образа для передачи

Microsoft Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure. Перед передачей подготовьте виртуальный диск соответствующим образом.

- **[Дистрибутивы на основе CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES и openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Прочее — нерекомендованные дистрибутивы](virtual-machines-linux-create-upload-generic.md)**

Другие общие советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**.

> [AZURE.NOTE] [Соглашение об уровне обслуживания платформы Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) применяется к виртуальным машинам, работающим под управлением ОС Linux, только если один из рекомендуемых дистрибутивов используется вместе со сведениями о конфигурации, указанными в разделе "Поддерживаемые дистрибутивы и версии" статьи [Linux в Azure — рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md).

## Дальнейшие действия
После подготовки и передачи пользовательского виртуального диска см. дополнительные сведения об [использовании Resource Manager и шаблонов](../resource-group-overview.md). Возможно, вам также потребуется [добавить диск данных](virtual-machines-linux-add-disk.md) для новых виртуальных машин. Если на виртуальных машинах запущены приложения, к которым необходим доступ, [откройте порты и конечные точки](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0713_2016-->
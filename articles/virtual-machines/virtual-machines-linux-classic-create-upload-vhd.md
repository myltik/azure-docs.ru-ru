.<properties
	pageTitle="Создание и передача виртуального жесткого диска Linux | Microsoft Azure"
	description="Создание и передача виртуального жесткого диска Azure, содержащего операционную систему Linux, с использованием классической модели развертывания."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# Создание и передача виртуального жесткого диска с операционной системой Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Вы можете также [передать пользовательский образ с помощью Azure Resource Manager](virtual-machines-linux-upload-vhd.md).

В этой статье показано, как создать и передать виртуальный жесткий диск (VHD-файл), чтобы использовать его в качестве образа для создания виртуальных машин в Azure. Узнайте, как подготовить операционную систему, чтобы использовать ее в качестве образа для создания нескольких виртуальных машин.


## Предварительные требования
В данной статье предполагается, что у вас есть следующие элементы:

- **Операционная система Linux, установленная в VHD-файле**. Вы установили [рекомендуемый для Azure дистрибутив Linux](virtual-machines-linux-endorsed-distros.md) (или ознакомьтесь с [информацией о нерекомендованных дистрибутивах](virtual-machines-linux-create-upload-generic.md)) на виртуальный диск в формате VHD. Для создания VHD-файлов существует несколько средств.
	- Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
	- Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью командлета PowerShell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go).

- **Интерфейс командной строки Azure**. Установите последнюю версию [интерфейса командной строки Azure](../virtual-machines-command-line-tools.md) для передачи VHD.

<a id="prepimage"> </a>
## Шаг 1. Подготовка образа для передачи

Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure. После выполнения указаний, описанных в приведенных ниже руководствах, вернитесь сюда. У вас уже будет VHD-файл для передачи в Azure.

- **[Дистрибутивы на основе CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES и openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Прочее — нерекомендованные дистрибутивы](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] Соглашение об уровне обслуживания платформы Azure применяется к виртуальным машинам, работающим под управлением Linux, только если используется один из рекомендуемых дистрибутивов с конфигурацией, указанной в разделе "Поддерживаемые дистрибутивы и версии" статьи [Linux в Azure — рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md). Все дистрибутивы Linux в коллекции образов Azure — это рекомендуемые дистрибутивы, уже имеющие необходимую конфигурацию.

Другие общие советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**.


<a id="connect"> </a>
## Шаг 2. Подготовка подключения к Azure

Убедитесь, что вы используете интерфейс командной строки Azure в классической модели развертывания (`azure config mode asm`), а затем войдите со своей учетной записью.

```
azure login
```


<a id="upload"> </a>
## Шаг 3. Передача образа в Azure

Для передачи VHD-файла нужна учетная запись хранения. Можно выбрать существующую учетную запись хранения или [создать новую](../storage/storage-create-storage-account.md).

Для передачи образа выполните следующую команду в командной строке Azure:

```bash
azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>
```

В предыдущем примере:

- **BlobStorageURL** — URL-адрес для учетной записи хранения, которую вы планируете использовать.
- **YourImagesFolder** — контейнер внутри хранилища BLOB-объектов, где будут храниться образы.
- **VHDName** — метка, которая отображается на портале для идентификации виртуального жесткого диска.
- **PathToVHDFile** — полный путь и имя VHD-файла на вашем компьютере.

Ниже представлен полный пример.

```bash
azure vm image create UbuntuLTS --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd --os Linux /home/ahmet/UbuntuLTS.vhd
```

## Шаг 4. Создание виртуальной машины из образа
Создайте обычную виртуальную машину с помощью команды `azure vm create`. Укажите имя, присвоенное образу на предыдущем шаге. В следующем примере мы используем имя образа **UbuntuLTS**, присвоенное на предыдущем шаге.

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh --location "West US" "DeployedUbuntu" UbuntuLTS
```

Для создания виртуальной машины следует указать свои имя пользователя и пароль, расположение, DNS-имя и имя образа.

## Дальнейшие действия

Дополнительные сведения см. в [справочнике по Azure CLI для классической модели развертывания Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0824_2016-->
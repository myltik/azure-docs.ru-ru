---
title: Создание и передача виртуального жесткого диска Linux в Azure | Документация Майкрософт
description: Создание и передача виртуального жесткого диска Azure, содержащего операционную систему Linux, с использованием классической модели развертывания.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
ms.openlocfilehash: 1ba568eeaf3bbc3d786cc48e54404aa65a00fecc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841900"
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Создание и передача виртуального жесткого диска с операционной системой Linux
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Вы можете также [передать пользовательский образ с помощью Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В этой статье показано, как создать и передать виртуальный жесткий диск (VHD-файл), чтобы использовать его в качестве образа для создания виртуальных машин в Azure. Узнайте, как подготовить операционную систему, чтобы использовать ее в качестве образа для создания нескольких виртуальных машин. 


## <a name="prerequisites"></a>предварительным требованиям
В данной статье предполагается, что у вас есть следующие элементы:

* **Операционная система Linux, установленная в VHD-файле**. Вы установили [рекомендуемый для Azure дистрибутив Linux](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (или ознакомьтесь с [информацией о нерекомендованных дистрибутивах](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) на виртуальный диск в формате VHD. Для создания VHD-файлов существует несколько средств.
  * Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](http://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
  * Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью командлета PowerShell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .

* **Интерфейс командной строки Azure**. Установите последнюю версию [интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) для передачи VHD-файлов.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Шаг 1. Подготовка образа для передачи
Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure. После выполнения указаний, описанных в приведенных ниже руководствах, вернитесь сюда. У вас уже будет VHD-файл для передачи в Azure.

* **[Дистрибутивы на основе CentOS](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES и OpenSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Прочее — нерекомендованные дистрибутивы](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Соглашение об уровне обслуживания платформы Azure применяется к виртуальным машинам, работающим под управлением Linux, только если используется один из рекомендуемых дистрибутивов с конфигурацией, указанной в разделе "Поддерживаемые дистрибутивы и версии" статьи [Linux в Azure — рекомендованные дистрибутивы](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Все дистрибутивы Linux в коллекции образов Azure — это рекомендуемые дистрибутивы, уже имеющие необходимую конфигурацию.
> 
> 

Другие общие советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](../create-upload-generic.md#general-linux-installation-notes)**.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Шаг 2. Подготовка подключения к Azure
Убедитесь, что вы используете интерфейс командной строки Azure в классической модели развертывания (`azure config mode asm`), а затем войдите со своей учетной записью.

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Шаг 3. Передача образа в Azure
Для передачи VHD-файла нужна учетная запись хранения. Можно выбрать существующую учетную запись хранения или [создать новую](../../../storage/common/storage-create-storage-account.md).

Для передачи образа выполните следующую команду в командной строке Azure:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

В предыдущем примере:

* **BlobStorageURL** — URL-адрес для учетной записи хранения, которую вы планируете использовать.
* **YourImagesFolder** — контейнер внутри хранилища BLOB-объектов, где будут храниться образы.
* **VHDName** — метка, которая отображается на портале для идентификации виртуального жесткого диска.
* **PathToVHDFile** — полный путь и имя VHD-файла на вашем компьютере.

В следующей команде представлен полный пример:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Шаг 4. Создание виртуальной машины из образа
Создайте обычную виртуальную машину с помощью команды `azure vm create`. Укажите имя, присвоенное образу на предыдущем шаге. В следующем примере мы используем имя образа **myImage**, присвоенное на предыдущем шаге:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Для создания виртуальной машины следует указать свои имя пользователя и пароль, расположение, DNS-имя и имя образа.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в [справочнике по Azure CLI для классической модели развертывания Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload

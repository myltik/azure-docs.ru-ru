---
title: "Установка драйвера серии N для Linux | Документация Майкрософт"
description: "Как установить драйверы NVIDIA GPU для виртуальных машин серии N под управлением Linux в Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 1701646ce8cb9494561c37d46cd435b4e7608fe8
ms.lasthandoff: 03/14/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Установка драйверов GPU для виртуальных машин серии N под управлением Linux

Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением поддерживаемого дистрибутива Linux, необходимо установить графические драйверы NVIDIA на каждую виртуальную машину после развертывания. Сведения об установке драйверов также доступны для [виртуальных машин Windows](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


> [!IMPORTANT]
> В настоящее время поддержка Linux GPU доступна только на виртуальных машинах Azure серии NC под управлением Ubuntu Server 16.04 LTS.
> 

Характеристики виртуальных машин серии N, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ознакомьтесь также с разделом [Общие рекомендации для виртуальных машин серии N](#general-considerations-for-n-series-vms).



## <a name="install-nvidia-cuda-drivers"></a>Установка драйверов NVIDIA CUDA

Ниже приведены инструкции по установке драйверов NVIDIA из набора средств NVIDIA CUDA Toolkit 8.0 на виртуальные машины NC под управлением Linux. Разработчики на языках C и C++ могут дополнительно установить полный набор средств для создания приложений, использующих ускорение на GPU. Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Предоставленные в статье ссылки для скачивания драйверов актуальны на момент ее публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](http://www.nvidia.com/).

Чтобы установить набор средств CUDA, установите SSH-подключение к каждой виртуальной машине. Убедитесь, что в системе есть графический процессор с архитектурой CUDA, выполнив следующую команду:

```bash
lspci | grep -i NVIDIA
```
Вы увидите данные, похожие на следующий пример (для адаптера NVIDIA Tesla K80):

![результаты выполнения команды lspci](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

Затем выполните команды, относящиеся к используемому дистрибутиву.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
Установка может занять несколько минут.

Чтобы дополнительно установить полный набор средств CUDA, введите следующую команду.

```bash
sudo apt-get install cuda
```

Перезапустите виртуальную машину и приступите к проверке установки.

## <a name="verify-driver-installation"></a>Проверка установки драйверов


Чтобы запросить состояние устройства GPU, установите SSH-подключение к виртуальной машине и запустите служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

![Состояние устройства NVIDIA](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>Обновления драйверов CUDA

Рекомендуется периодически обновлять драйверы CUDA после развертывания.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

После завершения обновления перезапустите виртуальную машину.


[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

* Мы не рекомендуем устанавливать X server или другие системы, использующие драйвер nouveau, на виртуальные машины NC под управлением Ubuntu. Перед установкой драйверов NVIDIA GPU необходимо отключить драйвер nouveau.  

* Если вы хотите записать образ виртуальной машины Linux, на которую установлены драйверы NVIDIA, ознакомьтесь с разделом [Как подготовить к работе и записать образ виртуальной машины Linux](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графических процессорах NVIDIA в виртуальных машинах серии N см. в следующих разделах:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (для виртуальных машин Azure серии NC);
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (для виртуальных машин Azure серии NV).



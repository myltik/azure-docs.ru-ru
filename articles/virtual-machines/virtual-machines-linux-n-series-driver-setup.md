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
ms.date: 12/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: f03026212dfcbe2faa22188ebfb2e953114a87fd


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>Установка драйверов GPU для виртуальных машин серии N
Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением поддерживаемого дистрибутива Linux, необходимо установить графические драйверы NVIDIA на каждую виртуальную машину после развертывания. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Характеристики виртуальных машин серии N, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



## <a name="supported-gpu-drivers"></a>Поддерживаемые драйверы GPU


> [!NOTE]
> В настоящее время поддержка Linux GPU доступна только на виртуальных машинах Azure серии NC под управлением Ubuntu Server 16.04 LTS.

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Драйверы NVIDIA Tesla для виртуальных машин NC

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899) (самоизвлекающийся установщик RUN)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Установка драйвера Tesla для Ubuntu 16.04 LTS

1. Установите SSH-подключение к виртуальной машине Azure серии N.

2. Убедитесь, что в системе есть графический процессор с архитектурой CUDA, выполнив следующую команду:

    ```bash
    lspci | grep -i NVIDIA
    ```
    Вы увидите данные, похожие на следующий пример (для адаптера NVIDIA Tesla K80):

    ![результаты выполнения команды lspci](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. Загрузите RUN-файл для драйвера своего дистрибутива. Ниже показан пример команды, которая загружает драйверы Ubuntu 16.04 LTS Tesla в каталог /tmp:

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. Если необходимо установить `gcc` и `make` в системе (требуется для драйверов Tesla), введите следующую команду:

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. Перейдите в каталог, содержащий установщик драйвера, и выполните команды, аналогичные следующим:

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>Проверка установки драйверов


Чтобы запросить состояние устройства GPU, выполните служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

![Состояние устройства NVIDIA](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Необязательная установка набора средств NVIDIA CUDA в Ubuntu 16.04 LTS

При необходимости на виртуальные машины серии NC под управлением Ubuntu 16.04 LTS можно также установить набор средств NVIDIA CUDA Toolkit 8.0. Помимо драйверов графического процессора набор средств предоставляет комплексную среду разработки для разработчиков C и C++, создающих приложения, которые используют ускорение GPU.

Чтобы установить набор средств CUDA, выполните команды следующим образом.

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

Установка может занять несколько минут.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графических процессорах NVIDIA в виртуальных машинах серии N см. в следующих разделах:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (для виртуальных машин Azure серии NC);
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (для виртуальных машин Azure серии NV).




<!--HONumber=Dec16_HO2-->



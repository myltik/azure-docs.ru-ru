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
ms.date: 10/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f8cd4fc37caca7fa6094a4780078d9ed882ba3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Установка драйверов GPU NVIDIA на виртуальные машины серии N под управлением Linux

Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением Linux, необходимо установить графические драйверы NVIDIA. В этой статье приводятся действия по установке драйверов после развертывания виртуальных машин серии N. Сведения об установке драйверов также доступны для [виртуальных машин Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Характеристики виртуальных машин серии N, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин Linux, оптимизированных для GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-grid-drivers-for-nv-vms"></a>Установка драйверов GRID для виртуальных машин NV

Чтобы установить драйверы NVIDIA GRID на виртуальных машинах NV, подключитесь по протоколу SSH к каждой виртуальной машине и выполните действия, необходимые для дистрибутива Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Выполните команду `lspci`. Убедитесь, что карта или карты NVIDIA M60 отображаются как устройства PCI.

2. Установите обновления.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Отключите драйвер ядра Nouveau, который несовместим с драйвером NVIDIA. (На виртуальных машинах NV используйте только драйвер NVIDIA.) Чтобы сделать это, создайте в `/etc/modprobe.d ` файл с именем `nouveau.conf` со следующим содержимым:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Перезагрузите виртуальную машину и подключитесь повторно. Выйдите из X-сервера:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Загрузите и установите драйвер GRID.

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 

6. Когда появится запрос на запуск служебной программы nvidia-xconfig для обновления файла конфигурации X, выберите **Да**.

7. После завершения установки скопируйте /etc/nvidia/gridd.conf.template в новый файл gridd.conf в расположении /etc/nvidia/.

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Добавьте следующую строку в файл `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Перезапустите виртуальную машину и приступите к проверке установки.


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 или Red Hat Enterprise Linux 7.3

> [!IMPORTANT]
> Не выполняйте команду `sudo yum update` для обновления версии ядра в CentOS 7.3 или Red Hat Enterprise Linux 7.3. Сейчас установка и обновления драйверов не выполняются в случае обновления ядра.
>

1. Обновите ядро и DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Отключите драйвер ядра Nouveau, который несовместим с драйвером NVIDIA. (На виртуальных машинах NV используйте только драйвер NVIDIA.) Чтобы сделать это, создайте в `/etc/modprobe.d ` файл с именем `nouveau.conf` со следующим содержимым:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Перезагрузите виртуальную машину, повторно подключитесь и установите последнюю версию служб интеграции Linux для Hyper-v.
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3.tar.gz

  tar xvzf lis-rpms-4.2.3.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Повторно подключитесь к виртуальной машине и выполните команду `lspci`. Убедитесь, что карта или карты NVIDIA M60 отображаются как устройства PCI.
 
5. Загрузите и установите драйвер GRID.

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 
6. Когда появится запрос на запуск служебной программы nvidia-xconfig для обновления файла конфигурации X, выберите **Да**.

7. После завершения установки скопируйте /etc/nvidia/gridd.conf.template в новый файл gridd.conf в расположении /etc/nvidia/.
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Добавьте следующую строку в файл `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Перезапустите виртуальную машину и приступите к проверке установки.

### <a name="verify-driver-installation"></a>Проверка установки драйверов


Чтобы запросить состояние устройства GPU, установите SSH-подключение к виртуальной машине и запустите служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

Отобразятся выходные данные, аналогичные приведенным ниже.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Сервер X11
Если вам требуется сервер X11 для удаленных подключений к виртуальной машине NV, рекомендуется использовать [x11vnc](http://www.karlrunge.com/x11vnc/), так как он поддерживает аппаратное ускорение графики. BusID устройства M60 необходимо вручную добавить в файл xconfig (`etc/X11/xorg.conf` в Ubuntu 16.04 LTS, `/etc/X11/XF86config` в CentOS 7.3 или Red Hat Enterprise Server 7.3). Добавьте раздел `"Device"`, аналогичный приведенному ниже.
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Чтобы использовать это устройство, обновите раздел `"Screen"`.
 
BusID можно найти, выполнив

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
BusID может измениться, если виртуальная машина будет перераспределена или перезагружена. Для обновления BusID в конфигурации X11 при перезагрузке виртуальной машины можно использовать скрипт. Например:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Этот файл можно вызывать с правами привилегированного пользователя во время загрузки, создав для него запись в `/etc/rc.d/rc3.d`.


## <a name="install-cuda-drivers-for-nc-vms"></a>Установка драйверов CUDA для виртуальных машин серии NC

Ниже приведены инструкции по установке драйверов NVIDIA из набора средств NVIDIA CUDA Toolkit 8.0 на виртуальные машины NC под управлением Linux. 

Разработчики на языках C и C++ могут дополнительно установить полный набор средств для создания приложений, использующих ускорение на GPU. Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Предоставленные в статье ссылки для скачивания драйверов CUDA актуальны на момент ее публикации. Последние версии драйверов CUDA можно получить на веб-сайте [NVIDIA](http://www.nvidia.com/).
>

Чтобы установить набор средств CUDA, установите SSH-подключение к каждой виртуальной машине. Убедитесь, что в системе есть графический процессор с архитектурой CUDA, выполнив следующую команду:

```bash
lspci | grep -i NVIDIA
```
Вы увидите данные, похожие на следующий пример (для адаптера NVIDIA Tesla K80):

![результаты выполнения команды lspci](./media/n-series-driver-setup/lspci.png)

Затем выполните команды установки, относящиеся к используемому дистрибутиву.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Скачайте и установите драйверы CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-9-0_9.0.176-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  Установка может занять несколько минут.

2. Чтобы дополнительно установить полный набор средств CUDA, введите следующую команду.

  ```bash
  sudo apt-get install cuda
  ```

3. Перезапустите виртуальную машину и приступите к проверке установки.

#### <a name="cuda-driver-updates"></a>Обновления драйверов CUDA

Рекомендуется периодически обновлять драйверы CUDA после развертывания.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 или Red Hat Enterprise Linux 7.3

> [!IMPORTANT]
> Не выполняйте команду `sudo yum update` для обновления версии ядра в CentOS 7.3 или Red Hat Enterprise Linux 7.3. Сейчас установка и обновления драйверов не выполняются в случае обновления ядра.
>

1. Установите последнюю версию Linux Integration Services для Hyper-V.

  > [!IMPORTANT]
  > Если вы установили на виртуальную машину NC24r образ HPC на основе CentOS, перейдите к шагу 3. Поскольку драйверы Azure RDMA и службы интеграции Linux предварительно установлены в образ, служба интеграции Linux не обновляется и обновления ядра отключены по умолчанию.
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3.tar.gz
 
  tar xvzf lis-rpms-4.2.3.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Повторно подключитесь к виртуальной машине и продолжите установку с помощью следующих команд:

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9-0-local-9.0.176-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  Установка может занять несколько минут. 

4. Чтобы дополнительно установить полный набор средств CUDA, введите следующую команду.

  ```bash
  sudo yum install cuda
  ```

5. Перезапустите виртуальную машину и приступите к проверке установки.


### <a name="verify-driver-installation"></a>Проверка установки драйверов


Чтобы запросить состояние устройства GPU, установите SSH-подключение к виртуальной машине и запустите служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

Отобразятся выходные данные, аналогичные приведенным ниже.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi.png)



## <a name="rdma-network-for-nc24r-vms"></a>Сеть RDMA для виртуальных машин NC24r

Можно включить сетевое подключение RDMA на виртуальных машинах NC24r, развернутых в одной группе доступности. Сеть RDMA поддерживает трафик MPI (Message Passing Interface) для приложений, использующих Intel MPI 5.x или более поздней версии. Дополнительные требования приведены ниже.

### <a name="distributions"></a>Дистрибутивы

Разверните виртуальные машины NC24r из одного из следующих образов, поддерживающих подключение RDMA, в Azure Marketplace:
  
* **Ubuntu** — Ubuntu Server 16.04 LTS. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **HPC на основе CentOS** — HPC на основе CentOS 7.3. Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине. 


## <a name="troubleshooting"></a>Устранение неполадок

* Может возникать известная проблема, связанная с работой драйверов CUDA на виртуальных машинах Azure серии N под управлением ядра Linux версии 4.4.0-75 на Ubuntu 16.04 LTS. Если вы обновляете более раннюю версию ядра, обновите ее как минимум до версии 4.4.0-77. 



## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графических процессорах NVIDIA в виртуальных машинах серии N см. в следующих разделах:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (для виртуальных машин Azure серии NC);
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (для виртуальных машин Azure серии NV).

* Чтобы записать образ виртуальной машины Linux с установленными драйверами NVIDIA, см. статью [Как подготовить к работе и записать образ виртуальной машины Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

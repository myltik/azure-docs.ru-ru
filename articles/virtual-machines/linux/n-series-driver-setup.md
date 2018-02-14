---
title: "Установка драйвера серии N для Linux | Документация Майкрософт"
description: "Как установить драйверы NVIDIA GPU для виртуальных машин серии N под управлением Linux в Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 421e594f7bd4df1bc1c5faedc2c8bfab0540ca61
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Установка драйверов GPU NVIDIA на виртуальные машины серии N под управлением Linux

Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением Linux, необходимо установить графические драйверы NVIDIA. В этой статье приводятся действия по установке драйверов после развертывания виртуальных машин серии N. Сведения об установке драйверов также доступны для [виртуальных машин Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Характеристики виртуальных машин серии N, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин Linux, оптимизированных для GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-and-nd-vms"></a>Установка драйверов CUDA для виртуальных машин NC, NCv2 и ND

Ниже приведены инструкции по установке драйверов NVIDIA из набора средств NVIDIA CUDA Toolkit на виртуальные машины NC под управлением Linux. 

Разработчики на языках C и C++ могут дополнительно установить полный набор средств для создания приложений, использующих ускорение на GPU. Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Предоставленные в статье ссылки для скачивания драйверов CUDA актуальны на момент ее публикации. Последние версии драйверов CUDA можно получить на веб-сайте [NVIDIA](https://developer.nvidia.com/cuda-zone).
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
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

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

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS или Red Hat Enterprise Linux 7.3 или 7.4

1. Обновите ядро.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest Linux Integration Services for Hyper-V.

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
 
  tar xvzf lis-rpms-4.2.3-5.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Повторно подключитесь к виртуальной машине и продолжите установку с помощью следующих команд:

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

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

Если драйвер установлен, то отобразятся выходные данные, аналогичные приведенным ниже. Обратите внимание, что **GPU-Util** отобразит 0 %, если в данный момент графический процессор не выполняет рабочую нагрузку на виртуальной машине. Версия драйвера и сведения о GPU могут отличаться от показанных на изображении.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi.png)


## <a name="rdma-network-connectivity"></a>Сетевое подключение RDMA

Сетевое подключение RDMA можно включить на виртуальных машинах серии N с поддержкой RDMA, таких как NC24r, развернутых в одной группе доступности. Сеть RDMA поддерживает трафик MPI (Message Passing Interface) для приложений, использующих Intel MPI 5.x или более поздней версии. Дополнительные требования приведены ниже.

### <a name="distributions"></a>Дистрибутивы

Разверните виртуальные машины серии N с поддержкой RDMA из образа, поддерживающего подключение RDMA для виртуальных машин серии N, в Azure Marketplace:
  
* **Ubuntu 16.04 LTS**. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

> [!NOTE]
> Образы HPC на основе centOS сейчас не рекомендуется использовать для подключения RDMA на виртуальных машинах серии N. RDMA не поддерживается на последнем ядре CentOS 7.4 с поддержкой графических процессоров NVIDIA.
> 


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
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
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


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS или Red Hat Enterprise Linux 

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
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz

  tar xvzf lis-rpms-4.2.3-5.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Повторно подключитесь к виртуальной машине и выполните команду `lspci`. Убедитесь, что карта или карты NVIDIA M60 отображаются как устройства PCI.
 
5. Загрузите и установите драйвер GRID.

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
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

Если драйвер установлен, то отобразятся выходные данные, аналогичные приведенным ниже. Обратите внимание, что **GPU-Util** отобразит 0 %, если в данный момент графический процессор не выполняет рабочую нагрузку на виртуальной машине. Версия драйвера и сведения о GPU могут отличаться от показанных на изображении.

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

## <a name="troubleshooting"></a>Устранение неполадок

* Когда необходимо запрашивать карты, для быстрого получения выходных данных команды можно задать режим сохранения с помощью команды `nvidia-smi`. Чтобы задать режим сохранения, выполните `nvidia-smi -pm 1`. Обратите внимание, что в случае перезапуска виртуальной машины настройка режима не сохранится. Всегда можно написать сценарий настройки режима для выполнения при запуске.


## <a name="next-steps"></a>Дополнительная информация

* Чтобы записать образ виртуальной машины Linux с установленными драйверами NVIDIA, см. статью [Как подготовить к работе и записать образ виртуальной машины Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

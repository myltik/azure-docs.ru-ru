---
title: Настройка кластера Linux RDMA для выполнения приложений MPI | Документация Майкрософт
description: Создание кластера виртуальных машин Linux с размером H16r, H16mr, A8 или A9 для запуска приложений MPI в сети Azure RDMA
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: d53305aae3b12c0de983dced85a9626cf98c6309
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210381"
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Настройка кластера Linux RDMA для выполнения приложений MPI
Узнайте, как настроить кластер Linux RDMA в Azure с [виртуальными машинами серии H или серии A для ресурсоемких вычислений](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для параллельного выполнения приложений с интерфейсом MPI. Эта статья содержит процедуру подготовки образа Linux HPC для запуска Intel MPI в кластере. После предварительной подготовки вы развернете кластер виртуальных машин, используя этот образ и еще один для любого размера виртуальной машины Azure, поддерживающего RDMA (сейчас это H16r, H16mr, A8 и A9). Такой кластер позволяет выполнять приложения MPI, которые эффективно взаимодействуют через сеть с низкой задержкой и высокой пропускной способностью с использованием технологии удаленного прямого доступа к памяти (RDMA).

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager](../../../resource-manager-deployment-model.md) и классическая модель. В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

## <a name="cluster-deployment-options"></a>Варианты развертывания кластера
Ниже перечислены методы, которые можно использовать для создания кластера Linux RDMA с планировщиком заданий или без него.

* **Сценарии интерфейса командной строки Azure**. Как показано далее в этой статье, [интерфейс командной строки Azure](../../../cli-install-nodejs.md) удобен для автоматизированного развертывания кластера виртуальных машин Linux с поддержкой RDMA. В классической модели развертывания интерфейс командной строки в режиме управления службами создает узлы кластера последовательно, поэтому развертывание нескольких вычислительных узлов может потребовать несколько минут. Чтобы создать сетевое подключение на основе RDMA, используя классическую модель развертывания, разверните виртуальные машины в одну облачную службу.
* **Шаблоны Azure Resource Manager**. Модель развертывания Resource Manager также позволяет развернуть кластер виртуальных машин Linux с поддержкой RDMA, подключенных к сети RDMA. Для развертывания своего решения вы можете [создать собственный шаблон](../../../resource-group-authoring-templates.md) или изучить [страницу шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/), которая содержит шаблоны, созданные корпорацией Майкрософт и сообществом пользователей. Шаблоны диспетчера ресурсов — самый быстрый и надежный способ развертывания кластера Linux. Чтобы создать сетевое подключение на основе RDMA, используя модель развертывания Resource Manager, разверните виртуальные машины в одну группу доступности.
* **Пакет HPC**. Можно создать кластер пакета Microsoft HPC в Azure и добавить вычислительные узлы с поддержкой технологии RDMA под управлением поддерживаемых дистрибутивов Linux для доступа к сети RDMA. Дополнительную информацию см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Пример процесса развертывания в классической модели
Далее показано, как с помощью Azure CLI развернуть виртуальную машину SUSE Linux Enterprise Server (SLES) 12 SP1 HPC из Azure Marketplace, настроить ее и создать пользовательский образ виртуальной машины. Затем этот образ можно использовать для автоматизации развертывания кластера виртуальных машин с поддержкой технологии RDMA.

> [!TIP]
> Выполните аналогичные действия для развертывания кластера виртуальных машин с поддержкой технологии RDMA, созданных на базе образов HPC на основе CentOS, доступных в Azure Marketplace. Некоторые шаги незначительно отличаются (мы указываем на это). 
>
>

### <a name="prerequisites"></a>предварительным требованиям
* **Клиентский компьютер**. Для взаимодействия с Azure необходим клиентский компьютер под управлением Mac, Linux или Windows. Далее предполагается, что вы используете клиент Linux.
* **Подписка Azure**. Если у вас ее нет, можно за пару минут создать [бесплатную учетную запись](https://azure.microsoft.com/free/). Для больших кластеров можно использовать подписку с оплатой по мере использования или другие варианты приобретения.
* **Доступность виртуальных машин нужного размера**. Сейчас технологию RDMA поддерживают следующие размеры экземпляра: H16r, H16mr, A8 и A9. Проверьте [доступность продуктов по регионам](https://azure.microsoft.com/regions/services/) , чтобы узнать, в каких регионах Azure их можно использовать.
* **Квота ядер**. Возможно, потребуется увеличить квоту ядер для развертывания кластера виртуальных машин для ресурсоемких вычислений. Например, для развертывания восьми виртуальных машин A9 необходимо не меньше 128 ядер, как показано в этой статье. Кроме того, количество ядер, которые можно развернуть для некоторых семейств размеров виртуальных машин (включая серию H), может быть ограничено условиями вашей подписки. Чтобы увеличить квоту, [отправьте запрос в службу поддержки](../../../azure-supportability/how-to-create-azure-support-request.md). Это бесплатная услуга.
* **Azure CLI**. [Установите](../../../cli-install-nodejs.md) Azure CLI и [подключитесь к подписке Azure](/cli/azure/authenticate-azure-cli) с клиентского компьютера.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Подготовка виртуальной машины SLES 12 SP1 HPC
Войдя в Azure с помощью Azure CLI, выполните команду `azure config list` и убедитесь, что в выводе отображается режим управления службами. Если это не так, активируйте нужный режим, выполнив следующую команду:

    azure config mode asm


Введите следующую команду, чтобы вывести список подписок, которые вы можете использовать.

    azure account list

Для текущей активной подписки `Current` имеет значение `true`. Если это не та подписка, которую вы хотите использовать для создания кластера, задайте правильный идентификатор активной подписки.

    azure account set <subscription-Id>

Для просмотра общедоступных образов SLES 12 SP1 HPC в Azure выполните следующую команду, если среда оболочки поддерживает команду **grep**:

    azure vm image list | grep "suse.*hpc"

Подготовьте к работе виртуальную машину с поддержкой RDMA на основе образа SLES 12 SP1 HPC, выполнив следующую команду.

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Описание

* Размер (в нашем примере — A9) должен предусматривать поддержку RDMA.
* Номер внешнего порта SSH (в данном примере — 22, номер порта SSH по умолчанию) — любой допустимый номер порта. Для внутреннего порта SSH будет указан номер 22.
* Новая облачная служба создается в регионе Azure, указанном согласно расположению. Укажите расположение с нужным размером ВМ.
* Для приоритетной поддержки SUSE (которая предусматривает дополнительную плату) сейчас можно выбрать образ SLES 12 SP1 с одним из этих двух имен: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Настройка виртуальной машины
Когда подготовка виртуальной машины завершится, установите SSH-подключение к виртуальной машине, используя ее внешний IP-адрес (или DNS-имя) и настроенный ранее номер внешнего порта. Настройте виртуальную машину. Подробнее о подключении см. в статье [о входе на виртуальную машину под управлением Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Выполните команды от имени пользователя, настроенного на виртуальной машине, если только для выполнения этого шага не нужен доступ с правами root.

> [!IMPORTANT]
> Microsoft Azure не предоставляет доступ с правами root для виртуальных машин Linux. Чтобы получить административный доступ при подключении к виртуальной машине в качестве обычного пользователя, выполняйте команды с использованием `sudo`.
>
>

* **Обновления**. Установите обновления с помощью zypper. Также можно установить служебные программы NFS.

  > [!IMPORTANT]
  > На виртуальных машинах SLES 12 SP1 HPC мы не рекомендуем выполнять обновления ядра, так как это может вызвать проблемы с драйверами Linux RDMA.
  >
  >
* **Intel MPI**. Для завершения установки Intel MPI на виртуальной машине SLES 12 SP1 HPC выполните следующую команду:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Блокировка памяти**. Чтобы коды MPI блокировали память для RDMA, добавьте или измените следующие параметры в файле /etc/security/limits.conf. Для изменения этого файла требуется доступ с правами root.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > В целях тестирования можно также задать неограниченное значение для параметра memlock. Например, `<User or group name>    hard    memlock unlimited`. Дополнительные сведения см. в статье [Best Known Methods for Setting Locked Memory Size](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size) (Рекомендуемые методы определения размера заблокированной памяти).
  >
  >
* **Ключи SSH для виртуальных машин SLES**. Создайте ключи SSH, чтобы установить отношения доверия для учетной записи пользователя на всех вычислительных узлах в кластере SLES при выполнении заданий MPI. Если развернута виртуальная машина HPC на основе CentOS, не выполняйте этот шаг. Инструкции по установке доверия SSH без пароля для узлов кластера после записи образа и развертывания кластера см. далее в статье.

    Выполните следующую команду, чтобы создать ключи SSH. Когда появится запрос на ввод нажмите клавишу **Ввод**. Ключи будут созданы в расположении по умолчанию без указания парольной фразы.

        ssh-keygen

    Добавьте открытый ключ в файл authorized_keys для известных открытых ключей.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Откройте или создайте файл config в каталоге ~/.ssh. Укажите диапазон IP-адресов частной сети, которую планируется использовать в Azure (в данном примере это 10.32.0.0/16).

        host 10.32.0.*
        StrictHostKeyChecking no

    Список IP-адресов частной сети для каждой виртуальной машины в кластере также можно получить следующим образом:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > Настройка `StrictHostKeyChecking no` может создать потенциальную угрозу безопасности, если определенный IP-адрес или диапазон не задан.
  >
  >
* **Приложения**. Установите все нужные приложения и (или) выполните другие настройки перед записью образа.

### <a name="capture-the-image"></a>Запись образа
Чтобы записать образ, выполните следующую команду на виртуальной машине Linux: При этом виртуальная машина отзывается, но учетные записи пользователей и ключи SSH, которые вы настроили, сохраняются.

```
sudo waagent -deprovision
```

На клиентском компьютере выполните следующие команды Azure CLI для записи образа. Подробнее см. в статье [Запись классической виртуальной машины Linux в виде образа](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

После выполнения этих команд образ виртуальной машины будет записан, а виртуальная машина будет удалена. Теперь ваш образ готов для развертывания кластера.

### <a name="deploy-a-cluster-with-the-image"></a>Развертывание кластера с помощью образа
Укажите соответствующие вашей среде значения в следующем скрипте Bash и запустите его на клиентском компьютере. Если используется классическая модель развертывания, Azure развертывает виртуальные машины последовательно. Поэтому для развертывания восьми виртуальных машин размера A9, указанных в этом скрипте, потребуется несколько минут.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Рекомендации для кластера CentOS HPC
Если вы будете использовать для создания кластера доступные в Azure Marketplace образы на основе CentOS, а не SLES 12 HPC, общая процедура будет такой же, как описано в предыдущем разделе. Но есть несколько важных отличий при подготовке и настройке ВМ.

- На виртуальной машине, подготовленной из образа на основе CentOS HPC, уже установлен интерфейс Intel MPI.
- Параметры блокировки памяти уже добавлены в файл /etc/security/limits.conf на виртуальной машине.
- Не создавайте ключи SSH на виртуальной машине, подготовленной для сбора информации. Вместо этого мы рекомендуем после развертывания кластера настроить аутентификацию на уровне пользователей. Дополнительные сведения приведены в следующем разделе.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Настройка для кластера отношения доверия SSH без использования пароля
В кластере на базе CentOS HPC существуют два метода настройки отношения доверия между вычислительными узлами: проверка подлинности на основе узлов и проверка подлинности на основе пользователей. В этой статье не рассматривается проверка подлинности на основе узлов, которая обычно должна осуществляться с помощью сценария расширения во время развертывания. После развертывания для установки доверия удобно применять пользовательскую проверку подлинности. Для этого нужно создать ключи SSH для их совместного использования вычислительными узлами в кластере. Такой метод, называемый входом через SSH без пароля, необходим для выполнения заданий MPI.

На сайте [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) можно найти предоставленный сообществом пользователей пример сценария для включения аутентификации пользователей в кластере HPC на основе CentOS. Скачайте и используйте этот скрипт, следуя приведенным ниже указаниям. Вы можете также изменить этот сценарий или использовать любой другой метод, чтобы настроить проверку подлинности через SSH без пароля между вычислительными узлами кластера.

    wget https://raw.githubusercontent.com/tanewill/utils/master/user_authentication.sh

Чтобы выполнить сценарий, требуется префикс IP-адресов подсети. Получите его, выполнив следующую команду на одном из узлов кластера. Вывод должен выглядеть примерно как 10.1.3.5, где 10.1.3 — это префикс.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Сценарий можно выполнить, используя три параметра: общее для вычислительных узлов имя пользователя, общий для вычислительных узлов пароль пользователя и префикс подсети, который был возвращен из предыдущей команды.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Скрипт выполняет следующее:

* Создает каталог с именем .ssh на главном узле, который требуется для входа без пароля.
* Создает в каталоге .ssh файл конфигурации, который настраивает вход без пароля, чтобы разрешить вход с любого узла в кластере.
* Создает файлы, содержащие имена и IP-адреса всех узлов в кластере. После выполнения сценария эти файлы остаются для последующего использования.
* Создает пару из закрытого и открытого ключей для каждого узла кластера, включая главный узел, и создает записи в файле authorized_keys.

> [!WARNING]
> Выполнение этого сценария может создать угрозу безопасности. Не допускайте распространения сведений об открытом ключе в каталоге ~/.ssh.
>
>

## <a name="configure-intel-mpi"></a>Настройка Intel MPI
Для запуска приложений MPI в Azure Linux RDMA необходимо настроить определенные переменные среды, связанные с Intel MPI. Ниже приведен пример скрипта Bash для настройки переменных и запуска приложения. Измените путь к файлу mpivars.sh в соответствии с вашей установкой Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Формат файла узла выглядит следующим образом. Добавьте одну строку для каждого узла в кластере. Укажите частные IP-адреса из виртуальной сети, определенной ранее, но не DNS-имена. Например для двух узлов с IP-адресами 10.32.0.1 и 10.32.0.2 файл содержит следующие сведения:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Запуск интерфейса MPI на базовом кластере с двумя узлами
Если это еще не сделано, сначала настройте среду для Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Выполнение команды MPI
Выполните любую команду MPI на одном из вычислительных узлов, чтобы убедиться, что MPI установлена правильно и что минимум два вычислительных узла могут обмениваться данными. Следующая команда **mpirun** выполняет команду **hostname** на двух узлах.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
В выходных данных должны быть перечислены имена всех узлов, переданные в качестве входных данных для `-hosts`. Например, команда **mpirun** с двумя узлами возвращает выходные данные следующего вида.

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Запуск теста производительности MPI
Следующая команда Intel MPI выполняет проверку связи для анализа конфигурации кластера и подключения к сети RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

В работающем кластере с двумя узлами вы должны увидеть результаты, аналогичные приведенным ниже. В сети RDMA Azure для сообщения размером до 512 байт можно ожидать задержку на уровне 3 микросекунд или ниже.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Дополнительная информация
* Разверните и запустите приложения MPI в кластере Linux.
* Рекомендации по Intel MPI см. в [документации для библиотеки Intel MPI](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/).
* Чтобы создать кластер Intel Lustre с помощью образа пакета HPC на основе CentOS, воспользуйтесь [шаблоном быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos). Дополнительные сведения см. в статье [Deploying Intel Cloud Edition for Lustre on Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/) (Развертывание Intel Cloud Edition для Lustre в Microsoft Azure).

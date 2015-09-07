<properties
 pageTitle="Настройка кластера Linux RDMA для выполнения приложений MPI | Microsoft Azure"
	description="Узнайте, как создать кластер Linux с виртуальными машинами A8 или A9, чтобы использовать RDMA для запуска приложений MPI."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>
<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/17/2015"
	ms.author="danlep"/>

# Настройка кластера Linux RDMA для выполнения приложений MPI

В этой статье показано, как настроить кластер Linux RDMA в Azure с [экземплярами виртуальных машин A8 и A9](virtual-machines-a8-a9-a10-a11-specs.md) для выполнения приложений MPI. При настройке виртуальных машин A8 и A9 под управлением Linux для выполнения поддерживаемой реализации MPI приложения MPI будут эффективно взаимодействовать по сети Azure с низкой задержкой и высокой пропускной способностью, которая основана на технологии удаленного прямого доступа к памяти (RDMA).

>[AZURE.NOTE]Azure Linux RDMA в настоящее время поддерживается библиотекой Intel MPI версии 5.0 ОС для SUSE Linux Enterprise Server 12 (SLES 12).
>
> Azure также предоставляет экземпляры для ресурсоемких вычислений A10 и A11 с идентичными экземплярам A8 и A9 возможностями обработки, но без подключения к серверной сети RDMA. Для запуска рабочих нагрузок MPI в Azure наилучшей производительности, как правило, можно добиться с экземплярами A8 и A9.


## Варианты развертывания кластера Linux

Ниже перечислены методы, которые можно использовать для создания кластера Linux RDMA с планировщиком заданий или без него.

* **Пакет HPC** — вы можете создать кластер пакета Microsoft HPC в Azure и добавить вычислительные узлы под управлением поддерживаемых дистрибутивов Linux (поддерживаются начиная с пакета HPC 2012 R2 с обновлением 2). Некоторые узлы Linux можно настроить для доступа к сети RDMA. Сведения о начале работы см. в [документации пакета HPC](http://go.microsoft.com/fwlink/?LinkId=617894).

* **Скрипты Azure CLI** — вы можете использовать[интерфейс командной строки Azure](../xplat-cli.md) (CLI) для Mac, Linux и Windows, чтобы создавать собственные скрипты для развертывания виртуальной сети и других необходимых компонентов для создания кластера Linux. CLI в режиме управления службами Azure (asm) развертывает узлы кластера последовательно, поэтому при развертывании множества вычислительных узлов для его завершения может потребоваться несколько минут. Пример см. далее в этой статье.

* **Шаблоны диспетчера ресурсов Azure** — создав простой шаблон JSON диспетчера ресурсов Azure и выполнив команды Azure CLI в режиме arm или используя предварительную версию портала Azure, вы можете развернуть несколько виртуальных машин A8 и A9 под управлением Linux, а также определить параметры виртуальных сетей, DNS, статические IP-адреса и другие ресурсы, чтобы создать вычислительный кластер, которые может использовать все преимущества сети RDMA и выполнять рабочие нагрузки MPI. Можно [создать собственный шаблон](../resource-group-authoring-templates.md) или изучить [страницу краткого руководства по шаблонам Azure](https://azure.microsoft.com/documentation/templates/), которая содержит шаблоны, созданные корпорацией Майкрософт и сообществом пользователей. Обычно шаблоны диспетчера ресурсов — это самый быстрый и самый надежный способ развертывания кластера Linux.

## Развертывание в режиме управления службами Azure с помощью скриптов Azure CLI

Далее показано, как использовать Azure CLI для развертывания виртуальных машин SLES 12, установки библиотеки Intel MPI и других настроек, создания образа виртуальной машины и скрипта для развертывания кластера виртуальных машин A8 или A9.

### Предварительные требования

* **Клиентский компьютер** — вам необходим клиентский компьютер Mac, Linux или Windows для взаимодействия с Azure.

* **Подписка Azure ** — если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Квота ядер** — вам может потребоваться увеличить квоту ядер для развертывания кластера виртуальных машин A8 или A9. Например, для развертывания восьми виртуальных машин A9 необходимо не меньше 128 ядер, как показано в этой статье. Чтобы увеличить квоту, бесплатно [отправьте запрос в службу поддержки](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

* **Azure CLI** — [установите](../xplat-cli-install.md) интерфейс командной строки Azure и [настройте его](../xplat-cli-connect.md) для использования подписки Azure на клиентском компьютере.


### Подготовка виртуальной машины SLES 12

После входа в систему в Azure с помощью Azure CLI выполните команду `azure config list`, чтобы убедиться, что CLI находится в режиме управления службами Azure (asm). Если это не так, активируйте режим, выполнив следующую команду:

```
azure config mode asm
```

Введите следующую команду, чтобы перечислить все подписки, которые вы можете использовать:

```
azure account list
```

Значение `Current` текущей активной подписки равно `true`. Если это не та подписка, которую вы хотите использовать для создания кластера, установите в качестве активной подписки соответствующий номер подписки:

```he
azure account set <subscription-number>
```

Для просмотра общедоступных образов SLES 12 HPC в Azure выполните следующую команду, если среда оболочки поддерживает **grep**:

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]В образах SLES 12 HPC предварительно установлены необходимые драйверы Linux RDMA для Azure.

Теперь настройте виртуальную машину размера A9 с доступным образом SLES 12 HPC, выполнив следующую команду:

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 10004 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

где:

* размер (в данном примере — A9) может быть A8 или A9;

* внешний номер порта SSH (в данном примере — 10004) — любой допустимый номер порта; внутренний номер порта SSH — 22;

* новая облачная служба будет создан в регионе Azure, заданной расположением; укажите расположение, например "Запад США", в котором доступны экземпляры A8 и A9;

* именем образа в настоящее время может быть `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` (бесплатно) или `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` для поддержки приоритетов SUSE (платно).

### Настройка виртуальной машины

После завершения подготовки виртуальной машины установите SSH-подключение к виртуальной машине с помощью ее внешнего IP-адреса (или DNS-имени) и внешнего порта номер, который вы указали, и настройте ее. Подробнее о подключении см. в разделе [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md).

>[AZURE.NOTE]Microsoft Azure не предоставляет доступ с правами root для виртуальных машин Linux. Чтобы получить административный доступ при подключении в качестве пользователя, можно использовать `sudo –s`.

**Обновления** — вы можете установить обновления с помощью **zypper**, а также служебных программ NFS.

>[AZURE.IMPORTANT]В настоящее время мы не рекомендуем применять обновления ядра, которые могут вызвать проблемы с драйверами Linux RDMA.

**Intel MPI** — скачайте и установите среду выполнения библиотеки Intel MPI 5.0 с [сайта Intel.com](https://software.intel.com/ru-ru/intel-mpi-library/). После регистрации на сайте Intel перейдите по ссылке в письме с подтверждением на соответствующую веб-страницу и скопируйте ссылку для скачивания TGZ-файла для соответствующей версии Intel MPI.

Выполните следующему команду, чтобы установить Intel MPI на виртуальной машине:

```

$ wget <download link for your registration>
$ tar xvzf <tar-file>
$ cd <mpi-directory>
$ sudo ./install.sh
```

**Блокировка памяти** — чтобы код MPI заблокировал память для RDMA, необходимо добавить или изменить следующие параметры в файле /etc/security/limits.conf:

```
<User or group name> hard    memlock <memory required for your application in KB>
<User or group name> soft    memlock <memory required for your application in KB>
```

>[AZURE.NOTE]В целях тестирования можно также задать неограниченное значение для параметра memlock. Например, `<User or group name>    hard    memlock unlimited`.


**SSH-ключи** — установите отношение доверия для имени пользователя и пароль, которые применялись для создания этой виртуальной машины, со всеми вычислительными узлами в кластере. Создайте SSH-ключи следующей командой:

```
$ ssh-keygen
```

Сохраните открытый ключ в расположении по умолчанию и запомните введенную парольную фразу.

```
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Измените или создайте файл ssh\_config в каталоге ~/.ssh. Укажите диапазон IP-адресов частной сети, которая будет использоваться в Azure:

```
host 10.32.0.*
StrictHostKeyChecking no
```

Кроме того, можно перечислить IP-адрес частной сети каждой виртуальной машины в кластере следующим образом:

```
host 10.32.0.1
 StrictHostKeyChecking no
host 10.32.0.2
 StrictHostKeyChecking no
host 10.32.0.3
 StrictHostKeyChecking no
```

>[AZURE.NOTE]Настройка `StrictHostKeyChecking no` может создавать угрозу безопасности, если определенный IP-адрес или диапазон не задан, как показано выше.

**Приложения** — скопируйте все нужные приложения на эту виртуальную машину или выполните другие настройки перед записью образа.

### Запись образа

Чтобы записать образ, выполните следующую команду на виртуальной машине Linux:

```
$ sudo waagent -deprovision
```

Затем на клиентском компьютере выполните следующие команды Azure CLI для записи образа. Подробнее см. в статье [Запись образа виртуальной машины Linux для использования в качестве шаблона](virtual-machines-linux-capture-image.md).

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

После выполнения этих команд образ виртуальной машины будет записан, а виртуальная машина будет удалена. Теперь ваш образ готов для развертывания кластера.

### Развертывание кластера с помощью образа

Укажите соответствующие вашей среде значения в следующем скрипте и запустите его на клиентском компьютере. Так как в режиме ASM виртуальные машины развертываются последовательно, для выполнения этого скрипта и развертывания восьми виртуальных машин размера A9 потребуется несколько минут.

```
### Script to create a compute cluster without a scheduler in a VNet in Azure
### Create a custom private network in Azure
### Replace 10.32.0.0 with your virtual network address space
### Replace <network-name> with your network identifier
### Select a region where A8 and A9 VMs are available, such as West US
### See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 <network-name>

### Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
### Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

### Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

### Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

### In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i <image-name>
done

### Save this script and run it at the CLI prompt to provision your cluster
```

## Настройка и запуск Intel MPI

Для запуска приложений MPI в Azure Linux RDMA необходимо настроить определенные переменные среды, связанные с Intel MPI. Ниже приведен пример скрипта для настройки переменных и запуска приложения.

```
#!/bin/bash -x
source /opt/intel/impi_latest/bin64/mpivars.sh

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

Формат файла узла выглядит следующим образом. Добавьте одну строку для каждого узла в кластере. Укажите частные IP-адреса из виртуальной сети, определенной ранее, но не DNS-имена.

```
private ip address1:16 [e.g. 10.32.0.1:16]
private ip address2:16
...
```

## Проверка базового кластера из двух узлов после установки Intel MPI

Вы можете выполнить следующие команды Intel MPI, чтобы проверить конфигурацию кластера.

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>, <host2> -ppn 1 -n 2 -env I_MPI_FABRICS dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
```

В работающем кластере с двумя узлами вы должны увидеть результаты, аналогичные приведенным ниже:

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

## Аспекты топологии сети

* На виртуальных машинах Linux Eth1 зарезервировано RDMA сетевого трафика. Не изменяйте параметры Eth1 и любые данные в файле конфигурации при обращении к этой сети.

* IP через Infiniband (IB) не поддерживается в Azure. Поддерживается только RDMA через IB.

* На виртуальных машинах Linux Eth0 зарезервирован для обычного сетевого трафика Azure.

## Дальнейшие действия

* Попробуйте развернуть и запустить приложения MPI в кластере Linux.

* Рекомендации по Intel MPI см. в [документации для библиотеки Intel MPI](https://software.intel.com/ru-ru/articles/intel-mpi-library-documentation/).

<!---HONumber=August15_HO9-->
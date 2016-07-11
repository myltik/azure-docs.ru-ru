<properties
 pageTitle="Кластер Linux RDMA для выполнения приложений MPI | Microsoft Azure"
 description="Создание кластера Linux виртуальных машин A8 или A9, чтобы использовать RDMA для запуска приложений MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="05/09/2016"
 ms.author="danlep"/>

# Настройка кластера Linux RDMA для выполнения приложений MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


В этой статье показано, как настроить кластер Linux RDMA в Azure с [виртуальными машинами A8 и A9](virtual-machines-linux-a8-a9-a10-a11-specs.md) для параллельного выполнения приложений MPI. При настройке кластера виртуальных машин A8 и A9 для выполнения поддерживаемого дистрибутива Linux HPC и поддерживаемой реализации MPI приложения MPI будут эффективно взаимодействовать по сети Azure с низкой задержкой и высокой пропускной способностью, которая основана на технологии удаленного доступа к памяти (RDMA).

>[AZURE.NOTE] Сейчас Azure Linux RDMA поддерживается библиотекой Intel MPI версии 5, которая запускается на виртуальных машинах A8 и A9, созданных на базе образа SUSE Linux Enterprise Server 12 (SLES) HPC или CentOS 6.5 или 7.1 HPC, которые доступны в Azure Marketplace. Образы CentOS HPC из Marketplace устанавливают Intel MPI версии 5.1.3.181.



## Варианты развертывания кластера

Ниже перечислены методы, которые можно использовать для создания кластера Linux RDMA с планировщиком заданий или без него.

* **Пакет HPC**. Можно создать кластер пакета Microsoft HPC в Azure и добавить вычислительные узлы размера A8 или A9 под управлением поддерживаемых дистрибутивов Linux для доступа к сети RDMA. См. [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

* **Сценарии интерфейса командной строки Azure**. Как показано далее в этой статье, можно использовать [интерфейс командной строки Azure](../xplat-cli-install.md), чтобы создавать сценарии для развертывания виртуальной сети и других необходимых компонентов для создания кластера виртуальных машин A8 или A9 под управлением Linux. В классической модели развертывания интерфейс командной строки в режиме управления службами создает узлы кластера последовательно, поэтому при развертывании множества вычислительных узлов для его завершения может потребоваться несколько минут.

* **Шаблоны Azure Resource Manager**. Используя модель развертывания Resource Manager, можно развернуть несколько виртуальных машин A8 и A9 под управлением Linux, а также определить виртуальные сети, статические IP-адреса, параметры DNS и другие ресурсы, чтобы создать вычислительный кластер, который может использовать все преимущества сети RDMA для выполнения рабочих нагрузок MPI. Для развертывания своего решения вы можете [создать собственный шаблон](../resource-group-authoring-templates.md) или изучить [страницу шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/), которая содержит шаблоны, созданные корпорацией Майкрософт и сообществом пользователей. Шаблоны диспетчера ресурсов — самый быстрый и надежный способ развертывания кластера Linux.

## Пример развертывания в классической модели

Далее показано, как использовать интерфейс командной строки Azure для развертывания виртуальной машины SUSE Linux Enterprise Server 12 HPC из Azure Marketplace, установки библиотеки Intel MPI и других настроек, создания настраиваемого образа виртуальной машины и сценария для развертывания кластера виртуальных машин A8 или A9.

>[AZURE.TIP]  Выполните аналогичные действия для развертывания кластера виртуальных машин A8 или A9, созданного с использованием образа CentOS 6.5 или 7.1 HPC в Azure Marketplace. Шаги будут отличаться. Например, так как образы CentOS HPC включают в себя Intel MPI, отдельно устанавливать Intel MPI на виртуальные машины, созданные из этих образов, не требуется.

### Предварительные требования

*   **Клиентский компьютер** — вам необходим клиентский компьютер под управлением Mac, Linux или Windows для взаимодействия с Azure. Далее предполагается, что вы используете клиент Linux.

*   **Подписка Azure**. Если ее нет, можно за пару минут создать [бесплатную учетную запись](https://azure.microsoft.com/free/). Для больших кластеров можно использовать подписку с оплатой по мере использования или другие варианты приобретения.

*   **Квота ядер** — вам может потребоваться увеличить квоту ядер для развертывания кластера виртуальных машин A8 или A9. Например, для развертывания восьми виртуальных машин A9 необходимо не меньше 128 ядер, как показано в этой статье. Чтобы увеличить квоту, [отправьте запрос в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Это бесплатно.

*   **Интерфейс командной строки Azure**. [Установите](../xplat-cli-install.md) интерфейс командной строки Azure и [подключитесь к подписке Azure](../xplat-cli-connect.md) с клиентского компьютера.

*   **Intel MPI**. В рамках настройки образа виртуальной машины SLES 12 HPC для своего кластера (подробно далее в этой статье) необходимо скачать и установить текущую среду выполнения Intel MPI Library 5 с [сайта Intel.com](https://software.intel.com/ru-RU/intel-mpi-library/). Для этого после регистрации на сайте Intel перейдите по ссылке в письме с подтверждением на соответствующую веб-страницу и скопируйте ссылку для скачивания TGZ-файла для соответствующей версии Intel MPI. В этой статье используется Intel MPI версии 5.0.3.048.

    >[AZURE.NOTE] При использовании образа CentOS 6.5 или CentOS 7.1 HPC в Azure Marketplace для создания узлов кластера Intel MPI версии 5.1.3.181 автоматически предварительно устанавливается на виртуальной машине.

### Подготовка виртуальной машины SLES 12

После входа в Azure с помощью интерфейса командной строки Azure выполните команду `azure config list` и убедитесь, что в выводе отображается режим управления службами. Если это не так, активируйте режим, выполнив следующую команду:


    azure config mode asm


Введите следующую команду, чтобы перечислить все подписки, которые вы можете использовать:


    azure account list

Для текущей активной подписки значение `Current` установлено в `true`. Если это не та подписка, которую вы хотите использовать для создания кластера, установите в качестве активной подписки соответствующий номер подписки:

    azure account set <subscription-number>

Для просмотра общедоступных образов SLES 12 HPC в Azure выполните следующую команду, если среда оболочки поддерживает **grep**:


    azure vm image list | grep "suse.*hpc"

Теперь настройте виртуальную машину размера A9 с доступным образом SLES 12 HPC, выполнив следующую команду:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

где:

* размер (в данном примере — A9) может быть A8 или A9;

* внешний номер порта SSH (в данном примере — 22, номер порта SSH по умолчанию) — любой допустимый номер порта; внутренний номер порта SSH будет установлен в 22;

* новая облачная служба будет создан в регионе Azure, заданной расположением; укажите расположение, например "Запад США", в котором доступны экземпляры A8 и A9;

* в качестве имени образа SLES 12 сейчас можно выбрать `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` или `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` для поддержки приоритетов SUSE (платно).

    >[AZURE.NOTE]Если вы хотите использовать образ CentOS HPC, текущими именами образов могут быть `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` и `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`.

### Настройка виртуальной машины

После завершения подготовки виртуальной машины установите SSH-подключение к виртуальной машине с помощью ее внешнего IP-адреса (или DNS-имени) и внешнего порта номер, который вы указали, и настройте ее. Подробнее о подключении см. в разделе [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-classic-log-on.md). Команды необходимо выполнять от имени пользователя, настроенного на виртуальной машине, если только для выполнения шага не нужен доступ с правами root.

>[AZURE.IMPORTANT]Microsoft Azure не предоставляет доступ с правами root для виртуальных машин Linux. Чтобы получить административный доступ при подключении к виртуальной машине в качестве обычного пользователя, можно выполнять команды с использованием `sudo`.

* **Обновления**. Установите обновления с помощью **zypper**. Также можно установить служебные программы NFS.

    >[AZURE.IMPORTANT]Если развернута виртуальная машина SLES 12 HPC, в настоящее время рекомендуется не применять обновления ядра. Они могут вызвать проблемы с драйверами Linux RDMA.
    >
    >В образах CentOS HPC, доступных в Marketplace, обновления ядра отключены в файле конфигурации **yum**. Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не сработать в случае обновления ядра.

* **Обновления драйверов Linux RDMA**. Если развернута виртуальная машина SLES 12 HPC, нужно обновить драйверы RDMA. Дополнительные сведения см. в статье [об использовании экземпляров A8, A9, A10 и A11 для ресурсоемких вычислений](virtual-machines-linux-a8-a9-a10-a11.md#Linux-RDMA-driver-updates-for-SLES-12).

* **Intel MPI**. Если развернута виртуальная машина SLES 12 HPC, скачайте и установите среду выполнения Intel MPI Library 5 с сайта Intel.com, выполнив команды, аналогичные указанным ниже. Этот шаг необязательный, если развернута виртуальная машина HPC на основе CentOS 6.5 или 7.1.

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    Примите параметры по умолчанию для установки Intel MPI на виртуальной машине.

* **Блокировка памяти** — чтобы код MPI заблокировал память для RDMA, необходимо добавить или изменить следующие параметры в файле /etc/security/limits.conf: (Для изменения этого файла потребуется доступ с правами root.) Если развернута виртуальная машина HPC на основе CentOS 6.5 или 7.1, то параметры уже добавлены в этот файл.

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]В целях тестирования можно также задать неограниченное значение для параметра memlock. Например: `<имя пользователя или группы> hard memlock unlimited.

* **Ключи SSH для виртуальных машин SLES 12**. Создайте ключи SSH, чтобы установить доверие для учетной записи пользователя среди всех вычислительных узлов в кластере пакета HPC SLES 12 при выполнении заданий MPI. (Если развернута виртуальная машина HPC на основе CentOS, не выполняйте этот шаг. Инструкции по установке доверия SSH без пароля для узлов кластера после записи образа и развертывания кластера см. далее в статье.)

    Для создания ключей SSH выполните следующую команду. Просто нажмите клавишу ВВОД, чтобы создать ключи в расположении по умолчанию без указания парольной фразы.

        ssh-keygen

    Добавьте открытый ключ в файл authorized\_keys для известных открытых ключей.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Измените или создайте файл config в каталоге ~/.ssh. Укажите диапазон IP-адресов частной сети, которая будет использоваться в Azure (в данном примере, 10.32.0.0/16):

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

    >[AZURE.NOTE]Настройка `StrictHostKeyChecking no` может создавать угрозу безопасности, если определенный IP-адрес или диапазон не задан, как показано в этих примерах.

* **Приложения** — Установите все нужные приложения на эту виртуальную машину или выполните другие настройки перед записью образа.

### Запись образа

Чтобы записать образ, выполните следующую команду на виртуальной машине Linux: При этом виртуальная машина отзывается, но учетные записи пользователей и ключи SSH, которые вы настроили, сохраняются.

```
sudo waagent -deprovision
```

Затем на клиентском компьютере выполните следующие команды Azure CLI для записи образа. Дополнительные сведения см. в статье [Запись классической виртуальной машины Linux в виде образа](virtual-machines-linux-classic-capture-image.md).

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

После выполнения этих команд образ виртуальной машины будет записан, а виртуальная машина будет удалена. Теперь ваш образ готов для развертывания кластера.

### Развертывание кластера с помощью образа

Укажите соответствующие вашей среде значения в следующем скрипте Bash и запустите его на клиентском компьютере. Так как в классической модели развертывания Azure развертывает виртуальные машины последовательно, то для выполнения этого сценария и развертывания восьми виртуальных машин размера A9 потребуется несколько минут.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
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

## Доверие SSH без пароля для кластера CentOS

Если для развертывания кластера использовался образ CentOS HPC, существуют два метода установки доверия между вычислительными узлами: проверка подлинности на основе узлов и пользовательская проверка подлинности. В этой статье не рассматривается проверка подлинности на основе узлов, которая обычно должна осуществляться с помощью сценария расширения во время развертывания. После развертывания для установки доверия удобно применять пользовательскую проверку подлинности. Для этого нужно создать ключи SSH для их совместного использования вычислительными узлами в кластере. Такой метод, называемый входом через SSH без пароля, необходим для выполнения заданий MPI.

На сайте [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) можно найти предоставленный сообществом пользователей пример сценария для включения аутентификации пользователей в кластере HPC на основе CentOS. Его можно скачать и использовать, выполнив действия ниже. Вы можете также изменить этот сценарий или использовать любой другой метод, чтобы настроить проверку подлинности через SSH без пароля между вычислительными узлами кластера.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Чтобы запустить сценарий, потребуется префикс IP-адресов подсети. Его можно получить, выполнив следующую команду на одном из узлов кластера. Вывод должен выглядеть примерно как 10.1.3.5, где 10.1.3 — это префикс.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Сценарий можно выполнить, используя три параметра: общее для вычислительных узлов имя пользователя, общий для вычислительных узлов пароль пользователя и префикс подсети, который был возвращен из предыдущей команды.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Скрипт выполняет следующее:

* Создает каталог с именем .ssh на главном узле, который требуется для входа без пароля.
* Создает в каталоге .ssh файл конфигурации, который настраивает вход без пароля, чтобы разрешить вход из любого узла в кластере.
* Создает файлы, содержащие имена и IP-адреса всех узлов в кластере. После выполнения сценария эти файлы остаются для последующего использования.
* Создает пару из закрытого и открытого ключа для каждого узла кластера, включая главный узел, предоставляет сведения о паре ключей и создает запись в файле authorized\_keys.

>[AZURE.WARNING]Выполнение этого сценария может создать угрозу безопасности. Не допускайте распространения сведений об открытом ключе в каталоге ~/.ssh.


## Настройка и запуск Intel MPI

Для запуска приложений MPI в Azure Linux RDMA необходимо настроить определенные переменные среды, связанные с Intel MPI. Ниже приведен пример скрипта Bash для настройки переменных и запуска приложения. Измените путь к файлу mpivars.sh в соответствии с вашей установкой Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

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

## Проверка базового кластера из двух узлов после установки Intel MPI

Если это еще не сделано, сначала настройте среду для Intel MPI.

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### Запуск простой команды MPI

Выполните простую команду MPI на одном из вычислительных узлов, чтобы убедиться, что MPI установлена правильно и по крайней мере два вычислительных узла могут взаимодействовать друг с другом. Следующая команда **mpirun** запускает команду **hostname** на двух узлах.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
В выходных данных должны быть перечислены имена всех узлов, переданные в качестве входных данных для `-hosts`. Например, команда **mpirun** с двумя узлами возвращает выходные данные следующего вида:

```
cluster11
cluster12
```

### Запуск теста производительности MPI

Следующая команда Intel MPI проверяет конфигурацию кластера и подключение к сети RDMA с помощью тестирования pingpong.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

В работающем кластере с двумя узлами вы должны увидеть результаты, аналогичные приведенным ниже. В сети RDMA Azure для сообщения размером до 512 байт ожидается задержка на уровне 3 микросекунд или ниже.

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



## Дальнейшие действия

* Попробуйте развернуть и запустить приложения MPI в кластере Linux.

* Рекомендации по Intel MPI см. в [документации для библиотеки Intel MPI](https://software.intel.com/ru-RU/articles/intel-mpi-library-documentation/).

* Чтобы создать кластер Intel Lustre с помощью образа пакета HPC на основе CentOS, воспользуйтесь [шаблоном быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos).

<!---HONumber=AcomDC_0629_2016-->
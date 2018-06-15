---
title: Выполнение заданий STAR-CCM+ с помощью пакета HPC на виртуальных машинах Linux | Документация Майкрософт
description: Развертывание кластера Microsoft HPC в Azure и запуск задания STAR-CCM+ на нескольких вычислительных узлах Linux в сети RDMA.
services: virtual-machines-linux
documentationcenter: ''
author: xpillons
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: 8689d7abfd5ab45277df3b5672a1f6e7e874d88e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841493"
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Выполнение заданий STAR-CCM+ в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC
В этой статье показано, как развернуть кластер пакета Microsoft HPC в Azure и запустить задание [STAR-CCM+ CD-adapco](http://www.cd-adapco.com/products/star-ccm%C2%AE) на нескольких вычислительных узлах Linux, соединенных с помощью InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Пакет Microsoft HPC предоставляет функции, необходимые для работы приложений высокопроизводительных и параллельных вычислений, включая приложения MPI, в кластерах виртуальных машин Microsoft Azure. Пакет HPC также поддерживает приложения высокопроизводительных вычислений для Linux на виртуальных вычислительных узлах Linux, развернутых в кластере HPC. Общие сведения об использовании вычислительных узлов Linux и пакета HPC см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Настройка кластера пакета HPC
Скачайте сценарии развертывания IaaS из пакета HPC из [Центра загрузки](https://www.microsoft.com/en-us/download/details.aspx?id=44949) и извлеките их в локальное расположение.

В системе должен быть установлен компонент Azure PowerShell. Если Azure PowerShell не настроен на локальном компьютере, то см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview).

На момент написания этой статьи образы Linux из Azure Marketplace (включая драйверы InfiniBand для Azure) предназначены для SLES 12, CentOS 6.5 и CentOS 7.1. В этой статье используется SLES 12. Чтобы получить имена всех образов Linux, поддерживающих HPC в Marketplace, можно выполнить следующую команду PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

На экран будут выведены расположение этих образов и имя образа (**ImageName**), который будет использоваться в шаблоне развертывания позже.

Перед развертыванием кластера необходимо создать файл шаблона развертывания пакета HPC. Поскольку мы намереваемся создать небольшой кластер, головной узел будет контроллером домена и на нем будет размещаться локальная база данных SQL.

Этот головной узел можно развернуть с помощью следующего шаблона. Создайте XML-файл с именем **MyCluster.xml** и задайте для параметров **SubscriptionId**, **StorageAccount**, **Location**, **VMName** и **ServiceName** собственные значения.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Создайте головной узел, выполнив команду PowerShell в командной строке с повышенными привилегиями:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Создание головного узла обычно занимает 20–30 минут. Вы можете подключиться к нему на портале Azure, щелкнув значок **подключения** виртуальной машины.

Со временем может потребоваться исправить DNS-сервер пересылки. Для этого необходимо запустить диспетчер DNS.

1. Щелкните правой кнопкой мыши имя сервера в диспетчере DNS, выберите пункт **Свойства** и перейдите на вкладку **Серверы пересылки**.
2. Нажмите кнопку **Изменить**, чтобы удалить серверы пересылки, а затем нажмите кнопку **ОК**.
3. Убедитесь, что установлен флажок **Использовать корневые ссылки, если нет доступных серверов пересылки**, и нажмите кнопку **ОК**.

## <a name="set-up-linux-compute-nodes"></a>Настройка вычислительных узлов Linux
Развертывание вычислительных узлов Linux выполняется с помощью того же шаблона развертывания, который использовался для создания головного узла.

Скопируйте файл **MyCluster.xml** с локального компьютера в головной узел и укажите для тега **NodeCount** число узлов, которые требуется развернуть (не более 20). Убедитесь, что в квотах Azure достаточно доступных ядер, так как для каждого экземпляра A9 в подписке используется 16 ядер. Если вы хотите увеличить число виртуальных машин в рамках того же бюджета, вместо A9 можно использовать экземпляры A8 (8 ядер).

В головном узле скопируйте сценарии развертывания IaaS из пакета HPC.

В командной строке с повышенными привилегиями выполните следующие команды Azure PowerShell:

1. Выполните командлет **Add-AzureAccount** , чтобы подключиться к подписке Azure.
2. При наличии нескольких подписок выполните командлет **Get-AzureSubscription** , чтобы получить их список.
3. Установите подписку по умолчанию, выполнив команду **Select-AzureSubscription -SubscriptionName xxxx -Default** .
4. Выполните команду **.\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml**, чтобы запустить развертывание вычислительных узлов Linux.
   
   ![Развертывание головного узла][hndeploy]

Откройте диспетчер кластера пакета HPC. Через несколько минут вычислительные узлы Linux станут периодически появляться в списке вычислительных узлов кластера. В режиме классического развертывания виртуальные машины IaaS создаются последовательно. Поэтому, если важно количество узлов, развертывание всех машин может занять значительное время.

![Узлы Linux в диспетчере кластера пакета HPC][clustermanager]

Теперь, когда все узлы работают в кластере, нужно настроить дополнительные параметры инфраструктуры.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Настройка общей папки Azure для узлов Windows и Linux
Службу файлов Azure можно использовать для хранения сценариев, пакетов приложений и файлов данных. Служба файлов Azure предоставляет возможности CIFS на базе хранилища BLOB-объектов Azure как постоянного хранилища. Это не самое масштабируемое решение, но оно самое простое и не требует выделенных виртуальных машин.

Создайте общую папку Azure, следуя инструкциям в статье [Приступая к работе с хранилищем файлов Azure в Windows](../../../storage/files/storage-dotnet-how-to-use-files.md).

Оставьте **saname** в качестве имени учетной записи хранения, **sharename** — в качестве имени общей папки и **sakey** — в качестве ключа учетной записи хранения.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Подключение общей папки Azure в головном узле
Откройте командную строку с повышенными привилегиями и выполните команду, указанную ниже, чтобы сохранить учетные данные в локальном хранилище компьютера.

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Затем, чтобы подключить общую папку Azure, выполните следующую команду:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Подключение общей папки Azure в вычислительных узлах Linux
В пакете HPC содержится один полезный инструмент — средство clusrun. Это средство командной строки позволяет выполнить одну и ту же команду одновременно на нескольких вычислительных узлах. В нашем случае оно используется для подключения общей папки Azure и ее сохранения после перезагрузки.
Выполните следующие команды в головном узле в командной строке с повышенными привилегиями.

Чтобы создать каталог подключения:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Чтобы подключить общую папку Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Чтобы сохранить общую папку подключения:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Установка STAR-CCM+
Экземпляры виртуальной машины Azure A8 и A9 обеспечивают поддержку InfiniBand и возможности RDMA. Драйверы ядра, обеспечивающие эти возможности, доступны для образов Windows Server 2012 R2, SUSE 12, CentOS 6.5 и CentOS 7.1 в Azure Marketplace. Microsoft MPI и Intel MPI (выпуск 5.x) — это две библиотеки MPI, которые поддерживают эти драйверы в среде Azure.

В состав пакета STAR-CCM+ CD-adapco (11.x и более поздней версии) входит библиотека Intel MPI версии 5.x, что обеспечивает поддержку InfiniBand для Azure.

Скачайте пакет Linux64 STAR-CCM+ с [портала CD-adapco](https://steve.cd-adapco.com). В нашем случае мы использовали версию 11.02.010 в режиме смешанной точности.

На головном узле в общей папке Azure **/hpcdata** создайте сценарий оболочки **setupstarccm.sh** со следующим содержимым. Этот сценарий будет выполняться на каждом вычислительном узле для локальной настройки STAR-CCM+.

#### <a name="sample-setupstarcmsh-script"></a>Пример сценария setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Чтобы настроить STAR-CCM+ на всех вычислительных узлах Linux, откройте командную строку с повышенными привилегиями и выполните следующую команду:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Во время выполнения команды можно отслеживать загрузку ЦП на тепловой карте диспетчера кластеров. Через несколько минут все узлы будут настроены должным образом.

## <a name="run-star-ccm-jobs"></a>Запуск заданий STAR-CCM+
Пакет HPC используется в качестве планировщика заданий для выполнения заданий STAR-CCM+. Для этого требуется обеспечить поддержку нескольких сценариев, которые используются для запуска задания и выполнения STAR-CCM+. Для упрощения входные данные хранятся в общей папке Azure.

Следующий сценарий PowerShell применяется для помещения задания STAR-CCM+ в очередь. Он принимает три аргумента:

* имя модели;
* количество используемых узлов;
* число используемых ядер на каждом узле.

Так как STAR-CCM+ может использовать всю пропускную способность памяти, как правило, лучше использовать меньше ядер на один вычислительный узел и добавлять новые узлы. Точное число ядер на каждый узел будет зависеть от семейства процессора и скорости взаимодействия.

Узлы выделяются исключительно для конкретного задания, и их не могут совместно использовать другие задания. Задание не запускается как задание MPI напрямую. Сценарий оболочки **runstarccm.sh** запустит средство запуска MPI.

Модель ввода и сценарий **runstarccm.sh** хранятся в общей папке **/hpcdata**, подключенной ранее.

Файлам журнала присваиваются имена с использованием идентификаторов заданий. Файлы журнала и выходные файлы STAR-CCM+ хранятся в **общей папке /hpcdata**.

#### <a name="sample-submitstarccmjobps1-script"></a>Пример сценария SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Замените **runner.java** предпочтительным средством запуска моделей Java и кодом ведения журнала STAR-CCM+.

#### <a name="sample-runstarccmsh-script"></a>Пример сценария runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

В рамках теста мы использовали маркер лицензии увеличения мощности по требованию. Для этого маркера переменной среды **$CDLMD_LICENSE_FILE** необходимо присвоить значение **1999@flex.cd-adapco.com**. Кроме того, нужно задать ключ в параметре командной строки **-podkey**.

После инициализации сценарий извлечет из переменных среды **$CCP_NODES_CORES**, заданных в пакете HPC, список узлов для создания файла узла, используемого средством запуска MPI. Этот файл узла будет содержать список имен вычислительных узлов, используемых для задания (по одному имени в строке).

Формат **$CCP_NODES_CORES** соответствует следующему шаблону:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Описание

* `<Number of nodes>` — количество узлов, выделенных для этого задания.
* `<Name of node_n_...>` — имя каждого узла, выделенного для этого задания.
* `<Cores of node_n_...>` — количество ядер узла, выделенного для этого задания.

Число ядер (**$NBCORES**) рассчитывается на основе числа узлов (**$NBNODES**) и числа ядер на узел, заданного в параметре **$NBCORESPERNODE**.

Для Intel MPI в среде Azure используются следующие параметры MPI:

* `-mpi intel` для указания Intel MPI;
* `-fabric UDAPL` для использования команд Azure InfiniBand;
* `-cpubind bandwidth,v` для оптимизации пропускной способности для MPI со STAR-CCM+;
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` для настройки работы Intel MPI с Azure InfiniBand и задания необходимого числа ядер на узел;
* `-batch` для запуска STAR-CCM+ в пакетном режиме без пользовательского интерфейса.

Наконец, чтобы запустить задание, убедитесь, что узлы работают и подключены в диспетчере кластера. В командной строке PowerShell выполните следующую команду:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Остановка узлов
Чтобы останавливать и запускать узлы после завершения тестов, можно выполнить следующие команды PowerShell пакета HPC:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Дополнительная информация
Попробуйте запустить другие рабочие нагрузки Linux. Например, ознакомьтесь со следующими статьями:

* [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](hpcpack-cluster-namd.md)
* [Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png

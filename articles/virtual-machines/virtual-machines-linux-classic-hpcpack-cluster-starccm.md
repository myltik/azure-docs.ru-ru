<properties
 pageTitle="Выполнение заданий StarCCM+ с помощью пакета HPC на виртуальных машинах Linux | Microsoft Azure"
 description="Развертывание кластера Microsoft HPC в Azure и запуск задания StarCCM+ на нескольких вычислительных узлах Linux в сети RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="kateh"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/13/2016"
 ms.author="xpillons"/>

# Выполнение заданий StarCCM+ в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC
В этой статье показано, как развернуть кластер пакета Microsoft HPC в Azure и запустить задание [CD-Adapco StarCCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) на нескольких вычислительных узлах Linux, соединенных с помощью Infiniband.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Пакет Microsoft HPC предоставляет функции, необходимые для работы приложений высокопроизводительных и параллельных вычислений, включая приложения MPI, в кластерах виртуальных машин Microsoft Azure. Пакет HPC также поддерживает приложения высокопроизводительных вычислений для Linux на виртуальных вычислительных узлах Linux, развернутых в кластере HPC. Общие сведения об использовании вычислительных узлов Linux и пакета HPC см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Настройка кластера пакета HPC
Скачайте сценарии развертывания IaaS из пакета HPC [отсюда](https://www.microsoft.com/ru-RU/download/details.aspx?id=44949) и извлеките их локально.

Вам потребуется Azure Powershell. Если он не настроен в вашем локальном компьютере, см. сведения в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

На момент написания этой статьи образы Linux из коллекции Azure, которая содержит драйверы Infiniband для Azure, используются для SLES 12, CentOS 6.5 и CentOS 7.1. В этой статье используется SLES 12. Чтобы получить имена всех образов Linux, поддерживающих HPC в коллекции, можно выполнить следующую команду PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

На экран будут выведены расположение этих образов и параметр **ImageName**, который будет использоваться в шаблоне развертывания позже. Перед развертыванием кластера необходимо создать файл шаблона для развертывания пакета HPC. Так как мы хотим развернуть небольшой кластер, в качестве головного узла будет использоваться контроллер домена, в котором будет находиться локальная база данных SQL. Этот головной узел можно развернуть с помощью шаблона ниже. Создайте XML-файл с именем **MyCluster.xml** и задайте для параметров **SubscriptionId**, **StorageAccount**, **Location**, **VMName** и **ServiceName** собственные значения.

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

Создайте головной узел, выполнив команду PowerShell в командном окне с повышенными привилегиями:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Головной узел будет создан через 20–30 мин. Вы можете подключиться к нему на портале Azure, щелкнув значок подключения виртуальной машины.

Со временем может потребоваться исправить DNS-сервер пересылки. Для этого необходимо запустить диспетчер DNS.

1.  Щелкните правой кнопкой мыши имя сервера в диспетчере DNS, выберите пункт "Свойства" и перейдите на вкладку "Серверы пересылки".

2.  Нажмите кнопку "Изменить", чтобы удалить серверы пересылки, а затем нажмите кнопку "ОК".

3.  Установите флажок "Использовать корневые ссылки, если нет доступных серверов пересылки" и нажмите кнопку "ОК".

## Настройка вычислительных узлов Linux
Для развертывания вычислительных узлов Linux используется тот же шаблон развертывания, что и для создания головного узла. Скопируйте файл **MyCluster.xml** с локального компьютера в головной узел и укажите для тега **NodeCount** число узлов, которые требуется развернуть (не более 20). Убедитесь, что в квотах Azure достаточно доступных ядер, так как для каждого экземпляра A9 в подписке используется 16 ядер. Если вы хотите увеличить число виртуальных машин в рамках того же бюджета, вместо A9 можно использовать экземпляры A8 (8 ядер).

В головном узле скопируйте сценарии развертывания IaaS из пакета HPC.

Откройте Azure Powershell в командном окне с повышенными привилегиями:

1.  Выполните командлет **Add-AzureAccount**, чтобы подключиться к подписке Azure.

2.  При наличии нескольких подписок выполните командлет **Get-AzureSubscription**, чтобы получить их список.

3.  Установите подписку по умолчанию, выполнив команду **Select-AzureSubscription -SubscriptionName xxxx -Default**.

4.  Выполните команду **.\\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml**, чтобы запустить развертывание вычислительных узлов Linux. ![Развертывание головного узла][hndeploy]

Откройте средство диспетчера кластеров пакета HPC. Через несколько минут вычислительные узлы Linux станут периодически появляться в списке вычислительных узлов кластера. В режиме классического развертывания виртуальные машины IaaS создаются последовательно. Таким образом, чтобы развернуть определенное количество узлов может потребоваться немало времени. ![Узлы Linux в диспетчере кластера пакета HPC][clustermanager]

Теперь, когда все узлы работают в кластере, нужно настроить дополнительные параметры инфраструктуры.

## Настройка общей папки Azure для узлов Windows и Linux
Для упрощения работы мы используем файлы Azure, которые предоставляют возможности CIFS для больших двоичных объектов Azure и используются в качестве постоянного хранилища, в котором можно хранить сценарии, пакеты приложений и файлы данных. Это не самое масштабируемое решение, но оно самое простое и не требует выделенных виртуальных машин.

Создайте общую папку Azure, следуя инструкциям в статье [Приступая к работе с хранилищем файлов Azure в Windows](..\storage\storage-dotnet-how-to-use-files.md).

Оставьте **saname** в качестве имени учетной записи хранения, **sharename** — в качестве имени общей папки и **sakey** — в качестве ключа учетной записи хранения.

### Подключение общей папки Azure в головном узле
Откройте командную строку с повышенными привилегиями и выполните команду, указанную ниже, чтобы сохранить учетные данные в локальном хранилище компьютера.

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Затем, чтобы подключить общую папку Azure, выполните следующую команду:

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### Подключение общей папки Azure в вычислительных узлах Linux
В пакете HPC содержится один полезный инструмент — служебная программа clusrun. Она позволяет выполнить одну и ту же команду одновременно на нескольких вычислительных узлах. В нашем случае она используется для подключения общей папки Azure и ее сохранения после перезагрузки. Выполните следующие команды в головном узле в командном окне с повышенными привилегиями.

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

## Обновление драйверов Linux
Со временем вам понадобится обновить драйверы Infiniband вычислительных узлов Linux. Сведения о том, нужно ли это делать и как это делать, см. в разделе [Обновление драйверов Linux RDMA для SLES 12](virtual-machines-linux-classic-rdma-cluster.md/#update-the-linux-rdma-drivers-for-sles-12).

## Установка StarCCM+
Экземпляры виртуальной машины Azure A8 и A9 обеспечивают поддержку Infiniband и возможности RDMA. На момент написания этой статьи драйверы ядра, позволяющие использовать эти возможности, доступны для образов Windows 2012 R2 и SUSE 12 в коллекции Azure. В скором времени станут доступны драйверы ядра для CentOS 7.x. Microsoft MPI и Intel MPI (выпуск 5.x) — это две библиотеки MPI, которые поддерживают эти драйверы в среде Azure.

В состав пакета CD-Adapco StarCCM+ (11.x и более поздней версии) входит библиотека Intel MPI версии 5.x, что обеспечивает поддержку Infiniband для Azure.

Скачайте пакет Linux64 StarCCM+ с [портала CD-Adapco](https://steve.cd-adapco.com). Мы использовали версию 11.02.010 со смешанной точностью.

В головном узле в общей папке Azure **/hpcdata** создайте сценарий оболочки **setupstarccm.sh** с содержимым, как показано ниже. Этот сценарий будет выполнен на каждом вычислительном узле для локальной настройки StarCCM+.

#### Пример сценария setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh setup StarCCM+ locally

    # create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # copy the starccm package from the file share to the local dir
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # start a silent installation of starccm without flexlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Чтобы настроить StarCCM+ на всех вычислительных узлах Linux, откройте командное окно с повышенными привилегиями и выполните следующую команду:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Во время выполнения команды можно отслеживать загрузку ЦП на тепловой карте диспетчера кластеров. Через несколько минут все узлы будут настроены должным образом.

## Выполнение заданий StarCCM+
Пакет HPC используется в качестве планировщика заданий для выполнения заданий StarCCM+. Для этого требуется обеспечить поддержку нескольких сценариев, которые используются для запуска задания и выполнения StarCCM+. Для упрощения входные данные хранятся в общей папке Azure. Следующий сценарий Powershell применяется для помещения задания StarCCM+ в очередь. Он принимает 3 аргумента:

*  имя модели;
*  количество используемых узлов;
*  число используемых ядер на каждом узле.

Так как StarCCM + может использовать всю пропускную способность памяти, как правило, лучше использовать меньше ядер на один вычислительный узел и добавлять новые узлы. Точный коэффициент ядер на каждый узел будет зависеть от семейства процессоров и скорости межсоединения.

Узлы выделяются исключительно для конкретного задания, и их не могут совместно использовать другие задания. Как видно, задание не запускается непосредственно как задание MPI. Средство запуска MPI будет запущено из сценария оболочки **runstarccm.sh**.

Модель ввода и **runstarccm.sh** должны храниться в общей папке **/hpcdata**, подключенной ранее.

Файлам журнала присваиваются имена с использованием идентификаторов заданий. Файлы журнала и выходные файлы StarCCM+ хранятся в **общей папке /hpcdata**.


#### Пример сценария SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # create a new job, this will give us the JobId used to identify the name of the uploaded package in azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # submit job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Замените **runner.java** предпочтительным средством запуска моделей Java и кодом ведения журнала StarCCM+.

#### Пример сценария runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set mpirun runtime environment
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

    # Run the starccm with hostfile arg
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

В рамках теста мы использовали маркер лицензии увеличения мощности по требованию. Для переменной среды **$CDLMD\_LICENSE\_FILE** этого маркера необходимо задать значение ****1999@flex.cd-adapco.com**. Кроме того, нужно задать ключ в параметре **-podkey** командной строки.

После инициализации сценарий извлечет из переменных среды **$CCP\_NODES\_CORES**, заданных в пакете HPC, список узлов для создания файла узла, используемого средством запуска MPI. Этот файл узла будет содержать список имен вычислительных узлов, используемых для задания (по одному имени в строке).

Формат **$CCP\_NODES\_CORES** соответствует следующему шаблону:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

где:

* `<Number of nodes>`: количество узлов, выделенных для этого задания.  
* `<Name of node_n_...>`: имя каждого узла, выделенного для этого задания.
* `<Cores of node_n_...>`: количество ядер каждого узла, выделенного для этого задания.

Число ядер **$NBCORES** рассчитывается на основе числа узлов **$NBNODES** и числа ядер на узел, заданного в параметре **$NBCORESPERNODE**.

Для Intel MPI в среде Azure используются следующие параметры MPI:

*   -mpi intel => для указания IntelMPI;
*   -fabric UDAPL => для использования команд Azure Infiniband;
*   -cpubind bandwidth,v => для оптимизации пропускной способности MPI с StarCCM+;
*   -mppflags "-ppn $NBCORESPERNODE -genv I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0 -genv I\_MPI\_DAPL\_UD=0 -genv I\_MPI\_DYNAMIC\_CONNECTION=0" => это параметры Intel MPI, которые следует использовать в Azure Infiniband. Они задают требуемое число ядер на узел.
*   -batch => для запуска StarCCM+ в пакетном режиме без пользовательского интерфейса.


Наконец, чтобы запустить задание, убедитесь, что узлы работают и подключены в диспетчере кластера. В командной строке PowerShell выполните следующую команду:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## Остановка работы узлов
Чтобы останавливать и запускать узлы после завершения тестов, можно выполнить следующие команды PowerShell пакета HPC:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## Дальнейшие действия
Попробуйте запустить другие рабочие нагрузки Linux. Например, просмотрите следующие статьи:

* [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0427_2016-->
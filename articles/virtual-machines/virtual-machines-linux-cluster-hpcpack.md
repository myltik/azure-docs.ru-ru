<properties
 pageTitle="Вычислительные виртуальные машины на базе Linux в кластере пакета HPC | Microsoft Azure"
 description="Узнайте, как создать сценарий развертывания кластера пакета HPC в Azure, содержащего головной узел под управлением Windows Server с вычислительными узлами Linux."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="12/02/2015"
 ms.author="danlep"/>

# Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure

В этой статье показано, как использовать сценарий Azure PowerShell для настройки в Azure кластера пакета Microsoft HPC, содержащего головной узел под управлением Windows Server и несколько вычислительных узлов под управлением дистрибутива Linux. Мы также покажем вам несколько способов перемещения файлов с данными в вычислительные узлы Linux. Этот кластер можно использовать для запуска рабочих нагрузок Linux HPC в Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


На верхнем уровне следующей схемы показан создаваемый кластер пакета HPC.

![Кластер HPC с узлами Linux][scenario]

## Развертывание кластера пакета HPC с вычислительными узлами Linux

Вы будете использовать сценарий развертывания IaaS пакета Microsoft HPC (**New-HpcIaaSCluster.ps1**) для автоматизации развертывания кластера в службах инфраструктуры Azure (IaaS). Этот сценарий Azure PowerShell использует образ виртуальной машины их пакета HPC в Azure Marketplace для быстрого развертывания и предоставляет исчерпывающий набор параметров конфигурации, обеспечивающий легкость и гибкость развертывания. Этот сценарий позволяет выполнить развертывание виртуальной сети Azure, учетных записей хранения, облачных служб, контроллера домена, необязательного отдельного сервера баз данных SQL Server, головного узла кластера, вычислительных узлов, узлов брокеров, («расширительных») узлов Azure PaaS и вычислительных узлов Linux (поддержка Linux появилась в [пакете HPC 2012 R2 с обновлением 2](https://technet.microsoft.com/library/mt269417.aspx)).

Общие сведения о вариантах развертывания кластера пакета HPC см. в [руководстве по началу работы с пакетом HPC 2012 R2 и пакетом HPC 2012](https://technet.microsoft.com/library/jj884144.aspx), а также в статье [Варианты создания кластера высокопроизводительных вычислений (HPC) в Azure и управления им с помощью пакета Microsoft HPC](virtual-machines-hpcpack-cluster-options.md).

### Предварительные требования

* **Клиентский компьютер**. Вам потребуется клиентский компьютер под управлением Windows для запуска сценария развертывания кластера.

* **Azure PowerShell**. [Установите и настройте на клиентском компьютере Azure PowerShell](../powershell-install-configure.md) версии 0.8.10 или более поздней.

* **Сценарий развертывания IaaS из пакета HPC**. Скачайте и распакуйте последнюю версию сценария из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=44949). Вы можете проверить версию сценария, выполнив команду `New-HPCIaaSCluster.ps1 –Version`. Эта статья основана на сценарии версии 4.4.0 или выше.

* **Подписка Azure**. Вы можете использовать подписку на службу Azure Global или Azure China. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Квота ядер**. Возможно, вам потребуется увеличить квоту на число ядер, особенно если вы решите развернуть несколько узлов кластера с многоядерными виртуальными машинами. Для примера в этой статье необходимо по крайней мере 12 ядер. Чтобы увеличить квоту, [отправьте запрос в службу поддержки](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Это бесплатно.

### Создание файла конфигурации

Сценарий развертывания IaaS из пакета HPC использует входной XML-файл конфигурации, описывающий инфраструктуру кластера HPC. Чтобы развернуть небольшой кластер, состоящий из головного узла и 2 вычислительных узлов Linux, вставьте значения для вашей среды в следующий образец файла конфигурации. Дополнительные сведения о файле конфигурации см. в файле Manual.rtf в папке сценария или в статье [Создание высокопроизводительных вычислительных кластеров (HPC-кластеров) на виртуальных машинах Azure с помощью скрипта развертывания пакета HPC по модели IaaS](virtual-machines-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>A4</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

Ниже приводится краткое описание элементов файла конфигурации.

* **IaaSClusterConfig** — корневой элемент файла конфигурации.

* **Subscription** — подписка Azure, используемая для развертывания кластера пакета HPC. Используйте приведенную ниже команду, чтобы убедиться, что имя подписки на Azure настроено и является уникальным на клиентском компьютере. В этом примере используется подписка на Azure под названием Subscription-1.

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]Кроме того, нужную подписку можно указать с помощью ее идентификатора. См. файл Manual.rtf в папке сценария.

* **StorageAccount** — все постоянные данные кластера пакета HPC сохраняются в указанной учетной записи хранения (в этом примере — allvhdsje). Если такая учетная запись хранения не существует, сценарий создаст ее в регионе, указанном в элементе **Location**.

* **Location** — регион Azure, в котором развертывается кластер пакета HPC (в этом примере — восточная Япония).

* **VNet** — параметры виртуальной сети и подсети, где будет создан кластер HPC. Вы можете создать виртуальную сеть и подсеть самостоятельно перед запуском сценария, либо сценарий создаст виртуальную сеть с пространством адресов 192.168.0.0/20 и подсеть с пространством адресов 192.168.0.0/23. В этом примере сценарий создает виртуальную сеть centos7rdmavnetje и подсеть CentOS7RDMACluster.

* **Domain** — параметры домена Active Directory для кластера пакета HPC. Все виртуальные машины Windows, созданные сценарием, будут присоединены к домену. В настоящее время сценарий поддерживает три варианта домена: ExistingDC, NewDC и HeadNodeAsDC. В этом примере головной узел настроен как контроллер домена с полным доменным именем hpc.local.

* **Database** — параметры базы данных для кластера пакета HPC. В настоящее время сценарий поддерживает три варианта базы данных: ExistingDB, NewRemoteDB и LocalDB. В этом примере локальная база данных будет создана на головном узле.

* **HeadNode** — параметры головного узла пакета HPC. В этом примере головной узел размера A7 с именем CentOS7RDMA-HN будет создан в облачной службе centos7rdma-je. Для поддержки отправки заданий HPC с удаленных (не присоединенных к домену) клиентских компьютеров сценарий активирует REST API планировщика заданий HPC и веб-портал HPC.

* **LinuxComputeNodes** — параметры вычислительных узлов Linux для пакета HPC. В этом примере будут созданы два вычислительных узла CentOS 7 Linux размера A7 (CentOS7RDMA-LN1 и CentOS7RDMA-LN2) в облачной службе centos7rdma-je.

### Дополнительные замечания по вычислительным узлам Linux

* В настоящий момент пакет HPC поддерживает следующие дистрибутивы Linux для вычислительных узлов: Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 и SUSE Linux Enterprise Server 12.

* Пример в этой статье использует для создания кластера версию CentOS, доступную в Azure Marketplace. Если вам нужно использовать другие доступные образы, используйте командлет Azure PowerShell **get-azurevmimage**, чтобы найти нужный образ. Например, чтобы получить список всех образов CentOS 7.0, выполните следующую команду:

    ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Найдите нужный образ и замените значение **ImageName** в файле конфигурации.

* Доступны образы Linux, поддерживающие соединение RDMA для виртуальных машин размеров A8 и A9. Если указать образ с установленными драйверами RDMA для Linux, сценарий развертывания IaaS пакета HPC развернет их. Например, укажите имя образа `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` для текущей версии образа «SUSE Linux Enterprise Server 12 — оптимизированный для высокой производительности вычислений» в Marketplace.

* Чтобы включить Linux RDMA на виртуальных машинах Linux, созданных из поддерживаемых образов для выполнения заданий MPI, установите и настройте соответствующую библиотеку MPI на узлах Linux после развертывания кластера в соответствии с потребностями приложения. С примером можно ознакомиться в статье [Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](virtual-machines-linux-cluster-hpcpack-openfoam.md).

* Убедитесь, что все узлы Linux RDMA развертываются в одной службе, чтобы работало сетевое соединение RDMA между узлами.


## Запуск сценария развертывания IaaS пакета HPC

1. Откройте консоль PowerShell на клиентском компьютере от имени администратора.

2. Измените каталог на папку сценария (E:\\IaaSClusterScript в этом примере).

    ```
    cd E:\IaaSClusterScript
    ```

3. Выполните приведенную ниже команду, чтобы развернуть кластер пакета HPC. В этом примере предполагается, что путь к файлу конфигурации — E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    Сценарий создает файл журнала автоматически, так как параметр **-LogFile** не указан. Журналы записываются не в реальном времени, но собираются по завершении проверки и развертывания, поэтому, если процесс PowerShell будет остановлен во время работы сценария, то некоторые журналы будут потеряны.

    а. Поскольку элемент **AdminPassword** не указан в приведенной выше команде, вам будет предложено ввести пароль для пользователя *MyAdminName*.

    b. Затем сценарий начинает проверку файла конфигурации. Это занимает от десятков секунд до нескольких минут, в зависимости от сетевого подключения.

    ![Проверка][validate]

    c. Завершив проверки, сценарий выводит на экран список ресурсов, которые будут созданы в кластере HPC. Для продолжения введите *Y*.

    ![Ресурсы][resources]

    г) Сценарий начинает развертывание кластера пакета HPC и завершает настройку без дальнейшего участия пользователя. Это может занять несколько минут.

    ![Развертывание][deploy]

4. После успешного завершения настройки перейдите на удаленный рабочий стол головного узла и откройте диспетчер кластера HPC, чтобы проверить состояние кластера пакета HPC. Вы можете управлять вычислительными узлами Linux и отслеживать их так же, как и вычислительными узлами Windows. Например, узлы Linux отображаются в разделе "Управление узлами".

    ![Управление узлами][management]

    Кроме того, узлы Linux показаны в представлении "Тепловая карта".

    ![Тепловая карта][heatmap]

## Перемещение данных в кластере с узлами Linux

Существует несколько вариантов перемещения данных между узлами Linux и головным узлом Windows кластера. Ниже приведены три распространенных метода.

* **Файл Azure** — предоставляет общую папку для хранения файлов с данными в службе хранилища Azure. Как узлы Windows, так и узлы Linux могут монтировать общую папку Azure одновременно как диск или папку, даже если они развертываются в разных виртуальных сетях.

* **Общая папка SMB головного узла** — монтирует общую папку головного узла на узлы Linux.

* **Сервер NFS головного узла** — предоставляет решение для обмена файлами для смешанной среды Windows и Linux.

### Хранилище файлов Azure

Служба [файлов Azure](https://azure.microsoft.com/services/storage/files/) предоставляет общие папки с помощью стандартного протокола SMB 2.1. Виртуальные машины Azure могут использовать файловые данные компонентов приложений через подключенные ресурсы, а локальные приложения получают доступ к этим данным совместно с помощью API хранилища файлов. Дополнительные сведения см. в статье [Использование хранилища файлов Azure в Windows](../storage/storage-dotnet-how-to-use-files.md).

Чтобы создать общую папку Azure, см. подробное руководство в статье [Знакомство со службой файлов Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Чтобы настроить сохраняемые подключения, см. статью [Сохраняемые подключения службе файлов Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

В этом примере мы создаем общую папку Azure с именем rdma в учетной записи хранения allvhdsje. Чтобы подключить общую папку на головном узле, откройте окно командной строки и введите следующие команды:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

В этом примере allvhdsje — это имя учетной записи хранения, storageaccountkey — ключ этой учетной записи, а rdma — имя общей папки Azure. Общая папка Azure будет смонтирована на диск Z: головного узла.

Чтобы подключить общую папку службы файлов Azure на узлах Linux, выполните команду **clusrun** на головном узле. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** — это полезное средство пакета HPC для выполнения административных задач на нескольких узлах. (См. также раздел [CLusrun для узлов Linux](#CLusrun-for-Linux-nodes) в этой статье.)

Откройте окно Windows PowerShell и введите следующие команды.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Первая команда создает папку под названием /rdma на всех узлах в группе LinuxNodes. Вторая команда монтирует общую папку Azure allvhdsjw.file.core.windows.net/rdma в папку /rdma и задает для битов каталога и режима файлов значение 777. Во второй команде allvhdsje — это имя учетной записи хранения, а storageaccountkey — ее ключ.

>[AZURE.NOTE]Символ "`" во второй команде — это escape-символ для PowerShell. "`," означает, что "," (запятая) является частью команды.

### Общая папка головного узла

Вы также можете монтировать общую папку головного узла на узлы Linux. Это самый простой способ предоставления доступа к файлам, но головной узел и все узлы Linux должны быть развернуты в одной виртуальной сети. Для этого выполните следующие действия.

1. Создайте папку на головном узле и сделайте ее доступной всем для чтения и записи. Например, откройте доступ к папке D:\\OpenFOAM на головном узле как \\CentOS7RDMA-HN\\OpenFOAM. Здесь CentOS7RDMA-HN — это имя головного узла.

    ![Разрешения общих папок][fileshareperms]

    ![Общий доступ к файлам][filesharing]

2. Откройте окно Windows PowerShell и выполните следующие команды для подключения к общей папке.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

Первая команда создает папку /openfoam на всех узлах в группе LinuxNodes. Вторая команда подключает общую папку //CentOS7RDMA-HN/OpenFOAM и задает для битов режима каталогов и папок значение 777. В команде должны быть указаны имя пользователя и пароль пользователя кластера на головном узле.

>[AZURE.NOTE]Символ "`" во второй команде — это escape-символ для PowerShell. "`," означает, что "," (запятая) является частью команды.


### Сервер NFS

Служба NFS позволяет пользователям обмениваться файлами и перемещать их между компьютерами под управлением операционной системы Windows Server 2012 с помощью протокола SMB и между компьютерами под управлением Linux с помощью протокола NFS. Сервер NFS и все остальные узлы должны быть развернуты в одной виртуальной сети. Он обеспечивает большую совместимость с узлами Linux, чем общая папка SMB. Например, он поддерживает связывание файлов.

1. Чтобы установить и настроить сервер NFS, следуйте указаниям из статьи [Сервер для первой общей папки сетевой файловой системы](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).

    К примеру, создадим общую папку NFS под названием nfs со следующими свойствами.

    ![Авторизация NFS][nfsauth]

    ![Разрешения общих папок NFS][nfsshare]

    ![Разрешения NTFS NFS][nfsperm]

    ![Свойства управления NFS][nfsmanage]

2. Откройте окно Windows PowerShell и выполните следующую команду для подключения к общей папке NFS.

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  Первая команда создает папку под названием /nfsshared на всех узлах в группе LinuxNodes. Вторая команда подключает общую папку NFS CentOS7RDMA-HN:/nfs к папке. Здесь CentOS7RDMA-HN:/nfs — это удаленный путь к общей папке NFS.

## Отправка заданий
Существует несколько способов отправки заданий в кластер пакета HPC.

* Пользовательский интерфейс диспетчера кластеров HPC или диспетчера заданий HPC

* Веб-портал HPC

* Интерфейс REST API

Отправка заданий в кластер в Azure с помощью пользовательского интерфейса пакета HPC и веб-портала HPC аналогична отправке заданий для вычислительных узлов Windows. См. статьи [Диспетчер заданий пакета HPC](https://technet.microsoft.com/library/ff919691.aspx) и [Отправка заданий HPC c локального компьютера в кластер на основе пакета HPC в Azure](virtual-machines-hpcpack-cluster-submit-jobs.md).

Сведения об отправке заданий с помощью REST API см. в статье [Создание и отправка заданий с помощью REST API в пакете Microsoft HPC](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). См. также пример на языке Python в [пакете SDK HPC](https://www.microsoft.com/download/details.aspx?id=47756) для отправки заданий с клиента Linux.

## Clusrun для узлов Linux

Средство **clusrun** пакета HPC можно использовать для выполнения команд на узлах Linux с помощью командной строки или диспетчера кластеров HPC. Ниже приводится несколько простых примеров.

* Отобразите имена текущих пользователей на всех узлах кластера.

    ```
    > clusrun whoami
    ```

* Установите средство отладки **gdb** с помощью команды **yum** на всех узлах в группе linuxnodes и перезапустите узлы через 10 минут.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* Создайте сценарий оболочки, который отображает каждое число в диапазоне от 1 до 10 в течение секунды на каждом узле Linux в кластере, запустите его и сразу же отобразите выходные данные с этих узлов.

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE]Возможно, в командах **clusrun** потребуется использовать определенные escape-символы. Как показано в данном примере, используйте ^ в окне командной строки для экранирования символа ">" .

## Дальнейшие действия

* Попробуйте масштабировать кластер, чтобы увеличить количество узлов, или запустите в кластере рабочую нагрузку Linux. Пример см. в статье [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-cluster-hpcpack-namd.md).

* Попробуйте использовать кластер с вычислительными узлами размера [A8 или A9](virtual-machines-a8-a9-a10-a11-specs.md) для запуска рабочих нагрузок MPI. С примером можно ознакомиться в статье [Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](virtual-machines-linux-cluster-hpcpack-openfoam.md).

* Попробуйте использовать [шаблон быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) совместно с диспетчером ресурсов Azure, чтобы ускорить развертывание пакета HPC при большом количестве вычислительных узлов Linux.

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=AcomDC_1210_2015-->
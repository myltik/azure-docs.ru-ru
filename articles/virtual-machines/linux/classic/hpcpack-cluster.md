---
title: Вычислительные виртуальные машины на базе Linux в кластере пакета HPC | Документация Майкрософт
description: Узнайте, как создать и использовать кластер пакета HPC в Azure для выполнения в Linux рабочих нагрузок высокопроизводительных вычислительных систем (HPC).
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 57ad5d5d2e7e068f47d51408527f1f7553917279
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841699"
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure
В этой статье описывается, как настроить в Azure [кластер пакета Microsoft HPC](https://technet.microsoft.com/library/cc514029.aspx), содержащий головной узел под управлением Windows Server и несколько вычислительных узлов под управлением поддерживаемого дистрибутива Linux. Кроме того, вы ознакомитесь с несколькими вариантами перемещения данных между узлами Linux и головным узлом Windows кластера, а также узнаете, как отправлять задания Linux HPC в кластер.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

На следующей обобщенной схеме показан кластер пакета HPC, который вы создадите для работы.

![Кластер пакета HPC с узлами Linux][scenario]

Чтобы изучить другие варианты выполнения высокопроизводительных рабочих нагрузок HPC для Linux в Azure, ознакомьтесь с [техническими материалами по пакетным и высокопроизводительным вычислениям](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Развертывание кластера пакета HPC с вычислительными узлами Linux
В этой статье описаны два варианта развертывания кластера пакета HPC в Azure с вычислительными узлами на основе Linux. В обоих методах для создания головного узла используется образ Windows Server с пакетом HPC из Azure Marketplace. 

* **Шаблон Azure Resource Manager**. Чтобы автоматизировать процесс создания кластера в модели развертывания с помощью Resource Manager, используйте шаблон из Azure Marketplace или шаблон для быстрого начала работы от сообщества. Например, шаблон [кластера пакета HPC для рабочих нагрузок Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) из Azure Marketplace создает полную инфраструктуру кластера пакета HPC для рабочих нагрузок высокопроизводительных вычислительных систем Linux.
* **Скрипт PowerShell**. Используйте [скрипт развертывания IaaS пакета Microsoft HPC](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) для автоматизации полного развертывания кластера в рамках классической модели развертывания. Этот скрипт Azure PowerShell использует образ виртуальной машины пакета HPC в Azure Marketplace для быстрого развертывания и предоставляет исчерпывающий набор параметров конфигурации для развертывания вычислительных узлов Linux.

Общие сведения о вариантах развертывания кластера пакета HPC в Azure см. в статье [Варианты создания кластера высокопроизводительных вычислений (HPC) в Azure и управления им с помощью пакета Microsoft HPC](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>предварительным требованиям
* **Подписка Azure**. Вы можете использовать подписку на глобальную службу Azure или Azure для Китая. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.
* **Квота ядер**. Возможно, вам потребуется увеличить квоту на число ядер, особенно если вы решите развернуть несколько узлов кластера с многоядерными виртуальными машинами. Чтобы увеличить квоту, отправьте запрос в службу поддержки. Плата за это обращение не взимается.
* **Дистрибутивы Linux**. В настоящее время пакет HPC поддерживает следующие дистрибутивы Linux для вычислительных узлов. Можно использовать версии этих дистрибутивов, доступные в Marketplace, или создать собственные.
  
  * **Выпуски на основе CentOS**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 для HPC, SLES 12 для HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Чтобы использовать сеть Azure RDMA с виртуальной машиной, размер которой поддерживает RDMA, укажите образ SUSE Linux Enterprise Server 12 HPC или образ CentOS для HPC из каталога Azure Marketplace. Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Ниже перечислены дополнительные компоненты, необходимые для развертывания кластера с помощью скрипта развертывания IaaS пакета HPC.

* **Клиентский компьютер**. Для запуска скрипта развертывания кластера вам потребуется клиентский компьютер под управлением Windows.
* **Azure PowerShell** - [установите и настройте Azure PowerShell](/powershell/azure/overview) (версии 0.8.10 или более поздней) на клиентском компьютере.
* **Сценарий развертывания IaaS из пакета HPC** — скачайте и распакуйте последнюю версию сценария из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=44949). Вы можете проверить версию скрипта, выполнив команду `.\New-HPCIaaSCluster.ps1 –Version`. В этой статье используется скрипт версии 4.4.1 или более поздней версии.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Вариант развертывания 1. Использование шаблона Resource Manager
1. Выберите в Azure Marketplace шаблон [кластера пакета HPC для рабочих нагрузок Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) и нажмите кнопку **Развернуть**.
2. На портале Azure проверьте данные и щелкните **Создать**.
   
    ![Создание на портале][portal]
3. В колонке **Основные данные** введите имя кластера, которое также является именем виртуальной машины на головном узле. Для развертывания можно указать существующую группу ресурсов или создать новую в доступном вам расположении. Расположение влияет на доступность определенных размеров виртуальных машин и других служб Azure (см. статью [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/)).
4. В колонке **Параметры головного узла** при первом развертывании обычно можно принять параметры по умолчанию. 
   
   > [!NOTE]
   > **URL-адрес скрипта пост-конфигурации** — это необязательный параметр, который указывает общедоступный скрипт Windows PowerShell, выполняемый на головном узле виртуальной машины после ее запуска. 
   > 
   > 
5. В колонке **Compute node settings** (Параметры вычислительных узлов) выберите шаблон имени для узлов, их количество и размер, а также дистрибутив Linux для развертывания.
6. В колонке **Параметры инфраструктуры** введите имена виртуальной сети и домена Active Directory, учетные данные администратора домена и виртуальной машины, а также формат имени для учетных записей хранения.
   
   > [!NOTE]
   > Пакет HPC использует домен Active Directory для проверки подлинности пользователей кластера. 
   > 
   > 
7. После выполнения проверок и ознакомления с условиями использования щелкните **Приобрести**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Вариант развертывания 2. Использование скрипта развертывания IaaS
Ниже перечислены дополнительные компоненты, необходимые для развертывания кластера с помощью скрипта развертывания IaaS пакета HPC:

* **Клиентский компьютер**. Для запуска скрипта развертывания кластера вам потребуется клиентский компьютер под управлением Windows.
* **Azure PowerShell** - [установите и настройте Azure PowerShell](/powershell/azure/overview) (версии 0.8.10 или более поздней) на клиентском компьютере.
* **Сценарий развертывания IaaS из пакета HPC** — скачайте и распакуйте последнюю версию сценария из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=44949). Вы можете проверить версию скрипта, выполнив команду `.\New-HPCIaaSCluster.ps1 –Version`. В этой статье используется скрипт версии 4.4.1 или более поздней версии.

**XML-файл конфигурации**

Скрипт развертывания IaaS из пакета HPC использует входной XML-файл конфигурации для описания кластера HPC. В следующем примере в файле конфигурации определен небольшой кластер, состоящий из головного узла пакета HPC и двух вычислительных узлов CentOS 7.0 Linux размера A7. 

Измените этот файл в соответствии с параметрами своей среды и необходимой конфигурацией кластера. Сохраните его, например, как HPCDemoConfig.xml. Например, вам нужно указать имя подписки, уникальное имя учетной записи хранения и имя облачной службы. Кроме того, для вычислительных узлов может потребоваться выбрать другой поддерживаемый образ Linux. Дополнительные сведения об элементах файла конфигурации см. в файле Manual.rtf в папке скрипта или в статье [Создание кластера HPC с помощью скрипта развертывания IaaS из пакета HPC](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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
  <VMSize>ExtraLarge</VMSize>
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

**Запуск скрипта развертывания IaaS из пакета HPC**

1. Откройте Windows PowerShell на клиентском компьютере от имени администратора.
2. Перейдите в каталог, в котором установлен скрипт (в данном случае E:\IaaSClusterScript).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Выполните следующую команду, чтобы развернуть кластер пакета HPC. В этом примере предполагается, что путь к файлу конфигурации — E:\HPCDemoConfig.xml.
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Поскольку элемент **AdminPassword** не указан в приведенной выше команде, вам предлагается ввести пароль для пользователя *MyAdminName*.
   
    Б. Затем скрипт начинает проверку файла конфигурации. Это занимает до нескольких минут, в зависимости от сетевого подключения.
   
    ![Проверка][validate]
   
    c. Завершив проверки, скрипт выводит на экран список ресурсов кластера, которые необходимо создать. Для продолжения введите *Y* .
   
    ![Ресурсы][resources]
   
    d. Скрипт начинает развертывание кластера пакета HPC и завершает настройку без дальнейшего участия пользователя. Выполнение скрипта может занять несколько минут.
   
    ![Развертывание][deploy]
   
   > [!NOTE]
   > В этом примере сценарий создает файл журнала автоматически, так как параметр **-LogFile** не указан. Хотя журналы не ведутся в режиме реального времени, они будут собраны в конце проверки и развертывания. Если процесс PowerShell остановлен во время выполнения скрипта, некоторые журналы будут утеряны.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Подключение к головному узлу
Когда развертывание кластера пакета HPC в Azure будет завершено, [подключитесь к виртуальной машине головного узла с помощью удаленного рабочего стола](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя учетные данные домена, которые вы указали при развертывании кластера (например, *hpc\\clusteradmin*). Управление кластером осуществляется из головного узла.

На головном узле запустите диспетчер кластеров HPC, чтобы проверить состояние кластера пакета HPC. Вы можете управлять вычислительными узлами Linux и отслеживать их так же, как и вычислительными узлами Windows. Например, в разделе **Управление ресурсами** вы видите узлы Linux (эти узлы развернуты с использованием шаблона **LinuxNode**).

![Управление узлами][management]

Вы также видите узлы Linux в представлении **Тепловая карта** .

![Тепловая карта][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Перемещение данных в кластере с узлами Linux
Существует несколько вариантов перемещения данных между узлами Linux и головным узлом Windows кластера. Ниже приведены три распространенных метода, которые более подробно описаны в следующих разделах:

* **Файл Azure** предоставляет управляемую общую папку SMB для хранения файлов с данными в службе хранилища Azure. Узлы Windows и Linux могут подключить общую папку Azure одновременно как диск или папку, даже если они развертываются в разных виртуальных сетях.
* **Общая папка SMB головного узла** подключает расположенную на головном узле стандартную общую папку Windows к узлам Linux.
* **Сервер NFS головного узла** — предоставляет решение для обмена файлами для смешанной среды Windows и Linux.

### <a name="azure-file-storage"></a>Хранилище файлов Azure
Служба [файлов Azure](https://azure.microsoft.com/services/storage/files/) предоставляет общие папки с помощью стандартного протокола SMB 2.1. Виртуальные машины Azure могут использовать файловые данные компонентов приложений через подключенные ресурсы, а локальные приложения получают доступ к этим данным совместно с помощью API хранилища файлов. 

Подробное описание создания и подключения общей папки Azure на головном узле см. в статье [Приступая к работе с хранилищем файлов Azure в Windows](../../../storage/files/storage-how-to-use-files-windows.md). Сведения о подключении общей папки Azure к узлам Linux см. в статье [Использование хранилища файлов Azure в Linux](../../../storage/files/storage-how-to-use-files-linux.md). Чтобы настроить сохраняемые подключения, см. статью [Сохраняемые подключения к файлам Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

В следующем примере создайте файловый ресурс Azure в учетной записи хранения. Чтобы подключить общую папку на головном узле, откройте окно командной строки и введите следующие команды:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

В этом примере allvhdsje — это имя учетной записи хранения, storageaccountkey — ключ этой учетной записи, а rdma — имя файлового ресурса папки Azure. Общая папка Azure подключается на диск Z: головного узла.

Чтобы подключить общую папку Azure на узлах Linux, выполните команду **clusrun** на головном узле. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** — это удобное средство в составе пакета HPC для выполнения административных задач на нескольких узлах. (См. также раздел [Clusrun для узлов Linux](#Clusrun-for-Linux-nodes) в этой статье.)

Откройте окно Windows PowerShell и введите следующие команды:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Первая команда создает папку под названием /rdma на всех узлах в группе LinuxNodes. Вторая команда монтирует общую папку Azure allvhdsjw.file.core.windows.net/rdma в папку /rdma и задает для битов каталога и режима файлов значение 777. Во второй команде allvhdsje — это имя учетной записи хранения, а storageaccountkey — ее ключ.

> [!NOTE]
> Символ \"\`\" во второй команде — это escape-символ для PowerShell. "\`," означает, что "," (запятая) является частью команды.
> 
> 

### <a name="head-node-share"></a>Общая папка головного узла
Вы также можете монтировать общую папку головного узла на узлы Linux. Общая папка — это самый простой способ предоставления доступа к файлам, но головной узел и все узлы Linux должны быть развернуты в одной виртуальной сети. Для этого выполните следующие действия.

1. Создайте папку на головном узле и сделайте ее доступной всем для чтения и записи. Например, откройте доступ к папке D:\OpenFOAM на головном узле как \\CentOS7RDMA-HN\OpenFOAM. Здесь CentOS7RDMA-HN — это имя головного узла.
   
    ![Разрешения общих папок][fileshareperms]
   
    ![Общий доступ к файлам][filesharing]
2. Откройте окно Windows PowerShell и выполните следующие команды:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Первая команда создает папку /openfoam на всех узлах в группе LinuxNodes. Вторая команда подключает общую папку //CentOS7RDMA-HN/OpenFOAM и задает для битов режима каталогов и папок значение 777. В команде должны быть указаны имя пользователя и пароль пользователя кластера на головном узле. (См. раздел [Добавление и удаление пользователей кластера](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> Символ \"\`\" во второй команде — это escape-символ для PowerShell. "\`," означает, что "," (запятая) является частью команды.
> 
> 

### <a name="nfs-server"></a>Сервер NFS
Служба NFS позволяет пользователям обмениваться файлами и перемещать их между компьютерами под управлением операционной системы Windows Server 2012 с помощью протокола SMB, а также между компьютерами под управлением Linux с помощью протокола NFS. Сервер NFS и все остальные узлы должны быть развернуты в одной виртуальной сети. Он обеспечивает большую совместимость с узлами Linux, чем общая папка SMB. Например, он поддерживает ссылки на файлы.

1. Чтобы установить и настроить сервер NFS, следуйте указаниям из статьи [Сервер для общей папки сетевой файловой системы](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    К примеру, создайте общую папку NFS под названием nfs со следующими свойствами:
   
    ![Авторизация NFS][nfsauth]
   
    ![Разрешения общих папок NFS][nfsshare]
   
    ![Разрешения NTFS NFS][nfsperm]
   
    ![Свойства управления NFS][nfsmanage]
2. Откройте окно Windows PowerShell и выполните следующие команды:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   Первая команда создает папку под названием /nfsshared на всех узлах в группе LinuxNodes. Вторая команда подключает общую папку NFS CentOS7RDMA-HN:/nfs к папке. Здесь CentOS7RDMA-HN:/nfs — это удаленный путь к общей папке NFS.

## <a name="how-to-submit-jobs"></a>Отправка заданий
Есть несколько способов отправки заданий в кластер пакета HPC.

* Пользовательский интерфейс диспетчера кластеров HPC или диспетчера заданий HPC
* Веб-портал HPC
* ИНТЕРФЕЙС REST API

Отправка заданий в кластер в Azure с помощью пользовательского интерфейса пакета HPC и веб-портала HPC аналогична отправке заданий для вычислительных узлов Windows. См. статьи, посвященные [диспетчеру заданий пакета HPC](https://technet.microsoft.com/library/ff919691.aspx) и [отправке заданий с локального клиентского компьютера](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Сведения об отправке заданий с помощью REST API см. в статье [Создание и отправка заданий с помощью REST API в пакете Microsoft HPC](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Чтобы узнать, как отправлять задания с клиента Linux, см. пример на языке Python в [пакете SDK для пакета HPC](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun для узлов Linux
Средство [clusrun](https://technet.microsoft.com/library/cc947685.aspx) пакета HPC можно использовать для выполнения команд на узлах Linux с помощью командной строки или диспетчера кластеров HPC. Ниже приводится несколько простых примеров.

* Отобразите имена текущих пользователей на всех узлах кластера.
  
    ```command
    clusrun whoami
    ```
* Установите средство отладки **gdb** с помощью команды **yum** на всех узлах в группе linuxnodes и перезапустите узлы через 10 минут.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Создайте сценарий оболочки, который отображает каждое число в диапазоне от 1 до 10 в течение секунды на каждом узле Linux в кластере, запустите его и сразу же отобразите выходные данные с этих узлов.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Возможно, в командах **clusrun** потребуется использовать определенные escape-символы. Как показано в этом примере, используйте ^ в окне командной строки для экранирования символа >.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* Попробуйте масштабировать кластер, чтобы увеличить количество узлов, или запустите в кластере рабочую нагрузку Linux. Пример см. в статье [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](hpcpack-cluster-namd.md).
* Для запуска рабочих нагрузок MPI используйте кластер с [поддержкой RDMA и высокопроизводительных виртуальных машин](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). С примером можно ознакомиться в статье [Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](hpcpack-cluster-openfoam.md).
* Дополнительные сведения о работе с узлами Linux в локальном кластере пакета HPC см. в [руководстве TechNet](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png

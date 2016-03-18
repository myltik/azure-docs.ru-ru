<properties
 pageTitle="Запуск заданий OpenFOAM с помощью пакета HPC на виртуальных машинах Linux | Microsoft Azure"
 description="Развертывание кластера Microsoft HPC в Azure и запуск заданий OpenFOAM на нескольких вычислительных узлах Linux в сети RDMA."
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
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="11/25/2015"
 ms.author="danlep"/>

# Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC

В этой статье рассказывается, как развернуть кластер пакета Microsoft HPC в Azure и выполнить задания [OpenFoam](http://openfoam.com/) с помощью Intel MPI на нескольких вычислительных узлах Linux, подключенных к сети Azure с технологией удаленного доступа к памяти (RDMA).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.

OpenFOAM (англ. Open Field Operation and Manipulation) — это распространяемый бесплатно пакет программного обеспечения с открытым исходным кодом для вычислительной гидродинамики (CFD). Он широко используется в науке и технике, как в научных, так и в коммерческих организациях. Пакет содержит средства для сеточного разбиения (например, snappyHexMesh), включает средство распараллеливания расчетов для сложных геометрических объектов, а также функции предварительной и последующей обработки. Почти все процессы выполняются параллельно, что позволяет пользователям максимально задействовать ресурсы компьютера.

Пакет Microsoft HPC предоставляет функции, необходимые для работы приложений высокопроизводительных и параллельных вычислений, включая приложения MPI, в кластерах виртуальных машин Microsoft Azure. Начиная с версии Microsoft HPC 2012 R2 с обновлением 2, пакет HPC также поддерживает приложения высокопроизводительных вычислений для Linux на виртуальных вычислительных машинах узла Linux, развернутых в кластере HPC. Общие сведения об использовании вычислительных узлов Linux и пакета HPC см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-cluster-hpcpack.md).

>[AZURE.NOTE]В этой статье предполагается, что у вас есть опыт администрирования Linux и выполнения рабочих нагрузок MPI на кластерах Linux HPC.

## Предварительные требования

*   **Кластер HPC с вычислительными узлами Linux**. Предварительные требования и действия для развертывания кластера HPC с вычислительными узлами Linux в Azure с помощью сценария Azure PowerShell и образов пакета HPC (из Azure Marketplace) см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-cluster-hpcpack.md). Дополнительные замечания по использованию экземпляров A8 с большим объемом вычислений для доступа к сети RDMA в Azure см. в статье [Об экземплярах A8, A9, A10 и A11 с большим объемом вычислений](virtual-machines-a8-a9-a10-a11-specs.md).

    Ниже приведен пример XML-файла конфигурации, который можно использовать со сценарием для развертывания в Azure кластера HPC, состоящего из головного узла Windows Server 2012 A8 (размера R2) и двух вычислительных узлов SUSE Linux Enterprise Server 12 (размера А8). Вместо используемых в файле имен подписки и службы подставьте свои значения.

    >[AZURE.NOTE]Сейчас сети Linux RDMA в Azure поддерживаются только на виртуальных машинах, созданных из образа SUSE Linux Enterprise Server 12 с поддержкой RDMA, которые можно найти в Azure Marketplace (b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-sles-12-hpc-v20150708).

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>allvhdsje</StorageAccount>
      </Subscription>
      <Location>Japan East</Location>  
      <VNet>
        <VNetName>suse12rdmavnet</VNetName>
        <SubnetName>SUSE12RDMACluster</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>SUSE12RDMA-HN</VMName>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <NodeCount>2</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>
```

    **Дополнительные сведения**

    *   Развертывайте все вычислительные узлы Linux в пределах одной облачной службы для использования подключения к сети RDMA между узлами.

    *   Если после развертывания узлов Linux требуется создать SSH-подключение для выполнения дополнительных задач администрирования, перейдите на портал Azure, где можно найти сведения о SSH-подключении для каждой виртуальной машины Linux.
        
*   **Intel MPI**. Для запуска OpenFOAM на вычислительных узлах Linux в Azure вам потребуется среда выполнения Intel MPI Library 5, которую можно загрузить на сайте [Intel.com](https://software.intel.com/ru-RU/intel-mpi-library/). Затем установите Intel MPI на вычислительные узлы Linux. Для этого после регистрации на сайте Intel перейдите по ссылке в письме с подтверждением на соответствующую веб-страницу и скопируйте ссылку для скачивания TGZ-файла для соответствующей версии Intel MPI. В этой статье используется Intel MPI версии 5.0.3.048.

*   **Исходный пакет OpenFOAM.** Загрузите исходный пакет OpenFOAM для Linux на сайте [OpenFOAM Foundation](http://www.openfoam.org/download/source.php). В этой статье используется пакет версии 2.3.1, доступный для загрузки в виде файла OpenFOAM-2.3.1.tgz. Инструкции по распаковке и компиляции OpenFOAM на вычислительных узлах Linux приведены далее в этой статье.

*   **EnSight** (необязательно). Для просмотра результатов моделирования OpenFOAM загрузите и установите на головном узле кластера пакета HPC приложение [EnSight](https://www.ceisoftware.com/download/) для Windows, предназначенное для визуализации и анализа данных. Сведения о лицензировании и загрузке приведены на сайте EnSight.


## Настройка взаимного доверия между вычислительными узлами

Чтобы задание выполнялось на нескольких узлах Linux одновременно, сначала необходимо настроить взаимное доверие узлов (по протоколу **rsh** или **ssh**). При создании кластера HPC с помощью сценария развертывания Microsoft HPC IaaS сценарий автоматически настраивает постоянное взаимное доверие для указанной учетной записи администратора. Для учетных записей пользователей без прав администратора, создаваемых в домене кластера, взаимное доверие между узлами должно создаваться в момент назначения им задания и уничтожаться после завершения задания. Чтобы реализовать это для всех пользователей, укажите пару ключей RSA в кластере, который пакет HPC использует для установления отношения доверия.

### Создание пары ключей RSA

Чтобы создать пару ключей RSA, состоящую из открытого и закрытого ключей, достаточно выполнить команду Linux **ssh-keygen**.

1.	Войдите на компьютер под управлением Linux.

2.	Выполните следующую команду:

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE]В ходе выполнения команды используйте параметры по умолчанию (нажимайте клавишу **ВВОД**). Не вводите парольную фразу. При запросе пароля просто нажмите клавишу **ВВОД**.

    ![Создание пары ключей RSA][keygen]

3.	Измените каталог на ~/.ssh. Закрытый ключ хранится в файле id\_rsa, а открытый — в файле id\_rsa.pub.

    ![Открытые и закрытые ключи][keys]

### Добавление пары ключей в кластер HPC
1.	Подключитесь к головному узлу через протокол удаленного рабочего стола, используя учетную запись администратора пакета HPC (учетная запись администратора, настроенная при запуске сценария развертывания).

2. С помощью стандартных процедур Windows Server создайте учетную запись пользователя домена в домене Active Directory кластера. Например, на головном узле можно использовать инструмент Active Directory «Пользователи и компьютеры». В приведенных здесь примерах предполагается, что вы создали пользователя домена с именем hpclab\\hpcuser.

3.	Создайте файл с именем C:\\cred.xml и скопируйте в него данные ключей RSA. Пример этого файла можно найти в фалах примеров в конце этой статьи.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.	Откройте командную строку и введите следующую команду, чтобы задать учетные данные для учетной записи hpclab\\hpcuser. Используйте параметр **extendeddata**, чтобы передать имя файла C:\\cred.xml, созданного для данных ключей.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Эта команда успешно завершается без выходных данных. Задав учетные данные для учетных записей пользователей, необходимых для выполнения заданий, сохраните файл cred.xml в безопасном месте или удалите его.

5.	Если вы создали пару ключей RSA на одном из узлов Linux, не забудьте удалить ключи после завершения работы с ними. Пакет HPC не устанавливает взаимное доверие, если обнаруживает существующий файл id\_rsa или id\_rsa.pub.

>[AZURE.IMPORTANT]Мы не рекомендуем выполнять задания Linux от имени администратора кластера в общем кластере, так как задания, отправленные администратором, выполняются на узлах Linux под учетной записью root. Задание, отправленное пользователем без прав администратора, выполняется под локальной учетной записью пользователя Linux с тем же именем, что и у пользователя задания, а пакет HPC устанавливает взаимное доверие для этого пользователя Linux на всех узлах, выделенных для выполнения задания. Учетную запись пользователя Linux можно настроить вручную на узлах Linux перед выполнением задания, или пакет HPC автоматически создаст ее при отправке задания. Если учетную запись пользователя создает пакет HPC, она удаляется после завершения задания. Из соображений безопасности после завершения задания ключи на узлах удаляются.

## Настройка файлового ресурса для узлов Linux

В папке на головном узле настройте стандартный общий ресурс SMB. Затем подключите его на всех узлах Linux. Таким образом узлы Linux смогут обращаться к файлам приложения по одному и тому же пути. Вы можете также использовать другие параметры общего доступа, например общие файлы Azure (рекомендуется для большинства сценариев) или общие папки NFS. Подробные сведения о параметрах и этапах настройки общего доступа к файлам см. в статье [Начало работы с вычислительными узлами Linux в кластере HPC в Azure](virtual-machines-linux-cluster-hpcpack.md).

1.	Создайте папку на головном узле и сделайте ее общедоступной для чтения и записи. Например, откройте доступ к папке C:\\OpenFOAM на головном узле как \\SUSE12RDMA-HN\\OpenFOAM. Здесь *SUSE12RDMA-HN* является именем головного узла.

2.	Откройте окно Windows PowerShell и выполните следующие команды для подключения к общей папке.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>’`,dir_mode=0777`,file_mode=0777
    ```

Первая команда создает папку /openfoam на всех узлах в группе LinuxNodes. Вторая команда подключает общую папку //SUSE12RDMA-HN/OpenFOAM и задает для битов режимов dir\_mode и file\_mode на узлах Linux значение 777. Значения *username* и *password* должны соответствовать учетным данным пользователя на головном узле.

>[AZURE.NOTE]Символ «`» во второй команде — это escape-символ для PowerShell. «`,» означает, что запятая является частью команды.

## Установка пакетов MPI и OpenFOAM

Чтобы запустить OpenFOAM в виде задания MPI в сети RDMA, нужно скомпилировать пакет OpenFOAM с помощью библиотеки Intel MPI.

Сначала вам нужно выполнить несколько команд **clusrun**, чтобы установить библиотеки Intel MPI и OpenFOAM на все узлы Linux. Для доступа к файлам установки на разных узлах Linux используйте общий доступ к папке на головном узле.

>[AZURE.IMPORTANT]Приведенные действия по установке и компиляции являются примерами и требуют некоторых знаний в сфере администрирования Linux. Особенно это касается правильности установки зависимых компиляторов и библиотек. Возможно, в зависимости от версии Intel MPI и OpenFOAM вам потребуется изменить некоторые переменные среды или другие параметры. Дополнительные сведения см. в статьях [Руководство по установке библиотеки Intel MPI для Linux](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html) и [Установка исходного пакета OpenFOAM](http://www.openfoam.org/download/source.php).


### Установка пакета Intel MPI

Сохраните загруженный пакет установки библиотеки Intel MPI (в этом примере это файл l\_mpi\_p\_5.0.3.048.tgz) в папку C:\\OpenFoam на головном узле. Таким образом, другие узлы Linux получат доступ к этому файлу через /openfoam. Затем запустите **clusrun**, чтобы установить библиотеку Intel MPI на всех узлах Linux.

1.  Следующие команды копируют установочный пакет и извлекают его в расположение /opt/intel на каждом узле.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Для автоматической установки библиотеки Intel MPI используйте файл silent.cfg. Пример можно найти в файлах примеров в конце этой статьи. Поместите этот файл в общую папку /openfoam. Дополнительные сведения о файле silent.cfg см. в статье [Руководство по установке библиотеки Intel MPI для Linux. Автоматическая установка](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html#silentinstall).

    >[AZURE.TIP]Сохраните файл silent.cfg как текстовый файл, в котором для завершения строк используется нотация Linux (только LF, а не CR LF). Это гарантирует, что файл будет правильно работать на узлах Linux.

3.  Установка библиотеки Intel MPI в автоматическом режиме.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### Настройка MPI

Для тестирования вам следует добавить следующие строки в файл /etc/security/limits.conf на каждом из узлов Linux:

```
*               hard    memlock         unlimited
*               soft    memlock         unlimited
```

После обновления файла limits.conf перезапустите узлы Linux. Например, используйте следующую команду **clusrun**.

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

После перезапуска убедитесь, что общая папка доступна по адресу /openfoam.

### Компиляция и установка OpenFOAM

Сохраните загруженный исходный пакет OpenFOAM (в этом примере это файл OpenFOAM-2.3.1.tgz) в папку C:\\OpenFoam на головном узле. Таким образом другие узлы Linux получат доступ к этому файлу через /openfoam. Затем выполните команду **clusrun**, чтобы скомпилировать пакет OpenFOAM на всех узлах Linux.


1.  Создайте папку /opt/OpenFOAM на каждом узле Linux. Затем скопируйте в эту папку исходный пакет и распакуйте его.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Чтобы скомпилировать OpenFOAM с помощью библиотеки Intel MPI, сначала потребуется настроить некоторые переменные среды для Intel MPI и OpenFOAM. Для этого используйте сценарий bash с именем settings.sh. Пример можно найти в файлах примеров в конце этой статьи. Поместите этот файл (сохраненный с нотацией Linux для завершения строк) в общую папку /openfoam. Этот файл также содержит параметры для сред выполнения MPI и OpenFOAM, которые впоследствии будут использованы для выполнения задания OpenFOAM.

3. Установите зависимые пакеты, необходимые для компиляции OpenFOAM. В зависимости от дистрибутива Linux вам может потребоваться сначала добавить репозиторий. Выполните команду **clusrun**, аналогичную следующей:

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Для надлежащего выполнения команд подключайтесь к каждому узлу Linux по SSH, если это необходимо.

4.  Чтобы скомпилировать OpenFOAM, выполните следующую команду. Процесс компиляции займет некоторое время. Выходных данных достаточно много, поэтому используйте ключ **/interleaved**, чтобы отображать их с чередованием.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]Символ ` во второй команде является escape-символом для PowerShell. `& означает, что & является частью команды.

## Подготовка к запуску задания OpenFOAM

Теперь попробуем запустить задание MPI с названием sloshingTank3D (стандартный пример из пакета OpenFoam) на двух узлах Linux.

### Настройка среды выполнения

На головном узле откройте Windows PowerShell и запустите следующую команду, чтобы настроить среду выполнения MPI и OpenFOAM на всех узлах Linux. (Эта команда допустима только для SUSE Linux.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### Подготовка примера данных

Чтобы открыть доступ к файлам с других узлов Linux, используйте ранее настроенную папку общего доступа (подключается как /openfoam).

1.  Выполните подключение к одному из вычислительных узлов Linux по SSH.

2.  Если это еще не сделано, запустите следующую команду, чтобы настроить среду выполнения OpenFOAM.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Скопируйте пример sloshingTank3D в общую папку и перейдите к нему.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Если вы используете стандартные параметры, представленные в этом примере, выполнение может занять несколько десятков минут или больше. Поэтому для ускорения выполнения можно изменить некоторые параметры. Простым решением является изменение переменных шага deltaT и writeInterval в файле system/controlDict, который хранит все входные данные, связанные с управлением временем, чтением и записью данных в решении. Например, значение deltaT можно изменить с 0,05 на 0,5, а значение writeInterval — с 0,05 на 0,5.

    ![Изменение переменных шага][step_variables]

5.  Укажите нужные значения переменных в файле system/decomposeParDict. В этом примере используется 2 узла Linux с 8 ядрами, поэтому присвойте параметру numberOfSubdomains значение 16, а параметру n hierarchicalCoeffs — (1 1 16), что означает выполнение задания OpenFOAM с созданием 16 параллельных процессов. Дополнительные сведения о параллельном выполнении заданий OpenFOAM см. в [Руководстве пользователя OpenFOAM: 3.4. Запуск приложений в параллельном режиме](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Разделение процессов][decompose]

6.  Выполните следующие команды из каталога sloshingTank3D, чтобы подготовить пример данных.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  На головном узле вы увидите, что файлы с примерами данных копируются в папку C:\\OpenFoam\\sloshingTank3D. (C:\\OpenFoam — это общая папка на головном узле).

    ![Файлы данных на головном узле][data_files]

### Файл узлов для mpirun

На этом шаге вы создадите файл узлов (список вычислительных узлов), который будет использоваться командой **mpirun**.

1.	На одном из узлов Linux создайте новый файл с именем hostfile и поместите его в папку /openfoam. В результате к нему будет открыт доступ с каждого узла Linux (в расположении /openfoam/hostfile).

2.	В этом файле укажите имена ваших узлов Linux. В этом примере файл выглядит так:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Также вы можете создать этот файл в каталоге C:\\OpenFoam\\hostfile на головном узле. В таком случае сохраните его в виде текстового файла с нотацией Linux для завершения строк (только LF, а не CR LF). Это гарантирует, что файл будет правильно работать на узлах Linux.

    **Оболочка сценария bash**

    Если у вас есть несколько узлов Linux, а задания будут выполняться только на некоторых из них, использовать фиксированный файл узлов не рекомендуется, так как вы не будете знать, какие узлы будут выделены для выполнения задания. В этом случае можно создать оболочку для сценария bash, которая будет использоваться командой **mpirun** для автоматического создания файла узлов. Пример оболочки для сценария bash можно найти в файле hpcimpirun.sh, входящем в файлы примеров в конце этой статьи. Сохраните этот файл в расположении /openfoam/hpcimpirun.sh. Этот пример сценария выполняет следующие действия.

    1.	Используя сеть RDMA, устанавливает переменные среды для команды **mpirun** и некоторые дополнительные параметры для выполнения заданий MPI. В данном примере устанавливаются следующие значения параметров:

        *	I\_MPI\_FABRICS=shm:dapl
        *	I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0
        *	I\_MPI\_DYNAMIC\_CONNECTION=0

    2.	Создает файл узлов в соответствии с переменной среды $CCP\_NODES\_CORES. Значение этой переменной устанавливается головным узлом HPC при активации задания.

        Формат $CCP\_NODES\_CORES соответствует следующему шаблону:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        где:

        * `<Number of nodes>`: количество узлов, выделенных для этого задания.  
        
        * `<Name of node_n_...>`: имя каждого узла, выделенного для этого задания.
        
        * `<Cores of node_n_...>`: количество ядер каждого узла, выделенного для этого задания.

        Например, если для выполнения задания требуется 2 узла, значение $CCP\_NODES\_CORES будет выглядеть примерно так:
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.	Вызывает команду **mpirun** и добавляет 2 параметра командной строки.

        * `--hostfile <hostfilepath>: <hostfilepath>`: путь к файлу узлов, который будет создан сценарием

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`: переменная среды, задаваемая головным узлом пакета HPC. Хранит общее количество ядер, выделенных для этого задания. В этом случае указывается количество процессов для команды **mpirun**.


## Отправка задания OpenFOAM

Теперь вы можете отправить задание из диспетчера кластера HPC. Для некоторых задач вам потребуется указать в командной строке сценарий hpcimpirun.sh.

1. Подключитесь к головному узлу кластера и запустите диспетчер кластера HPC.

2. В разделе **Управление ресурсами** убедитесь, что для вычислительных узлов Linux отображается состояние **В сети**. Если это не так, выберите их и щелкните **Подключить**.

3.  В разделе **Управление заданиями** щелкните **Новое задание**.

4.  Введите имя задания, например _sloshingTank3D_.

    ![Сведения о задании][job_details]

5.	На странице **Ресурсы задания** выберите тип ресурса "Узел" и задайте для параметра "Минимум" значение 2. В результате мы выполним задание на двух узлах Linux, каждый из которых имеет по восемь ядер.

    ![Ресурсы задания][job_resources]

6.	Добавьте 4 задачи к заданию, используя следующие команды и параметры.

    >[AZURE.NOTE]Выполнение команды `source /openfoam/settings.sh` настраивает среды выполнения OpenFOAM и MPI, поэтому каждая из задач вызывает ее перед командой OpenFOAM.

    *   **Задача 1**. Запуск **decomposePar** для создания файла данных для параллельного запуска **interDyMFoam**.
    
        *   Назначьте этой задаче 1 узел.

        *   **Командная строка** — `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`.
    
        *   **Рабочий каталог** — /openfoam/sloshingTank3D.
        
        См. следующий рисунок. Остальные задачи настраиваются аналогичным образом.

        ![Сведения о задаче 1][task_details1]

    *   **Задача 2**. Запуск **interDyMFoam** в параллельном режиме для выполнения расчета на основании примера данных.

        *   Назначьте этой задаче 2 узла.

        *   **Командная строка** — `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`.

        *   **Рабочий каталог** — /openfoam/sloshingTank3D.

    *   **Задача 3**. Запуск **reconstructPar** для слияния наборов каталогов времени из каждого processor\_N\_ directory в общий набор каталогов времени.

        *   Назначьте этой задаче 1 узел.

        *   **Командная строка** — `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`.

        *   **Рабочий каталог** — /openfoam/sloshingTank3D.

    *   **Задача 4**. Запуск **foamToEnsight** в параллельном режиме для преобразования выходных файлов OpenFOAM в формат EnSight. Преобразованные файлы помещаются в папку Ensight учебного каталога.

        *   Назначьте этой задаче 2 узла.

        *   **Командная строка** — `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`.

        *   **Рабочий каталог** — /openfoam/sloshingTank3D.

6.	Добавьте зависимости этих задач в возрастающем порядке.

    ![Зависимости задачи][task_dependencies]

7.	Щелкните **Отправить**, чтобы выполнить это задание.

    По умолчанию пакет HPC отправляет задание от имени текущего пользователя. После нажатия кнопки **Отправить** может появиться диалоговое окно с запросом на ввод имени пользователя и пароля.

    ![Учетные данные для задания][creds]

    При некоторых обстоятельствах пакет HPC запоминает введенные ранее сведения о пользователе, и это диалоговое окно не появляется. Чтобы пакет HPC отобразил его снова, введите в командной строке указанную ниже команду, а затем отправьте задание.

    ```
    hpccred delcreds
    ```

8.	Выполнение задания занимает от нескольких десятков минут до нескольких часов в зависимости от заданных параметров. На тепловой карте вы увидите, что задание выполняется на 2 узлах Linux.

    ![Тепловая карта][heat_map]

    На каждом узле запускается 8 процессов.

    ![Процессы Linux][linux_processes]

9.  После завершения задания результаты можно будет найти в папке C:\\OpenFoam\\sloshingTank3D, а файлы журнала — в папке C:\\OpenFoam.


## Просмотр результатов в EnSight

Для отображения и анализа результатов заданий OpenFOAM можно использовать [EnSight](https://www.ceisoftware.com/). Дополнительные сведения о визуализации и анимации в EnSight см. в этом [видеоролике](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Установив программное обеспечение EnSight на головном узле, запустите его.

2.  Откройте файл C:\\OpenFoam\\sloshingTank3D\\EnSight\\sloshingTank3D.case.

    В окне просмотра вы увидите резервуар.

    ![Резервуар в EnSight][tank]

3.	Создайте объект **Isosurface** из **internalMesh** и выберите переменную **alpha\_water**.

    ![Создание isosurface][isosurface]

4.	Задайте цвет для части **Isosurface\_part**, созданной на предыдущем шаге. Например, выберите голубой цвет.

    ![Изменение цвета isosurface][isosurface_color]

5.  Создайте объект **Iso-volume** из коллекции **стен**. Для этого на панели **Parts** (Части) выберите **Walls** (Стены) и нажмите кнопку **Isosurfaces** (Изоповерхности).

6.	В диалоговом окне в поле **Type** (Тип) выберите **Isovolume** (Изообъем) и для диапазона **Isovolume range** (Диапазон изообъема) задайте минимальное значение 0,5. Щелкните **Create with selected parts** (Создать с помощью выделенных частей), чтобы создать объект Isovolume.

7.	Задайте цвет для части **Iso\_volume\_part**, созданной на предыдущем шаге. Например, выберите темно-синий цвет.

8.	Выберите цвет для **стен**. Например, выберите прозрачный белый.

9. Теперь щелкните **Play** (Воспроизведение), чтобы увидеть результаты моделирования.

    ![Результат для резервуара][tank_result]

## Файлы с примерами


### Пример файла cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### Пример файла silent.cfg

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no

```

### Пример сценария settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###Пример сценария hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
	${MPIRUN} $*
else
	# Create the hostfile file
	NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

	# Get every node name and write into the hostfile file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the mpirun with hostfile arg
	${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/linux_processes.png

<!---HONumber=AcomDC_1203_2015-->
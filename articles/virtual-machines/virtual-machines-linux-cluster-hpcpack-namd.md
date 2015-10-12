<properties
 pageTitle="NAMD с пакетом Microsoft HPC на виртуальных машинах Linux | Microsoft Azure"
 description="Развертывание кластера Microsoft HPC в Azure и запуск моделирования NAMD с помощью charmrun на нескольких вычислительных узлах Linux."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/02/2015"
 ms.author="danlep"/>

# Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure

В этой статье показано, как развернуть кластер Microsoft HPC в Azure и выполнить задание [NAMD](http://www.ks.uiuc.edu/Research/namd/) с помощью служебной программы **charmrun** на нескольких вычислительных узлах Linux в сети виртуального кластера, чтобы вычислить и визуализировать структуру большой биомолекулярной системы.

NAMD (программа Nanoscale Molecular Dynamics) — это пакет для параллельных вычислений молекулярной динамики, разработанный для высокопроизводительного моделирования больших биомолекулярных систем с миллионами атомов, таких как вирусы, клеточные структуры и большие белки. NAMD масштабируется до сотен ядер для стандартного моделирования и более чем 500 000 ядер для моделирования самых крупных систем.

Пакет Microsoft HPC предоставляет функции, необходимые для работы приложений высокопроизводительных и параллельных вычислений, включая приложения MPI, в кластерах виртуальных машин Microsoft Azure. Начиная с версии Microsoft HPC 2012 R2 пакет HPC также поддерживает приложения высокопроизводительных вычислений для Linux на виртуальных вычислительных узлах Linux, развернутых в кластере HPC. Общие сведения об использовании вычислительных узлов Linux и пакета HPC см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-cluster-hpcpack.md).


## Предварительные требования

* **Кластер HPC с вычислительными узлами Linux**. Предварительные требования и действия для развертывания кластера HPC с вычислительными узлами Linux в Azure с помощью сценария Azure PowerShell и образов пакета HPC (из Azure Marketplace) см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-cluster-hpcpack.md).

    Ниже приведен пример XML-файла конфигурации, который можно использовать со сценарием для развертывания в Azure кластера HPC, состоящего из головного узла Windows Server 2012 R2 и четырех больших (размера A3) вычислительных узлов CentOS 6.6. Вместо используемых в файле имен подписки и службы подставьте свои значения.

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>West US</Location>  
      <VNet>
        <VNetName>MyVNet</VNetName>
        <SubnetName>Subnet-1</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>CentOS66HN</VMName>
        <ServiceName>MyHPCService</ServiceName>
        <VMSize>Large</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
        <ServiceName>MyLnxCNService</ServiceName>
        <VMSize>Large</VMSize>
        <NodeCount>4</NodeCount>
        <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>    
```


* **Программное обеспечение NAMD и файлы с обучающими материалами**. Скачайте программное обеспечение NAMD для Linux с веб-сайта [NAMD](http://www.ks.uiuc.edu/Research/namd/). Для целей этой статьи используется версия NAMD 2.10 и архив [Linux-x86\_64 (64-разрядный процессор Intel или AMD с поддержкой Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310), с помощью которого вы будете использовать NAMD на нескольких вычислительных узлах Linux в сети кластера. Также скачайте [учебники по NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Следуя инструкциям, приведенным ниже в этой статье, извлеките архив и учебные образцы на головном узле кластера.

* **VMD** (по желанию). Чтобы просмотреть результаты задания NAMD, скачайте и установите на любом компьютере программу [VMD](http://www.ks.uiuc.edu/Research/vmd/), которая позволяет визуализировать молекулярные системы. Текущая версия — 1.9.2. Чтобы скачать программу, перейдите на сайт загрузки VMD.


## Настройка взаимного доверия между вычислительными узлами
Чтобы задание выполнялось на нескольких узлах Linux одновременно, сначала необходимо настроить взаимное доверие узлов (через протокол **rsh** или **ssh**). При создании кластера HPC с помощью сценария развертывания Microsoft HPC IaaS сценарий автоматически настраивает постоянное взаимное доверие для указанной учетной записи администратора. Для учетных записей пользователей без прав администратора, создаваемых в домене кластера, взаимное доверие между узлами должно создаваться в момент назначения им задания и уничтожаться после завершения задания. Чтобы реализовать это для всех пользователей, укажите пару ключей RSA в кластере, который пакет HPC использует для установления отношения доверия.

### Создание пары ключей RSA
Чтобы создать пару ключей RSA, состоящую из открытого и закрытого ключей, достаточно выполнить команду Linux **ssh keygen**.

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

2.	Создайте файл с именем C:\\cred.xml и скопируйте в него данные ключей RSA. Пример этого файла можно найти в приложении в конце этой статьи.

    ```
    <ExtendedData>
      <PrivateKey>Copy the contents of private key here</PrivateKey>
      <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.	Откройте командную строку и введите следующую команду, чтобы задать учетные данные для учетной записи hpclab\\hpcuser. Используйте параметр **extendeddata**, чтобы передать имя файла C:\\cred.xml, созданного для данных ключей.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Эта команда успешно завершается без выходных данных. Задав учетные данные для учетных записей пользователей, необходимых для выполнения заданий, сохраните файл cred.xml в безопасном месте или удалите его.

5.	Если вы создали пару ключей RSA на одном из узлов Linux, не забудьте удалить ключи после завершения работы с ними. Пакет HPC не устанавливает взаимное доверие, если обнаруживает существующий файл id\_rsa или id\_rsa.pub.

>[AZURE.IMPORTANT]Мы не рекомендуем выполнять задания Linux от имени администратора кластера в общем кластере, так как задания, отправленные администратором, выполняются на узлах Linux под учетной записью root. Задание, отправленное пользователем без прав администратора, выполняется под локальной учетной записью пользователя Linux с тем же именем, что и у пользователя задания, а пакет HPC устанавливает взаимное доверие для этого пользователя Linux на всех узлах, выделенных для выполнения задания. Учетную запись пользователя Linux можно настроить вручную на узлах Linux перед выполнением задания, или пакет HPC автоматически создаст ее при отправке задания. Если учетную запись пользователя создает пакет HPC, она удаляется после завершения задания. Из соображений безопасности после завершения задания ключи на узлах удаляются.

## Настройка файлового ресурса для узлов Linux

В папке на головном узле настройте стандартный общий ресурс SMB. Затем подключите его на всех узлах Linux. Таким образом узлы Linux смогут обращаться к файлам NAMD по одному и тому же пути. Параметры и этапы настройки общего доступа к файлам см. в статье [Начало работы с вычислительными узлами Linux в кластере HPC в Azure](virtual-machines-linux-cluster-hpcpack.md). (В этой статье мы рекомендуем подключить общую папку на головном узле, потому что узлы Linux CentOS 6.6 в настоящее время не поддерживают службу файлов Azure, которая предоставляет аналогичные функциональные возможности. Дополнительные сведения о подключении общей папки в службе файлов Azure см. в статье [Сохраняемые подключения к файлам Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).)

1.	Создайте папку на головном узле и сделайте ее общедоступной для чтения и записи. В этом примере \\\CentOS66HN\\Namd является именем папки, где CentOS66HN — имя хоста головного узла.

2. Извлеките файлы NAMD в папку с помощью служебной программы **tar** (версия для Windows) или другой служебной программы Windows, которая работает с TAR-архивами. Извлеките TAR-архив NAMD в папку \\\CentOS66HN\\Namd\\namd2 и извлеките учебники в папке \\\CentOS66HN\\Namd\\namd2\\namdsample.

2.	Откройте окно Windows PowerShell и выполните следующие команды для подключения к общей папке.

    ```
    PS > clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Первая команда создает папку с именем /namd2 на всех узлах в группе LinuxNodes. Вторая команда подключает общую папку //CentOS66HN/Namd/namd2 к папке и задает для параметров dir\_mode и file\_mode значение 777. Значения *username* и *password* должны соответствовать учетным данным пользователя на головном узле.

>[AZURE.NOTE]Символ «`» во второй команде — это escape-символ для PowerShell. «`,» означает, что запятая является частью команды.


## Подготовка к выполнению задания NAMD

 Заданию NAMD требуется файл *nodelist*, указывающий для **charmrun** количество узлов, которые следует использовать при запуске процессов NAMD. Напишите сценарий Bash, который создает файл nodelist и запускает **charmrun** с помощью этого файла. Затем отправьте задание NAMD в диспетчер кластера HPC, вызывающий этот сценарий.

### Переменные сред и файл nodelist
Сведения об узлах и ядрах содержатся в переменной среды $CCP\_NODES\_CORES, которая автоматически устанавливается головным узлом HPC при активации задания. В переменной $CCP\_NODES\_CORES используется такой формат:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
```

Здесь перечисляется общее число узлов, имена узлов и количество ядер на каждом узле, выделенных для выполнения задания. Например, если для выполнения задания требуется 10 ядер, значение $CCP\_NODES\_CORES будет выглядеть примерно так:

```
3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
```

Ниже приведены сведения в файле nodelist, который создается сценарием.

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Например:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```
### Сценарий Bash для создания файла nodelist

Используя любой текстовый редактор, создайте сценарий Bash в папке с файлами программы NAMD и назовите его hpccharmrun.sh. Полный пример этого файла приводится в приложении к данной статье. Ниже описаны задачи, которые выполняет этот сценарий.

>[AZURE.TIP]Сохраните сценарий как текстовый файл, в котором для завершения строк используется нотация Linux (только LF, а не CR LF). Это гарантирует, что файл будет правильно работать на узлах Linux.

1.	Определяет несколько переменных.

    ```
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.	Получает сведения об узле из переменных среды. В переменной $NODESCORES хранится список разбиения слов из $CCP\_NODES\_CORES. $COUNT — это размер $NODESCORES.

    ```
    # Get node information from the environment variables
    # CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```

3.	Если значение переменной $CCP\_NODES\_CORES не задано, просто запускает **charmrun** (это должно происходить, только если сценарий запускается непосредственно на узлах Linux).

    ```
    if [ ${COUNT} -eq 0 ]
    then
    	# CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
    	#echo ${CHARMRUN} $*
    	${CHARMRUN} $*
    ```

4.	Или создает файл nodelist для **charmrun**.

    ```
    else
    	# Create the nodelist file
    	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    	# Write the head line
    	echo "group main" > ${NODELIST_PATH}

    	# Get every node name and number of cores and write into the nodelist file
    	I=1
    	while [ ${I} -lt ${COUNT} ]
    	do
    		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
    		let "I=${I}+2"
    	done
```
5.	Запускает **charmrun** с файлом nodelist, получает для него состояние возврата и в конце удаляет файл nodelist.

    ${CCP\_NUMCPUS} — еще одна переменная среды, которую задает головной узел HPC. В ней хранится общее число ядер, выделенных для этого задания. Мы используем ее, чтобы указать число процессов для charmrun.

    ```
	# Run charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
    fi

    ```
6.	Завершает работу, выдавая состояние возврата **charmrun**.

    ```
    exit ${RTNSTS}
    ```

## Отправка задания NAMD

Теперь вы можете отправить задание NAMD в диспетчер кластера HPC.

1.	Подключитесь к головному узлу кластера и запустите диспетчер кластера HPC.

2.  В разделе **Управление узлами** убедитесь, что для вычислительных узлов Linux задано состояние **В сети**. Если это не так, выберите их и щелкните **Подключить**.

2.  В разделе **Управление заданиями** щелкните **Новое задание**.

3.	Введите для задания имя, например *hpccharmrun*.

    ![Новое задание HPC][namd_job]

4.	На странице **Сведения о задании** в разделе **Ресурсы задания** выберите тип ресурса **Узел** и задайте для параметра **Минимум** значение 3. В этом примере мы выполним задание на трех узлах Linux, каждый из которых имеет по четыре ядра.

    ![Ресурсы задания][job_resources]

5.	На странице **Сведения о задаче и перенаправление ввода-вывода** добавьте к заданию новую задачу и установите следующие значения.

    * **Командная строка** — `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`.

    * **Рабочий каталог** — /namd2.

    * **Минимум** — 3.

    ![Сведения о задаче][task_details]

    >[AZURE.NOTE]Рабочий каталог задается на этой странице, так как **charmrun** пытается переходить в один и тот же рабочий каталог на каждом узле. Если рабочий каталог не задан, пакет HPC запускает команду в папке со случайным именем, созданной на одном из узлов Linux. Это приведет к возникновению на других узлах следующей ошибки: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Чтобы избежать этого, укажите путь к папке, к которой все узлы смогут обращаться как к рабочему каталогу.

5.	Щелкните **Отправить**, чтобы выполнить это задание.

    По умолчанию пакет HPC отправляет задание от имени текущего пользователя. После нажатия кнопки **Отправить** может появиться диалоговое окно с запросом на ввод имени пользователя и пароля.

    ![Учетные данные для задания][creds]

    При некоторых обстоятельствах пакет HPC запоминает введенные ранее сведения о пользователе, и это диалоговое окно не появляется. Чтобы оно снова появилось, введите в командной строке указанную ниже команду, а затем отправьте задание.

    ```
    hpccred delcreds
    ```

6.	Выполнение задания занимает несколько минут.

7.	Журнал задания будет помещен в расположение \<headnodeName>\\Namd\\namd2\\namd2\_hpccharmrun.log, а выходные файлы — в папку \<headnode>\\Namd\\namd2\\namdsample\\1-2-sphere.

8.	При необходимости запустите VMD, чтобы просмотреть результаты задания. Шаги по визуализации выходных файлов NAMD (в данном случае — молекулы белка убиквитина в водяном шаре) не входят в эту статью. Дополнительные сведения см. в [учебниках NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf).

    ![Результаты задания][vmd_view]

## Приложение

### Пример сценария hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
	#echo ${CHARMRUN} $*
	${CHARMRUN} $*
else
	# Create the nodelist file
	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

	# Write the head line
	echo "group main" > ${NODELIST_PATH}

	# Get every node name & cores and write into the nodelist file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 
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




<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-cluster-hpcpack-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-namd/creds.png
[task_details]: ./media/virtual-machines-linux-cluster-hpcpack-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-cluster-hpcpack-namd/vmd_view.png

<!---HONumber=Oct15_HO1-->
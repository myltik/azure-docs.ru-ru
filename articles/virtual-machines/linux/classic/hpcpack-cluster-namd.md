---
title: NAMD с пакетом Microsoft HPC на виртуальных машинах Linux | Документация Майкрософт
description: Развертывание кластера пакета Microsoft HPC в Azure и запуск моделирования NAMD с использованием charmrun на нескольких вычислительных узлах Linux.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 61dd49d4bd3183b6b9a78036d6d7d01798e4dc89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842019"
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure
В этой статье показан один из способов выполнения рабочих нагрузок высокопроизводительных вычислительных систем Linux на виртуальных машинах Azure. Кроме того, здесь показано, как настроить кластер [пакета Microsoft HPC](https://technet.microsoft.com/library/cc514029) в Azure, используя вычислительные узлы Linux, и выполнить моделирование [NAMD](http://www.ks.uiuc.edu/Research/namd/), чтобы вычислить и визуализировать структуру большой биомолекулярной системы.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (программа Nanoscale Molecular Dynamics) — это пакет для параллельных вычислений молекулярной динамики, разработанный для высокопроизводительного моделирования больших биомолекулярных систем с миллионами атомов. К этим системам относятся вирусы, клеточные структуры и большие белки. NAMD масштабируется до сотен ядер для стандартного моделирования и более чем 500 000 ядер для моделирования самых крупных систем.
* **Пакет Microsoft HPC** предоставляет функции, необходимые для запуска приложений высокопроизводительных и параллельных вычислений в кластерах локальных компьютеров или виртуальных машин Azure. Изначально предназначенный для рабочих нагрузок HPC, пакет HPC теперь поддерживает приложения HPC для Linux на виртуальных машинах вычислительного узла Linux, развернутых в кластере пакета HPC. Общие сведения см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](hpcpack-cluster.md).

## <a name="prerequisites"></a>Предварительные требования
* **Кластер пакета HPC с вычислительными узлами Linux.** Разверните кластер пакета HPC с вычислительными узлами Linux в Azure, используя [шаблон Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) или [скрипт Azure PowerShell](hpcpack-cluster-powershell-script.md). Предварительные требования и необходимые действия для обоих вариантов см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](hpcpack-cluster.md). Если вы выбрали вариант со сценарием PowerShell, просмотрите один из примеров файлов конфигурации в конце этой статьи. Этот файл позволяет настроить кластер пакета HPC на основе Azure, который состоит из головного узла Windows Server 2012 R2 и четырех вычислительных узлов большого размера под управлением CentOS 6.6. Измените этот файл в соответствии со своей средой.
* **Программное обеспечение NAMD и руководства.** Скачайте программное обеспечение NAMD для Linux с веб-сайта [NAMD](http://www.ks.uiuc.edu/Research/namd/) (требуется регистрация). Для выполнения действий, описанных в этой статье, используется NAMD версии 2.10 и архив [Linux-x86_64 (64-разрядный процессор Intel или AMD с поддержкой Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310). Кроме того, скачайте [файлы руководства по NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Эти файлы имеют формат TAR, поэтому вам понадобится средство Windows для извлечения этих файлов на головной узел кластера. Чтобы извлечь эти файлы, следуйте инструкциям, приведенным далее в этой статье. 
* **VMD** (необязательно). Чтобы просмотреть результаты задания NAMD, скачайте и установите на любом компьютере программу [VMD](http://www.ks.uiuc.edu/Research/vmd/), позволяющую визуализировать молекулярные системы. Текущая версия — 1.9.2. Чтобы скачать программу, перейдите на сайт загрузки VMD.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Настройка взаимного доверия между вычислительными узлами
Чтобы задание выполнялось на нескольких узлах Linux одновременно, сначала необходимо настроить взаимное доверие узлов (через протокол **rsh** или **ssh**). При создании кластера HPC с помощью сценария развертывания Microsoft HPC IaaS сценарий автоматически настраивает постоянное взаимное доверие для указанной учетной записи администратора. Для учетных записей пользователей без прав администратора, создаваемых в домене кластера, взаимное доверие между узлами должно создаваться в момент назначения им задания и уничтожаться после завершения задания. Чтобы реализовать это для всех пользователей, укажите пару ключей RSA в кластере, который пакет HPC использует для установления отношения доверия. Следуйте инструкциям ниже.

### <a name="generate-an-rsa-key-pair"></a>Создание пары ключей RSA
Чтобы создать пару ключей RSA, состоящую из открытого и закрытого ключей, достаточно выполнить команду Linux **ssh-keygen** .

1. Войдите на компьютер под управлением Linux.
2. Выполните следующую команду:
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > В ходе выполнения команды используйте параметры по умолчанию (нажимайте клавишу **ВВОД**). Не вводите парольную фразу. При запросе пароля просто нажмите клавишу **ВВОД**.
   > 
   > 
   
   ![Создание пары ключей RSA][keygen]
3. Измените каталог на ~/.ssh. Закрытый ключ хранится в файле id_rsa, а открытый — в файле id_rsa.pub.
   
   ![Открытые и закрытые ключи][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Добавление пары ключей в кластер HPC
1. [Подключитесь к головному узлу виртуальной машины с помощью удаленного рабочего стола](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя учетные данные домена, указанные при развертывании кластера (например, hpc\clusteradmin). Управление кластером осуществляется из головного узла.
2. С помощью стандартных процедур Windows Server создайте учетную запись пользователя домена в домене Active Directory кластера. Например, на головном узле можно использовать инструмент Active Directory «Пользователи и компьютеры». В приведенных в этой статье примерах предполагается, что вы создаете пользователя hpcuser в домене hpclab (hpclab\hpcuser).
3. Добавьте пользователя домена в кластер пакета HPC в качестве пользователя кластера. Инструкции см. в статье [Add or remove cluster users](https://technet.microsoft.com/library/ff919330.aspx) (Добавление и удаление пользователей кластера).
4. Создайте файл с именем C:\cred.xml и скопируйте в него данные ключей RSA. Пример можно найти в файлах примеров в конце этой статьи.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Откройте командную строку и введите следующую команду, чтобы задать учетные данные для учетной записи hpclab\hpcuser. Используйте параметр **extendeddata**, чтобы передать имя файла C:\cred.xml, созданного для данных ключей.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Эта команда успешно завершается без выходных данных. Задав учетные данные для учетных записей пользователей, необходимых для выполнения заданий, сохраните файл cred.xml в безопасном месте или удалите его.
6. Если вы создали пару ключей RSA на одном из узлов Linux, не забудьте удалить ключи после завершения работы с ними. Пакет HPC не устанавливает взаимное доверие, если обнаруживает существующий файл id_rsa или id_rsa.pub.

> [!IMPORTANT]
> Мы не рекомендуем выполнять задания Linux от имени администратора кластера в общем кластере, так как задания, отправленные администратором, выполняются на узлах Linux под учетной записью root. Задание, отправленное пользователем без прав администратора, выполняется под локальной учетной записью пользователя Linux с тем же именем, что и у пользователя задания. В таком случае пакет HPC устанавливает взаимное доверие для этого пользователя Linux на всех узлах, выделенных для выполнения задания. Учетную запись пользователя Linux можно настроить вручную на узлах Linux перед выполнением задания, или пакет HPC автоматически создаст ее при отправке задания. Если учетную запись пользователя создает пакет HPC, она удаляется после завершения задания. Из соображений безопасности после завершения задания ключи на узлах удаляются.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Настройка файлового ресурса для узлов Linux
Теперь настройте стандартный общий ресурс SMB и смонтируйте общую папку на всех узлах Linux, чтобы узлы Linux могли обращаться к файлам NAMD по общему пути. Ниже приведены действия для подключения общей папки на головном узле. Мы советуем выполнять эти действия для таких дистрибутивов, как CentOS 6.6, которые в настоящее время не поддерживают службу файлов Azure. Если ваши узлы Linux поддерживают файловый ресурс Azure, см. статью [Использование хранилища файлов Azure в Linux](../../../storage/files/storage-how-to-use-files-linux.md). Дополнительные параметры использования файловых ресурсов с помощью пакета HPC описаны в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](hpcpack-cluster.md).

1. Создайте папку на головном узле и сделайте ее общедоступной для чтения и записи для всех пользователей. В этом примере \\\\CentOS66HN\Namd — это имя папки, где CentOS66HN — имя головного узла.
2. Создайте в общей папке вложенную папку namd2. В namd2 создайте вложенную папку namdsample.
3. Извлеките файлы NAMD в папку с помощью служебной программы **tar** (версия для Windows) или другой служебной программы Windows, которая работает с TAR-архивами. 
   
   * Извлеките содержимое TAR-архива NAMD в папку \\\\CentOS66HN\Namd\namd2.
   * Извлеките файлы руководства в папку \\\\CentOS66HN\Namd\namd2\namdsample.
4. Откройте окно Windows PowerShell и выполните следующие команды для подключения общей папки к узлам Linux.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Первая команда создает папку с именем /namd2 на всех узлах в группе LinuxNodes. Вторая команда подключает общую папку //CentOS66HN/Namd/namd2 к папке и задает для параметров dir_mode и file_mode значение 777. Значения *username* и *password* должны соответствовать учетным данным пользователя на головном узле.

> [!NOTE]
> Символ \"\`\" во второй команде — это escape-символ для PowerShell. \"\`\,"\ означает, что "\,\" (запятая) является частью команды.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Создание сценария Bash для выполнения задания NAMD
Заданию NAMD требуется файл *nodelist* , с помощью которого **charmrun** определяет количество узлов, которые следует использовать при запуске процессов NAMD. Используется скрипт Bash, который создает файл nodelist и запускает **charmrun** с этим файлом. Затем отправьте задание NAMD в диспетчер кластера HPC, вызывающий этот сценарий.

Используя любой текстовый редактор, создайте сценарий Bash в папке /namd2 с файлами программы NAMD и назовите его hpccharmrun.sh. Для быстрого подтверждения концепции скопируйте пример скрипта hpccharmrun.sh, приведенный в конце этой статьи, и перейдите к разделу [Отправка задания NAMD](#submit-a-namd-job).

> [!TIP]
> Сохраните сценарий как текстовый файл, в котором для завершения строк используется нотация Linux (только LF, а не CR LF). Это гарантирует, что файл будет правильно работать на узлах Linux.
> 
> 

Ниже приведены сведения о том, что делает этот сценарий Bash. 

1. Определите несколько переменных.
   
   ```bash
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
2. Получите сведения об узле из переменных среды. В переменной $NODESCORES хранится список разбиения слов из $CCP_NODES_CORES. $COUNT — это размер $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   В переменной $CCP_NODES_CORES используется такой формат:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Здесь перечисляется общее число узлов, имена узлов и количество ядер на каждом узле, выделенных для выполнения задания. Например, если для выполнения задания требуется 10 ядер, значение $CCP_NODES_CORES будет выглядеть примерно следующим образом.
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Если значение переменной $CCP_NODES_CORES не задано, запустите **charmrun** напрямую. (это должно происходить, только если сценарий запускается непосредственно на узлах Linux).
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Или создайте файл nodelist для **charmrun**.
   
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
5. Запустите **charmrun** с файлом nodelist, получите для него состояние возврата и в конце удалите файл nodelist.
   
   ${CCP_NUMCPUS} — еще одна переменная среды, которую задает головной узел HPC. В ней хранится общее число ядер, выделенных для этого задания. Мы используем ее, чтобы указать число процессов для charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Завершите работу, выдав состояние возврата **charmrun** .
   
   ```
   exit ${RTNSTS}
   ```

Ниже приведены сведения в файле nodelist, который создается скриптом.

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

## <a name="submit-a-namd-job"></a>Отправка задания NAMD
Теперь вы можете отправить задание NAMD в диспетчер кластера HPC.

1. Подключитесь к головному узлу кластера и запустите диспетчер кластера HPC.
2. В разделе **Управление ресурсами** убедитесь, что для вычислительных узлов Linux отображается состояние **В сети**. Если это не так, выберите их и щелкните **Подключить**.
3. В разделе **Управление заданиями** щелкните **Новое задание**.
4. Введите для задания имя, например *hpccharmrun*.
   
   ![Новое задание HPC][namd_job]
5. На странице **Сведения о задании** в разделе **Ресурсы задания**, выберите тип ресурса **Узел** и задайте для параметра **Минимум** значение 3. В этом примере мы выполним задание на трех узлах Linux, каждый из которых имеет по четыре ядра.
   
   ![Ресурсы задания][job_resources]
6. Щелкните **Изменение задач** в левой области навигации, а затем — **Добавить**, чтобы добавить задачу в задание.    
7. На странице **Task Details and I/O Redirection** (Сведения о задаче и перенаправление операций ввода-вывода) задайте следующие значения.
   
   * **Командная строка** -
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > Предыдущая командная строка содержит одну команду без разрывов. В области **Командная строка** она будет разбита на несколько строк.
     > 
     > 
   * **Рабочий каталог** — /namd2.
   * **Минимум** — 3.
     
     ![Сведения о задаче][task_details]
     
     > [!NOTE]
     > Так как **charmrun** пытается переходить в один и тот же рабочий каталог на каждом узле, рабочий каталог задается на этой странице. Если рабочий каталог не задан, пакет HPC запускает команду в папке со случайным именем, созданной на одном из узлов Linux. Это приведет к возникновению на других узлах следующей ошибки: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.`. Чтобы избежать этой проблемы, укажите путь к папке, к которой все узлы смогут обращаться как к рабочему каталогу.
     > 
     > 
8. Нажмите кнопку **ОК**, а затем щелкните **Отправить**, чтобы выполнить это задание.
   
   По умолчанию пакет HPC отправляет задание от имени текущего пользователя. После нажатия кнопки **Отправить**может появиться диалоговое окно с запросом на ввод имени пользователя и пароля.
   
   ![Учетные данные для задания][creds]
   
   При некоторых обстоятельствах пакет HPC запоминает введенные ранее сведения о пользователе, и это диалоговое окно не появляется. Чтобы пакет HPC отобразил его снова, введите в командной строке указанную ниже команду, а затем отправьте задание.
   
   ```command
   hpccred delcreds
   ```
9. Выполнение задания занимает несколько минут.
10. Журнал задания будет помещен в папку \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log, а выходные файлы — в папку \\<headnodeName>\Namd\namd2\namdsample\1-2-sphere\..
11. При необходимости запустите VMD, чтобы просмотреть результаты задания. Шаги по визуализации выходных файлов NAMD (в данном случае — молекулы белка убиквитина в водяном шаре) не входят в эту статью. Дополнительные сведения см. в [руководстве по NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf).
    
    ![Результаты задания][vmd_view]

## <a name="sample-files"></a>Файлы с примерами
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Пример XML-файла конфигурации для развертывания кластера с помощью скрипта PowerShell
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

### <a name="sample-credxml-file"></a>Пример файла cred.xml
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

### <a name="sample-hpccharmrunsh-script"></a>Пример сценария hpccharmrun.sh
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

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png

---
title: "Высокий уровень доступности SAP HANA на виртуальных машинах Azure | Документация Майкрософт"
description: "Обеспечение высокого уровня доступности SAP HANA на виртуальных машинах Azure."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 7fa853983119ef4e570b768ca177d169c6e17153
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Высокий уровень доступности SAP HANA на виртуальных машинах Azure

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

В локальной среде для обеспечения высокого уровня доступности SAP HANA можно использовать либо системную репликацию HANA, либо общее хранилище.
В настоящее время в Azure поддерживается только настройка системной репликации HANA. Для репликации SAP HANA используются один главный узел и по крайней мере один подчиненный узел. Изменения данных на главном узле синхронно или асинхронно реплицируются на подчиненные узлы.

В этой статье описывается развертывание виртуальных машин, их настройка, установка платформы кластера, а также установка и настройка системной репликации SAP HANA.
В примерах конфигурации, командах для установки и т. д. используется экземпляр номер 03 и идентификатор системы HANA HDB.

Сначала прочитайте следующие примечания и документы SAP:

* Примечание SAP [2205917]: рекомендуемые параметры ОС для SUSE Linux Enterprise Server for SAP Applications.
* Примечание SAP [1944799]: рекомендации для SAP HANA в SUSE Linux Enterprise Server for SAP Applications.
* [Сценарий оптимизации производительности системной репликации SAP HANA][suse-hana-ha-guide]: это руководство содержит все сведения, необходимые для настройки системной репликации SAP HANA в локальной среде. Используйте это руководство как основу.
  
## <a name="deploying-linux"></a>Развертывание Linux

Агент ресурсов для SAP HANA включен в состав SUSE Linux Enterprise Server for SAP Applications.
В Azure Marketplace доступен образ SUSE Linux Enterprise Server for SAP Applications 12 с BYOS (использование собственной подписки), который можно использовать для развертывания новых виртуальных машин.

### <a name="manual-deployment"></a>Развертывание вручную

1. Создание группы ресурсов
1. Создайте виртуальную сеть
1. Создание двух учетных записей хранения.
1. Создание группы доступности.  
   Настройка максимального числа доменов обновления.
1. Создание подсистемы балансировки нагрузки (внутренней).  
   Выбор виртуальной сети, созданной ранее.
1. Создание виртуальной машины 1.  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS).  
   Выбор учетной записи хранения 1.  
   Выбор группы доступности.  
1. Создание виртуальной машины 2.  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS).  
   Выбор учетной записи хранения 2.   
   Выбор группы доступности.  
1. Добавление дисков данных.
1. Настройка подсистемы балансировки нагрузки.
    1. Создание пула внешних IP-адресов.
        1. Откройте подсистему балансировки нагрузки, выберите пул внешних IP-адресов и щелкните "Добавить".
        1. Введите имя нового пула внешних IP-адресов (например, hana-frontend).
       1. Нажмите кнопку "ОК"
        1. После создания пула внешних IP-адресов запишите его IP-адрес.
    1. Создание внутреннего пула.
        1. Выберите подсистему балансировки нагрузки, щелкните "Серверные пулы" и нажмите кнопку "Добавить".
        1. Введите имя нового внутреннего пула (например, hana-backend).
        1. Щелкните "Добавить виртуальную машину".
        1. Выберите ранее созданную группу доступности.
        1. Выберите виртуальные машины кластера SAP HANA.
        1. Нажмите кнопку "ОК"
    1. Создание пробы работоспособности
       1. Выберите подсистему балансировки нагрузки, щелкните "Зонды работоспособности" и нажмите кнопку "Добавить".
        1. Введите имя новой пробы работоспособности (например, hana-hp).
        1. Выберите протокол TCP, порт 625**03**, интервал, равный 5, и порог состояния неработоспособности, равный 2.
        1. Нажмите кнопку "ОК"
    1. Создание правил балансировки нагрузки.
        1. Выберите подсистему балансировки нагрузки, щелкните "Правила балансировки нагрузки" и нажмите кнопку "Добавить".
        1. Введите имя нового правила балансировки нагрузки (например hana-lb-3**03**15).
        1. Выберите пул внешних IP-адресов, внутренний пул и пробу работоспособности, созданные ранее (например, hana-frontend).
        1. Оставьте выбранным протокол TCP и введите порт 3**03**15.
        1. Увеличьте время ожидания до 30 минут.
       1. **Не забудьте включить плавающий IP-адрес**.
        1. Нажмите кнопку "ОК"
        1. Повторите предыдущие шаги для порта 3**03**17.

### <a name="deploy-with-template"></a>Развертывание с помощью шаблона
Шаблоны быстрого запуска с сайта GitHub можно использовать для развертывания всех необходимых ресурсов. Шаблон развертывает виртуальные машины, подсистему балансировки нагрузки, группу доступности и т. д. Выполните следующее, чтобы развернуть шаблон.

1. Откройте [шаблон базы данных][template-multisid-db] или [конвергированный шаблон][template-converged] на портале Azure. Шаблон базы данных создает только правила балансировки нагрузки для базы данных, а конвергированный шаблон дополнительно создает правила балансировки нагрузки для экземпляра ASCS/SCS и ERS (только Linux). Если вы планируете установить систему на основе SAP NetWeaver и экземпляр ASCS/SCS на одних и тех же компьютерах, используйте [конвергированный шаблон][template-converged].
1. Задайте следующие параметры.
    1. Идентификатор системы SAP  
       Введите идентификатор системы SAP, которую требуется установить. Идентификатор будет использоваться в качестве префикса для развертываемых ресурсов.
    1. Тип стека (указывается только при использовании конвергированного шаблона).  
       Выберите тип стека SAP NetWeaver.
    1. Тип ОС  
       Выберите один из дистрибутивов Linux. Для этого примера выберите SLES 12 BYOS.
    1. Тип базы данных.  
       Выберите HANA.
    1. Размер системы SAP  
       Количество систем SAP, которые будет содержать система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям SAP или к системному интегратору SAP.
    1. Доступность системы  
       Выберите высокую доступность
    1. Имя пользователя и пароль администратора  
       Создается учетная запись пользователя, которую можно использовать для входа на компьютер.
    1. Новая или имеющаяся подсеть  
       Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите ее.
    1. Идентификатор подсети  
    Идентификатор подсети, к которой следует подключить виртуальные машины. Выберите подсеть виртуальной сети VPN или Express Route, чтобы подключить виртуальную машину к локальной сети. Идентификатор обычно выглядит следующим образом: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>.

## <a name="setting-up-linux-ha"></a>Настройка высокого уровня доступности Linux

Ниже приведены элементы с префиксами: [A] — применяется ко всем узлам, [1] — применяется только к узлу 1, [2] — применяется только к узлу 2.

1. [A] Только SLES for SAP BYOS. Зарегистрируйте SLES, чтобы иметь возможность использовать репозитории.
1. [A] Только SLES for SAP BYOS. Добавьте модуль public-cloud.
1. [A] Обновите SLES.
    ```bash
    sudo zypper update

    ```

1. [1] Включите доступ по протоколу SSH.
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] Включите доступ по протоколу SSH.
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] Включите доступ по протоколу SSH.
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] Установите расширение для обеспечения высокого уровня доступности.
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] Настройте разметку дисков.
    1. Диспетчер логических томов  
    Обычно для томов, предназначенных для хранения данных и файлов журнала, рекомендуется использовать LVM. В приведенном ниже примере предполагается, что к виртуальным машинам подключены четыре диска данных, которые следует использовать для создания двух томов.
        * Создайте физические тома для всех дисков, которые вы хотите использовать.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Создайте группу томов для файлов данных, одну группу томов для файлов журнала и одну — для общей папки SAP HANA.
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Создайте логические тома.
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Создайте каталоги подключения и скопируйте UUID всех логических томов.
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Создайте записи fstab для трех логических томов.
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Вставьте эту строку в /etc/fstab.
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Подключите новые тома.
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Обычные диски  
       Для небольших или демонстрационных систем файлы данных и журналов HANA можно поместить на один диск. Приведенные ниже команды создают раздел/dev/sdc и форматируют его для файловой системы XFS.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>write down the id of /dev/sdc1
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] Настройте разрешение имен узлов для всех узлов.  
    Можно использовать DNS-сервер или внести изменения в файл /etc/hosts на всех узлах. В этом примере показано, как использовать файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах.
    ```bash
    sudo vi /etc/hosts
    ```
    Вставьте следующие строки в /etc/hosts. Измените IP-адрес и имя узла в соответствии со своей средой.    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] Установите кластер.
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] Добавьте узел в кластер.
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] Измените пароль hacluster, введя тот же пароль.
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] Настройте corosync для использование другого транспорта и добавьте список узлов. Иначе кластер не будет работать.
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Добавьте в этот файл приведенное ниже содержимое, выделенное полужирным шрифтом.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of note 1 >
      }
      node {
        ring0_addr:     < ip address of note 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Затем перезапустите службу corosync.

    ```bash
    sudo service corosync restart
    
    ```

1. [A] Установите пакеты для обеспечения высокого уровня доступности HANA.  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Установка SAP HANA

Выполните указания в главе 4 [руководства по оптимизации производительности системной репликации SAP HANA][suse-hana-ha-guide], чтобы установить системную репликацию SAP HANA.

1. [A] Запустите hdblcm с DVD-диска HANA.
    * Выберите установку (1).
    * Выберите дополнительные компоненты для установки (1).
    * Введите путь установки [/hana/shared] и нажмите клавишу "ВВОД".
    * Введите имя локального узла [..] и нажмите клавишу "ВВОД".
    * "Do you want to add additional hosts to the system? (y/n)" (Вы хотите добавить дополнительные узлы в систему? (Да/нет)). Нажмите клавишу n и клавишу "ВВОД".
    * Введите идентификатор системы SAP HANA: <SID of HANA e.g. HDB>
    * Введите номер экземпляра [00].   
  Номер экземпляра HANA. Введите 03, если вы использовали шаблон Azure или пример выше.
    * Выберите режим базы данных и введите индекс [1], затем нажмите клавишу "ВВОД".
    * Выберите использование системы и введите индекс [4].  
  Выберите использование системы.
    * Введите расположение томов данных [/hana/data/HDB], затем нажмите клавишу "ВВОД".
    * Введите расположение томов журнала [/hana/log/HDB], затем нажмите клавишу "ВВОД".
    * "Restrict maximum memory allocation?" (Ограничить максимальный объем выделяемой памяти?) Нажмите клавишу n и клавишу "ВВОД".
    * Введите имя узла сертификатов для узла "…". [...]: нажмите клавишу "ВВОД".
    * Введите пароль пользователя агента узла SAP (sapadm).
    * Подтвердите пароль пользователя агента узла SAP (sapadm).
    * Введите пароль системного администратора (hdbadm).
    * Подтвердите пароль системного администратора (hdbadm).
    * Введите домашний каталог системного администратора [/usr/sap/HDB/home], затем нажмите клавишу "ВВОД".
    * Укажите оболочку для входа администратора системы [/bin/sh] и нажмите клавишу "ВВОД".
    * Введите идентификатор пользователя администратора системы [1001] и нажмите клавишу "ВВОД".
    * Введите идентификатор для группы пользователей (sapsys) [79], затем нажмите клавишу "ВВОД".
    * Введите пароль пользователя базы данных (SYSTEM).
    * Подтвердите пароль пользователя базы данных (SYSTEM).
    * "Restart system after machine reboot?" (Перезапустить систему после перезагрузки компьютера?) Нажмите клавишу n и клавишу "ВВОД".
    * "Do you want to continue? (y/n)" (Вы действительно хотите продолжить? (Да/нет)).  
  Просмотрите сводку и нажмите клавишу y, чтобы продолжить.
1. [A] Обновите агент узла SAP.  
  Скачайте последний архив агента узла SAP с сайта [SAP Softwarecenter][sap-swcenter] и выполните следующую команду, чтобы обновить агент. Замените путь к архиву, указав скачанный файл.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] Создайте репликацию HANA (в качестве привилегированного пользователя).  
    Выполните следующую команду: Обязательно замените строки, выделенные полужирным шрифтом (идентификатор системы HANA HDB и номер экземпляра 03), значениями для своей устанавливаемой системы.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] Создайте запись хранилища ключей (в качестве привилегированного пользователя).
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] Создайте резервную копию базы данных (в качестве привилегированного пользователя).
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] Переключитесь на пользователя sapsid (например, hdbadm) и создайте первичный сайт.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] Переключитесь на пользователя sapsid (например, hdbadm) и создайте вторичный сайт.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Настройка платформы кластера

Измените параметры по умолчанию.

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-saphana.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Создание устройства STONITH

Устройство STONITH использует субъект-службу для авторизации в Microsoft Azure. Чтобы создать субъект-службу, выполните следующее.

1. Перейдите на портал <https://portal.azure.com>.
1. Откройте колонку "Azure Active Directory".  
   Перейдите в колонку "Свойства" и запишите идентификатор каталога. Это **идентификатор клиента**.
1. Щелкните "Регистрация приложений".
1. Нажмите "Добавить"
1. Введите имя, выберите тип приложения "Веб-приложения или API", введите URL-адрес входа (например. http://localhost) и нажмите кнопку "Создать".
1. URL-адрес входа не используется и может быть любым допустимым URL-адресом.
1. Выберите новое приложение и щелкните "Ключи" на вкладке "Параметры".
1. Введите описание нового ключа, выберите "Срок действия не ограничен" и нажмите кнопку "Сохранить".
1. Запишите его значение. Он используется в качестве **пароля** субъекта-службы.
1. Запишите идентификатор приложения. Он используется в качестве имени пользователя (**идентификатора для входа** в следующих шагах) субъекта-службы.

У субъекта-службы по умолчанию нет разрешений на доступ к ресурсам Azure. Необходимо предоставить ему разрешения на запуск и остановку (освобождение) всех виртуальных машин кластера.

1. Перейдите на портал https://portal.azure.com.
1. Откройте колонку "Все ресурсы".
1. Выберите виртуальную машину.
1. Выберите "Управление доступом (IAM)".
1. Нажмите "Добавить"
1. Выберите роль "Владелец".
1. Введите имя созданного ранее приложения.
1. Нажмите кнопку "ОК"

После изменения разрешений для виртуальных машин можно настроить устройства STONITH в кластере.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Создание ресурсов SAP HANA

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Проверка установки кластера
В следующей главе описывается, как можно проверить конфигурацию. Для каждого теста предполагается, что он выполняется от имени привилегированного пользователя, а главный узел SAP HANA выполняется на виртуальной машине saphanavm1.

#### <a name="fencing-test"></a>Тестирование ограждения

Можно проверить конфигурацию агента ограждения, отключив сетевой интерфейс на узле saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

Виртуальная машина должна быть перезагружена или остановлена, в зависимости от конфигурации кластера.
Если для параметра stonith-action задано значение off, то виртуальная машина будет остановлена, а ресурсы перенесены на работающую виртуальную машину.

Если для параметра AUTOMATED_REGISTER задано значение false, то после повторного запуска виртуальной машины не удастся запустить ресурс SAP HANA на вторичном узле. В этом случае необходимо настроить экземпляр HANA как вторичный узел, выполнив следующую команду.

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Тестирование отработки отказа вручную

Можно проверить отработку отказа вручную, остановив службу pacemaker на узле saphanavm1.
<pre><code>
service pacemaker stop
</code></pre>

После отработки отказа можно снова запустить эту службу. Если для параметра AUTOMATED_REGISTER задано значение false, то ресурс SAP HANA на узле saphanavm1 не удастся запустить на вторичном узле. В этом случае необходимо настроить экземпляр HANA как вторичный узел, выполнив следующую команду.

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Тестирование переноса

Можно перенести на главный узел SAP HANA, выполнив следующую команду.
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Она перенесет главный узел SAP HANA и группу, которая содержит виртуальный IP-адрес, на узел saphanavm2.
Если для параметра AUTOMATED_REGISTER задано значение false, то ресурс SAP HANA на узле saphanavm1 не удастся запустить на вторичном узле. В этом случае необходимо настроить экземпляр HANA как вторичный узел, выполнив следующую команду.

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

При переносе устанавливаются ограничения расположения, которые должны быть удалены.

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

Необходимо также очистить состояние ресурса на вторичном узле.

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Дальнейшие действия
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* Дополнительные сведения об обеспечении высокого уровня доступности и планировании аварийного восстановления SAP HANA в Azure (крупные экземпляры) см. в [этой статье](hana-overview-high-availability-disaster-recovery.md). 


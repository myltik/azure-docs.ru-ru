---
title: Настройка системной репликации SAP HANA на виртуальных машинах Azure | Документация Майкрософт
description: Обеспечение высокого уровня доступности SAP HANA на виртуальных машинах Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: e23c69b020c96dab4d119ebf4f45791e6f63c4af
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656845"
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Высокий уровень доступности SAP HANA на виртуальных машинах Azure

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

В локальной среде для обеспечения высокого уровня доступности SAP HANA можно использовать либо системную репликацию HANA, либо общее хранилище.
В настоящее время в Azure поддерживается только одна функция высокого уровня доступности — системная репликация HANA на виртуальных машинах Azure. Для репликации SAP HANA используются один основной узел и по крайней мере один вторичный узел. Изменения данных на основном узле синхронно или асинхронно реплицируются на вторичные узлы.

В этой статье описывается развертывание виртуальных машин, их настройка, установка платформы кластера, а также установка и настройка системной репликации SAP HANA.
В примерах конфигурации, командах установки и т. д. используется экземпляр номер 03 и идентификатор системы HANA HN1.

Сначала прочитайте следующие примечания и документы SAP:

* примечание к SAP [1928533], которое содержит:
  * список размеров виртуальных машин Azure, поддерживаемых для развертывания ПО SAP;
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * сведения о требуемой версии ядра SAP для Windows и Linux в Microsoft Azure.
* примечание к SAP [2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Azure;
* Примечание SAP [2205917] содержит рекомендуемые параметры ОС для SUSE Linux Enterprise Server для приложений SAP.
* Примечание SAP [1944799] содержит рекомендации для SAP HANA в SUSE Linux Enterprise Server для приложений SAP.
* примечание к SAP [2178632], содержащее подробные сведения обо всех доступных метриках мониторинга для SAP в Azure;
* примечание к SAP [2191498], содержащее сведения о необходимой версии агента SAP Host Agent для Linux в Azure;
* примечание к SAP [2243692], содержащее сведения о лицензировании SAP в Linux в Azure;
* примечание к SAP [1984787], содержащее общие сведения о SUSE Linux Enterprise Server 12;
* примечание к SAP [1999351], содержащее дополнительные сведения об устранении неполадок, связанных с расширением для расширенного мониторинга Azure для SAP;
* [вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), содержащий все необходимые примечания к SAP для Linux;
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Linux в Azure (эта статья)][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* [Сценарий оптимизации производительности системной репликации SAP HANA][suse-hana-ha-guide]: это руководство содержит все сведения, необходимые для настройки системной репликации SAP HANA в локальной среде. Используйте это руководство как основу.

## <a name="overview"></a>Обзор

Чтобы достичь высокого уровня доступности, SAP HANA устанавливается на двух виртуальных машинах. Данные реплицируются с использованием репликации системы HANA.

![Обзор высокого уровня доступности SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

В установке SAP HANA SR используется выделенное виртуальное имя узла и виртуальный IP-адрес. Балансировщику нагрузки в Azure нужен виртуальный IP-адрес. Ниже показана конфигурация балансировщика нагрузки.

* Конфигурация внешнего интерфейса:
  * IP-адрес 10.0.0.13 для hn1-db
* Конфигурация серверной части:
  * подключена к основным сетевым интерфейсам всех виртуальных машин, которые должны входить в репликацию системы HANA.
* Порт пробы:
  * Порт 62503
* Правила балансировки нагрузки:
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Развертывание Linux

Агент ресурсов для SAP HANA включен в состав SUSE Linux Enterprise Server for SAP Applications.
В Azure Marketplace доступен образ для SUSE Linux Enterprise Server для приложения SAP 12, который можно использовать для развертывания новых виртуальных машин.

### <a name="deploy-with-template"></a>Развертывание с помощью шаблона
Все необходимые ресурсы можно развернуть с помощью шаблонов быстрого запуска с сайта GitHub. Шаблон развертывает виртуальные машины, подсистему балансировки нагрузки, группу доступности и т. д. Чтобы развернуть шаблон, сделайте следующее.

1. Откройте [шаблон базы данных][template-multisid-db] или [конвергированный шаблон][template-converged] на портале Azure. 
   Шаблон базы данных создает только правила балансировки нагрузки для базы данных, а конвергированный шаблон дополнительно создает правила балансировки нагрузки для экземпляра ASCS/SCS и ERS (только Linux). Если вы планируете установить систему на основе SAP NetWeaver и экземпляр ASCS/SCS на одних и тех же компьютерах, используйте [конвергированный шаблон][template-converged].
1. Задайте следующие параметры.
    1. Идентификатор системы SAP  
       Введите идентификатор системы SAP, которую требуется установить. Идентификатор будет использоваться в качестве префикса для развертываемых ресурсов.
    1. Тип стека (указывается только при использовании конвергированного шаблона).   
       Выберите тип стека SAP NetWeaver.
    1. Тип ОС  
       Выберите один из дистрибутивов Linux. Для этого примера выберите SLES 12.
    1. Тип базы данных.  
       Выберите HANA.
    1. Размер системы SAP  
       Количество систем SAP, которые будет содержать система. Если вы не знаете, сколько систем SAP потребуется, обратитесь к партнеру по технологиям или системному интегратору SAP.
    1. Доступность системы  
       Выберите высокую доступность
    1. Имя пользователя и пароль администратора  
       Создается учетная запись пользователя, которую можно использовать для входа на компьютер.
    1. Новая или имеющаяся подсеть  
       Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите ее.
    1. Идентификатор подсети  
    Идентификатор подсети, к которой следует подключить виртуальные машины. Выберите подсеть виртуальной сети VPN или Express Route, чтобы подключить виртуальную машину к локальной сети. Идентификатор обычно выглядит следующим образом: /subscriptions/`<subscription ID`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>.

### <a name="manual-deployment"></a>Развертывание вручную

1. Создание группы ресурсов
1. Создайте виртуальную сеть
1. Создание группы доступности.  
   Настройка максимального числа доменов обновления.
1. Создание подсистемы балансировки нагрузки (внутренней).  
   Выберите виртуальную сеть, созданную на шаге 2.
1. Создание виртуальной машины 1.  
   Используйте по меньшей мере SLES4SAP 12 с пакетом обновления 1. В этом примере мы используем образ 12 SLES4SAP с пакетом обновления 2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES для SAP 12 с пакетом обновления 2 (Премиум)  
   Выберите ранее созданную группу доступности.  
1. Создание виртуальной машины 2.  
   Используйте по меньшей мере SLES4SAP 12 с пакетом обновления 1. В этом примере мы используем образ SLES4SAP 12 с пакетом обновления 1 (BYOS) https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES для SAP 12 с пакетом обновления 2 (Премиум)  
   Выберите ранее созданную группу доступности.  
1. Добавление дисков данных.
1. Настройка подсистемы балансировки нагрузки.
    1. Создание пула внешних IP-адресов.
        1. Откройте подсистему балансировки нагрузки, выберите пул внешних IP-адресов и щелкните "Добавить".
        1. Введите имя нового пула внешних IP-адресов (например, hana-frontend).
        1. Выберите для параметра "Назначение" вариант "Статическое" и введите IP-адрес (например, **10.0.0.13**)
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
    1. SAP HANA 1.0: создание правил балансировки нагрузки.
        1. Выберите подсистему балансировки нагрузки, щелкните "Правила балансировки нагрузки" и нажмите кнопку "Добавить".
        1. Введите имя нового правила балансировки нагрузки (например hana-lb-3**03**15).
        1. Выберите пул внешних IP-адресов, внутренний пул и зонд работоспособности, созданные ранее (например, hana-frontend).
        1. Оставьте выбранным протокол TCP и введите порт 3**03**15.
        1. Увеличьте время ожидания до 30 минут.
        1. **Не забудьте включить плавающий IP-адрес**.
        1. Нажмите кнопку "ОК"
        1. Повторите предыдущие шаги для порта 3**03**17.
    1. SAP HANA 2.0: создание правил балансировки нагрузки для системной базы данных.
        1. Выберите подсистему балансировки нагрузки, щелкните "Правила балансировки нагрузки" и нажмите кнопку "Добавить".
        1. Введите имя нового правила балансировки нагрузки (например, hana-lb-3**03**13).
        1. Выберите пул внешних IP-адресов, внутренний пул и зонд работоспособности, созданные ранее (например, hana-frontend).
        1. Оставьте выбранным протокол TCP и введите порт 3**03**13.
        1. Увеличьте время ожидания до 30 минут.
        1. **Не забудьте включить плавающий IP-адрес**.
        1. Нажмите кнопку "ОК"
        1. Повторите предыдущие шаги для порта 3**03**14
    1. SAP HANA 2.0: создание правила балансировки нагрузки для первой базы данных клиента.
        1. Выберите подсистему балансировки нагрузки, щелкните "Правила балансировки нагрузки" и нажмите кнопку "Добавить".
        1. Введите имя нового правила балансировки нагрузки (например, hana-lb-3**03**40).
        1. Выберите пул внешних IP-адресов, внутренний пул и зонд работоспособности, созданные ранее (например, hana-frontend).
        1. Оставьте выбранным протокол TCP и укажите порт 3**03**40.
        1. Увеличьте время ожидания до 30 минут.
        1. **Не забудьте включить плавающий IP-адрес**.
        1. Нажмите кнопку "ОК"
        1. Повторите предыдущие шаги для портов 3**03**41 и 3**03**42.

Дополнительные сведения о портах, требуемых для SAP HANA, см. в главе о [подключениях к базам данных клиентов](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) руководства по [базам данных клиентов SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) или в [примечании к SAP № 2388694][2388694].


## <a name="create-pacemaker-cluster"></a>Создание кластера Pacemaker

Следуйте указаниям из статьи [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) (Настройка Pacemaker на SUSE Linux Enterprise Server в Azure), чтобы создать базовый кластер Pacemaker для этого сервера HANA. Также вы можете применить тот же кластер Pacemaker для SAP HANA и SAP NetWeaver (A)SCS.

## <a name="installing-sap-hana"></a>Установка SAP HANA

Ниже приведены элементы с префиксами: **[A]** — применяется ко всем узлам, **[1]** — применяется только к узлу 1, **[2]** — применяется только к узлу 2 в кластере Pacemaker.

1. **[A]** Настройка разметки дисков
    1. Диспетчер логических томов  
       
       Обычно для томов, предназначенных для хранения данных и файлов журнала, рекомендуется использовать LVM. В приведенном ниже примере предполагается, что к виртуальным машинам подключены четыре диска данных, которые следует использовать для создания двух томов.

       Вывод списка всех доступных дисков
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Пример выходных данных
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Создайте физические тома для всех дисков, которые вы хотите использовать.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Создайте группу томов для файлов данных, одну группу томов для файлов журнала и одну — для общей папки SAP HANA.

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
        Создайте логические тома. Линейный том будет создан при использовании lvcreate без параметра -i. Чтобы улучшить производительность операций ввода-вывода, советуем создать чередующийся том. Значение аргумента -i должно соответствовать количеству базовых физических томов. В этом документе том данных использует 2 физических тома, поэтому параметр -i имеет аргумент 2. Журнальный том имеет 1 физический том, поэтому параметр -i используется явно. При использовании более 1 физического тома укажите каждому тому данных, журнальному или общему тому, параметр -i и замените число идентичным числом базовых физических томов.

       <pre><code>
       sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Создайте каталоги подключения и скопируйте UUID всех логических томов.
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Создайте записи fstab для трех логических томов.
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Вставьте эту строку в /etc/fstab.
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Подключите новые тома.
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Обычные диски  
       Для демонстрационных систем файлы данных и журналов HANA можно поместить на один диск. Приведенные ниже команды создают раздел /dev/disk/azure/scsi1/lun0 и форматируют его для файловой системы XFS.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Вставьте эту строку в /etc/fstab.
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Создайте целевой каталог и подключите диск.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]** Настройка разрешения имен узлов для всех узлов  
    Можно использовать DNS-сервер или внести изменения в файл /etc/hosts на всех узлах. В этом примере показано, как использовать файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах.
    ```bash
    sudo vi /etc/hosts
    ```
    Вставьте следующие строки в /etc/hosts. Измените IP-адрес и имя узла в соответствии со своей средой.    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Установка пакетов для обеспечения высокого уровня доступности HANA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

Чтобы установить системную репликацию SAP HANA, выполните указания из главы 4 руководства SAP HANA SR Performance Optimized Scenario (Сценарий оптимизации производительности системной репликации SAP HANA) на странице https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/.

1. **[A]** Запустите hdblcm с DVD-диска HANA.
    * Выберите установку (1).
    * Выберите дополнительные компоненты для установки (1).
    * Введите путь установки [/hana/shared] и нажмите клавишу "ВВОД".
    * Введите имя локального узла [..] и нажмите клавишу "ВВОД".
    * "Do you want to add additional hosts to the system? (y/n)" (Вы хотите добавить дополнительные узлы в систему? (Да/нет)). Нажмите клавишу n и клавишу "ВВОД".
    * Введите идентификатор системы SAP HANA: <SID of HANA e.g. HN1>
    * Введите номер экземпляра [00].   
  Номер экземпляра HANA. Введите 03, если вы использовали шаблон Azure или выполняли развертывание вручную.
    * Выберите режим базы данных и введите индекс [1], затем нажмите клавишу "ВВОД".
    * Выберите использование системы и введите индекс [4].  
  Выберите использование системы.
    * Введите расположение томов данных [/hana/data/HN1], а затем нажмите клавишу ВВОД.
    * Введите расположение томов журналов [/hana/log/HN1], а затем нажмите клавишу ВВОД.
    * "Restrict maximum memory allocation?" (Перезапустить систему после перезагрузки компьютера?) Нажмите клавишу n и клавишу "ВВОД".
    * Введите имя узла сертификатов для узла "…" [...] и нажмите клавишу ВВОД.
    * Введите пароль пользователя агента узла SAP (sapadm).
    * Подтвердите пароль пользователя агента узла SAP (sapadm).
    * Введите пароль системного администратора (hdbadm).
    * Подтвердите пароль системного администратора (hdbadm).
    * Введите домашний каталог системного администратора [/usr/sap/HN1/home], а затем нажмите клавишу ВВОД.
    * Укажите оболочку для входа администратора системы [/bin/sh] и нажмите клавишу "ВВОД".
    * Введите идентификатор пользователя администратора системы [1001] и нажмите клавишу "ВВОД".
    * Введите идентификатор для группы пользователей (sapsys) [79], затем нажмите клавишу "ВВОД".
    * Введите пароль пользователя базы данных (SYSTEM).
    * Подтвердите пароль пользователя базы данных (SYSTEM).
    * "Restart system after machine reboot?" (Перезапустить систему после перезагрузки компьютера?) Нажмите клавишу n и клавишу "ВВОД".
    * "Do you want to continue? (y/n)" (Вы действительно хотите продолжить? (Да/нет)).   
  Просмотрите сводку и нажмите клавишу y, чтобы продолжить.

1. **[A]** Обновите агент узла SAP.  
  Скачайте последний архив агента узла SAP с сайта [SAP Softwarecenter][sap-swcenter] и выполните следующую команду, чтобы обновить агент. Замените путь к архиву, указав скачанный файл.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Настройка репликации системы SAP HANA 2.0

Ниже приведены элементы с префиксами: **[A]** — применяется ко всем узлам, **[1]** — применяется только к узлу 1, **[2]** — применяется только к узлу 2 в кластере Pacemaker.

1. **[1]** Создание базы данных клиента

   Если вы используете SAP HANA 2.0 или MDC, создайте базу данных клиента для системы SAP NetWeaver. Замените NW1 идентификатором SID этой системы SAP.

   Войдите от имени пользователя `<hanasid`>adm и выполните следующую команду.

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Настройка репликации системы на первом узле
   
   Войдите от имени пользователя `<hanasid`>adm и выполните резервное копирование баз данных.

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Скопируйте системные файлы PKI на дополнительный сайт.

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Создайте основной сайт.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Настройка репликации системы на втором узле
    
    Зарегистрируйте второй узел, чтобы запустить репликацию системы. Войдите от имени пользователя `<hanasid`>adm и выполните следующую команду.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Настройка репликации системы SAP HANA 1.0

1. **[1]** Создание обязательных пользователей

    Войдите от имени привилегированного пользователя и выполните следующую команду. Обязательно замените строки, выделенные полужирным шрифтом (идентификатор системы HANA HN1 и номер экземпляра 03), значениями для своей системы SAP HANA.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]** Создание записи в хранилище ключей
   
    Войдите от имени привилегированного пользователя и выполните следующую команду, чтобы создать новую запись в хранилище ключей.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]** Создание резервной копии базы данных

   Войдите от имени привилегированного пользователя и выполните резервное копирование баз данных.

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Если вы используете мультитенантную систему, создайте резервные копии и для баз данных клиентов.

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Настройка репликации системы на первом узле
    
    Войдите от имени пользователя `<hanasid`> adm и создайте основной сайт.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]** Настройка репликации системы на вторичном узле

    Войдите от имени пользователя `<hanasid`> adm и зарегистрируйте дополнительный сайт.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Создание ресурсов кластера SAP HANA

   Прежде всего создайте топологию HANA. Выполните следующие команды на одном из узлов кластера Pacemaker.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Теперь создайте ресурсы HANA.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Убедитесь, что состояние кластера — "ОК" и что запущены все ресурсы. Не важно, на каком узле выполняются ресурсы.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Проверка установки кластера
В этой главе описывается, как можно проверить установку. Для каждого теста предполагается, что он выполняется от имени привилегированного пользователя, а главный узел SAP HANA выполняется на виртуальной машине hn1-db-0.

#### <a name="fencing-test"></a>Тестирование ограждения

Можно проверить конфигурацию агента ограждения, отключив сетевой интерфейс на узле hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

Виртуальная машина должна быть перезагружена или остановлена, в зависимости от конфигурации кластера.
Если для параметра stonith-action задано значение off, то виртуальная машина будет остановлена, а ресурсы перенесены на работающую виртуальную машину.

Если для параметра AUTOMATED_REGISTER задано значение false, то после повторного запуска виртуальной машины не удастся запустить ресурс SAP HANA на вторичном узле. В этом случае настройте экземпляр HANA в качестве вторичного узла, выполнив следующую команду.

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Тестирование отработки отказа вручную

Можно проверить отработку отказа вручную, остановив службу pacemaker на узле hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

После отработки отказа можно снова запустить эту службу. Если для параметра AUTOMATED_REGISTER задано значение false, то ресурс SAP HANA с узла hn1-db-0 не удастся запустить на вторичном узле. В этом случае настройте экземпляр HANA в качестве вторичного узла, выполнив следующую команду.

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Тестирование переноса

Можно перенести на главный узел SAP HANA, выполнив следующую команду.
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Если для параметра AUTOMATED_REGISTER задано значение false, такая последовательность команд перенесет главный узел SAP HANA и группу, которая содержит виртуальный IP-адрес, на узел hn1-db-1.
Не удается запустить ресурс SAP HANA с узла hn1-db-0 на вторичном узле. В этом случае настройте экземпляр HANA в качестве вторичного узла, выполнив следующую команду.

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

При переносе устанавливаются ограничения расположения, которые должны быть удалены.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

Необходимо также очистить состояние ресурса на вторичном узле.

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Дополнительная информация
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* Дополнительные сведения об обеспечении высокого уровня доступности и планировании аварийного восстановления SAP HANA в Azure (крупные экземпляры) см. в [этой статье](hana-overview-high-availability-disaster-recovery.md). 

---
title: "Настройка высокого уровня доступности с помощью STONITH для SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт"
description: "Установка высокого уровня доступности для SAP HANA в Azure (крупные экземпляры) в SUSE с помощью STONITH."
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6db4a9308ede1744081f114c61f1ca0c303706e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-setup-in-suse-using-the-stonith"></a>Настройка высокого уровня доступности в SUSE с помощью STONITH
Этот документ содержит подробные пошаговые инструкции для настройки высокого уровня доступности в операционной системе SUSE с помощью устройства STONITH.

**Заявление об отказе:** *это руководство получено путем тестирования установки в среде крупных экземпляров Microsoft HANA, которая успешно работает. Так как команда управления службами Майкрософт для крупных экземпляров HANA не поддерживает операционную систему, может потребоваться обратиться к специалистам SUSE за дальнейшим устранением неполадок или уточнениями на уровне операционной системы. Команда управления службами Майкрософт устанавливает устройство STONITH и будет оказывать полную поддержку, а также может быть задействована для устранения неполадок, связанных с проблемами устройства STONITH.*
## <a name="overview"></a>Обзор
Для настройки высокого уровня доступности с помощью кластеризации SUSE необходимо выполнить следующие предварительные требования.
### <a name="pre-requisites"></a>Предварительные требования
- Крупные экземпляры HANA подготовлены.
- Операционная система зарегистрирована.
- Крупные экземпляры HANA подключены к серверу SMT для получения исправлений или пакетов.
- Установлены последние исправления операционной системы.
- NTP (сервер времени) настроен.
- Вы ознакомились с последней версией документации SUSE по настройке HA.

### <a name="setup-details"></a>Сведения о настройке
- В этом руководстве использованы следующие настройки.
- Операционная система: SUSE 12 SP1.
- Крупные экземпляры HANA: 2xS192 (4 сокета, 2 ТБ).
- Версия HANA: HANA 2.0 с пакетом обновления 1.
- Имена серверов: sapprdhdb95 (node1) и sapprdhdb96 (node2).
- Устройство STONITH: устройство STONITH на базе iSCSI.
- Настройка NTP на одном из узлов крупных экземпляров HANA.

При настройке крупных экземпляров HANA с помощью HSR можно запросить команду управления службами Майкрософт для настройки STONITH. Пользователю, у которого уже есть подготовленный крупный экземпляр HANA и которому необходимо настроить устройство STONITH для имеющихся колонок, необходимо предоставить следующие сведения команде управления службами Майкрософт в форме запроса на обслуживание (SRF). Чтобы подключить крупный экземпляр HANA, форму SRF можно запросить у менеджера по технической поддержке или представителя Майкрософт. Новые клиенты могут запросить устройство STONITH во время подготовки. Входные данные доступны в форме запроса подготовки.

- Имя сервера и IP-адрес сервера (например, myhanaserver1, 10.35.0.1).
- Расположение (например, восточная часть США).
- Имя клиента (например, корпорация Майкрософт).

После настройки устройства STONITH команда управления службами Майкрософт предоставляет имя устройства SBD и IP-адрес хранилища iSCSI, которое можно использовать для настройки установки STONITH. 

Для настройки сквозной высокой доступности с помощью STONITH выполните такие действия:

1.  Идентифицируйте устройство SBD.
2.  Инициализируйте устройство SBD.
3.  Настройте кластер.
4.  Настройте службу наблюдения Softdog.
5.  Соедините узел с кластером.
6.  Проверка кластера
7.  Настройка ресурсов в кластере.
8.  Протестируйте процесс отработки отказа.

## <a name="1---identify-the-sbd-device"></a>1.   Идентификация устройства SBD
В этом разделе описывается, как определить устройство SBD для установки после того, как команда управления службами Майкрософт настроила STONITH. **Этот раздел относится только к имеющимся клиентам**. Команда управления службами Майкрософт предоставляет новым клиентам имя устройства SBD, поэтому они могут пропустить этот раздел.

1.1. Измените */etc/iscsi/initiatorname.isci* на *iqn.1996-04.de.suse:01: <Tenant><Location><SID><NodeNumber>*.  
Управление службами Майкрософт предоставит данную строку. Это необходимо выполнить на **обоих** узлах, при этом номера узлов отличаются.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2. Измените */etc/iscsi/iscsid.conf*: задайте *node.session.timeo.replacement_timeout=5* и *node.startup = automatic*. Это необходимо выполнить на **обоих** узлах.

1.3. Выполните команду обнаружения. Отобразится четыре сеанса. Это необходимо выполнить на обоих узлах.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Выполните команду для входа в устройство iSCSI. Отобразится четыре сеанса. Это необходимо выполнить на **обоих** узлах.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Выполните сценарий повторного сканирования: *rescan-scsi-bus.sh*.  Отобразятся созданные диски.  Запустите его на обоих узлах. Отобразится номер LUN больше нуля (например, 1, 2 и т. д.).

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6. Чтобы получить имя устройства, выполните команду *fdisk –l*. Запустите ее на обоих узлах. Выберите устройство с размером **178MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Инициализация устройства SBD

2.1. Инициализируйте устройство SBD на **обоих** узлах.

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2. Проверьте, что было записано на устройство. Выполните это на **обоих** узлах.

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Настройка кластера
В этом разделе описаны шаги по настройке кластера SUSE HA.
### <a name="31-package-installation"></a>3.1. Установка пакета
3.1.1 Проверьте, установлены ли шаблоны SAPHanaSR-doc и ha_sles. Если нет, установите их. Это необходимо выполнить на **обоих** узлах.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2. Настройка кластера
3.2.1 Для настройки кластера можно использовать команду *ha-cluster-init* или мастер yast2. В этом случае используется мастер yast2. Это действие выполняется **только на первичном узле**.

Последовательно выберите "yast2> Высокая доступность > Кластер" ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Щелкните **Закрыть**, так как пакет halk2 уже установлен.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Нажмите кнопку **Продолжить**.

Ожидаемое значение — число развернутых узлов (в данном случае 2) ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) Нажмите кнопку **Далее**
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Добавьте имена узлов, а затем щелкните Add suggested files (Добавить предложенные файлы).

Щелкните Turn csync2 ON (Включить csync2).

Щелкните Generate Pre-Shared-Keys (Создать предопределенные ключи). Отобразится всплывающее окно, показанное ниже.

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Щелкните **ОК**

Аутентификация выполняется с помощью IP-адреса и предопределенных ключей в Csync2. Файл ключа создается с помощью csync2 -k /etc/csync2/key_hagroup. Файл key_hagroup нужно скопировать во все элементы кластера вручную после его создания. **Убедитесь, что файл скопирован с узла 1 на узел 2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Нажмите кнопку **Далее**.
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

В параметрах по умолчанию загрузка была отключена, поэтому измените значение на "ВКЛ.", чтобы Pacemaker запускался во время загрузки. Выбор можно сделать на основе требований к установке.
Щелкните **Далее** и настройка кластера будет завершена.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Настройка службы наблюдения Softdog
В этом разделе описана настройка службы наблюдения (softdog).

4.1. Добавьте следующую строку в */etc/init.d/boot.local* на **обоих** узлах.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Обновите файл */etc/sysconfig/sbd* на **обоих** узлах, как показано ниже.
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Загрузите модуль ядра на **обоих** узлах, выполнив следующую команду.
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4. Проверьте и убедитесь, что softdog выполняется на **обоих** узлах, как это показано ниже.
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5. Запустите устройство SBD на **обоих** узлах.
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Протестируйте управляющую программу SBD на **обоих** узлах. После того, как она была настроена на **обоих** узлах, отобразится две записи.
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7. Отправьте текстовое сообщение на **один** из узлов.
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8. На **втором** узле (node2) можно проверить состояние сообщения.
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9. Чтобы внедрить конфигурацию SBD, обновите файл */etc/sysconfig/sbd*, как показано ниже. Это необходимо выполнить на **обоих** узлах.
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10. Запустите службу Pacemaker на **первичном узле** (node1).
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Если служба pacemaker *завершается с ошибкой*, дополнительные сведения см. в разделе *Сценарий 5. Сбой службы Pacemaker*.

## <a name="5---joining-the-cluster"></a>5.   Соединение с кластером
В этом разделе описано, как соединить узел с кластером.

### <a name="51-add-the-node"></a>5.1. Добавление узла.
Выполните следующую команду на **node2**, чтобы узел 2 мог соединиться с кластером.
```
ha-cluster-join
```
Если во время соединения кластера была получена *ошибка*, дополнительные сведения см. в разделе *Сценарий 6. Узел 2 не может соединиться с кластером*.

## <a name="6---validating-the-cluster"></a>6.   Проверка кластера

### <a name="61-start-the-cluster-service"></a>6.1. Запуск службы кластера
Чтобы проверить и при необходимости запустить кластера в первый раз на **обоих** узлах, запустите службы кластера.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Мониторинг состояния
Выполните команду *crm_mon*, чтобы убедиться, что **оба** кластера в сети. Ее можно выполнить на **любом узле** кластера.
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png)Кроме того, для проверки состояния кластера можно войти в Hawk *https://<node IP>: 7630*. Пользователь по умолчанию — hacluster, а пароль — linux. При необходимости можно изменить пароль с помощью команды *passwd*.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Настройка свойств кластера и ресурсов 
В этом разделе описаны действия по настройке кластерных ресурсов.
В этом примере выполняется настройка следующего ресурса, остальные можно настроить (при необходимости) с помощью руководства SUSE HA. Эту настройку необходимо выполнить только на **одном из узлов**. Выполните ее на первичном узле.

- Начальная загрузка кластера
- Устройство STONITH
- Виртуальный IP-адрес


### <a name="71-cluster-bootstrap-and-more"></a>7.1. Виртуальная загрузка кластера и многое другое
Добавьте начальную загрузку кластера. Создайте файл и добавьте в текст следующее.
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
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
```
Добавьте конфигурацию в кластер.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2. Устройство STONITH
Добавьте ресурс STONITH. Создайте файл и добавьте в текст следующее.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
Добавьте конфигурацию в кластер.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3. Виртуальный IP-адрес
Добавьте ресурс виртуального IP-адреса. Создайте файл и добавьте текст, как показано ниже.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Добавьте конфигурацию в кластер.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4. Проверка ресурсов

При выполнении команды *crm_mon* отобразится два ресурса.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Кроме того, появится сообщение о состоянии в *https://<node IP address>:7630/cib/live/state*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Тестирование процесса отработки отказа
Чтобы протестировать процесс отработки отказа, остановите службу Pacemaker на node1 и отработку отказа ресурсов на node2.
```
Service pacemaker stop
```
Теперь остановите службу Pacemaker на узле **node2**. После этого произойдет отработка отказа ресурсов на узле **node1**.

**Перед выполнением отработки отказа**
![Before-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**После выполнения отработки отказа**
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Устранение неполадок
В этом разделе описывается несколько сценариев сбоев, которые могут возникнуть во время установки. Вы можете не столкнуться с этими проблемами.

### <a name="scenario-1-cluster-node-not-online"></a>Сценарий 1. Узел кластера не в сети
Если какой-либо из узлов находится в автономном режиме в диспетчере кластеров, можно попробовать включить его.

Запустите службу iSCSI.
```
service iscsid start
```

Теперь можно войти на узел iSCSI.
```
iscsiadm -m node -l
```
Ожидаемый результат выглядит так:
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Сценарий 2. yast2 не показывает графическое представление
В этом документе для настройки высокодоступного кластера используется графический экран yast2. Если yast2 не открывается с графическим окном, как показано на рисунке, и выводит ошибку Qt, выполните следующие действия. Если он открывается с графическим окном, эти шаги можно пропустить.

**Ошибка**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Ожидаемые выходные данные**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Если yast2 не открывается с помощью графического представления, выполните следующие действия.

Установите необходимые пакеты. Необходимо войти в систему как пользователь root и настроить SMT для скачивания и/или установки пакетов.

Для установки пакетов последовательно выберите "yast > Программное обеспечение > Software Management (Управление программным обеспечением) > Зависимости" и используйте параметр Install recommended packages… (Установить рекомендуемые пакеты...) На следующем снимке экрана показаны ожидаемые экраны.
>[!NOTE]
>Для получения доступа к графическому представлению yast2 с обоих узлов на них нужно выполнить шаги, указанные выше.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

В разделе зависимостей выберите Install Recommended Packages (Установите рекомендуемые пакеты). ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Просмотрите изменения, а затем щелкните "ОК".

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Установка пакета продолжится. ![yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Щелкните "Далее".

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Щелкните "Готово".

Необходимо также установить пакеты libqt4 и libyui qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)yast2 должен иметь возможность открыть графическое представление, как показано здесь.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Сценарий 3. yast2 не имеет варианта высокого уровня доступности
Для отображения варианта высокого уровня доступности в центре управления yast2 необходимо установить дополнительные пакеты.

Щелкните "Yast2 > Программное обеспечение > Software Management (Управление программным обеспечением)", выберите следующие шаблоны.

- Базовый сервер SAP HANA.
- Компилятор и средства C/C++.
- высокую доступность;
- Базовый сервер приложений SAP.

На следующем экране показаны шаги по установке шаблонов.

Выберите "Yast2 > Программное обеспечение > Software Management (Управление программным обеспечением)".

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Выберите шаблоны.

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Нажмите кнопку **Принять**.

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Нажмите кнопку **Продолжить**.

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Щелкните **Далее** после завершения установки.

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Сценарий 4. Установка HANA завершается с ошибкой сборок GCC
Установка HANA завершается сбоем со следующей ошибкой.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Чтобы устранить проблему, необходимо установить библиотеки (libgcc_sl и libstdc ++ 6) следующим образом.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Сценарий 5. Сбой службы Pacemaker

Во время запуска службы Pacemaker произошел следующий сбой.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Чтобы устранить проблему, удалите следующую строку в файле */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Сценарий 6. Узел 2 не может соединиться с кластером

При соединении node2 с имеющимся кластером с помощью команды *ha-cluster-join* возникла следующая ошибка.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Чтобы устранить проблему, выполните следующую команду на обоих узлах.

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

После предыдущего исправления узел node2 должен добавиться в кластер.

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Основные документы
Дополнительные сведения об установке SUSE HA можно найти в следующих статьях: 

- [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf ) (Сценарий оптимизации производительности системной репликации SAP HANA)
- [Storage based fencing](https://www.suse.com/documentation/sle-ha-2/book_sleha/data/sec_ha_storage_protect_fencing.html) (Разграничение на основе хранилища)
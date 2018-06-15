---
title: Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure | Документация Майкрософт
description: Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: ba44a8988c4af68abf4d155a2b9cb490b6122d39
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656420"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure.

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Существуют два варианта настройки кластера Pacemaker в Azure. Вы можете использовать агент ограждения, который выполняет перезапуск неисправного узла через интерфейсы API Azure, или можете использовать устройство SBD.

Для устройства SBD требуется дополнительная виртуальная машина, выступающая в роли сервера цели iSCSI и предоставляющая устройство SBD. Этот сервер цели iSCSI может совместно использоваться другими кластерами Pacemaker. Преимуществом использования устройства SBD является ускоренная отработка отказа. Кроме того, при использовании устройств SBD в локальной среде не требуется вносить изменения на эксплуатацию кластера Pacemaker. Для функций ограждения SBD по-прежнему можно использовать агент ограждения Azure в качестве резервного механизма ограждения на случай, если сервер цели iSCSI станет недоступен.

Если вы не хотите тратить средства на дополнительную виртуальную машину, можно также использовать агент ограждения Azure. Недостатком этого подхода является то, что отработка отказа может занять от 10 до 15 минут, если не удается выполнить остановку ресурсов или узлам кластера не удается связаться друг с другом.

![Обзор кластера Pacemaker на SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>Ограждение SBD

Если вы хотите использовать устройство SBD для ограждения, выполните следующие действия.

### <a name="set-up-an-iscsi-target-server"></a>Настройка сервера цели iSCSI

Сначала необходимо создать виртуальную машину цели iSCSI, если она еще не создана. Серверы цели iSCSI могут использовать совместно несколько кластеров Pacemaker.

1. Разверните новую виртуальную машину под управлением SLES 12 с пакетом обновления 1 (SP1) или более поздней версии и подключитесь в ней по протоколу SSH. Эта виртуальная машина не обязательно должна быть большой. Будет достаточно выбрать размер Standard_E2s_v3 или Standard_D2s_v3.

1. Обновите SLES.

   <pre><code>
   sudo zypper update
   </code></pre>

1. Удалите пакеты.

   Чтобы избежать проблем с targetcli и SLES 12 с пакетом обновления 3 (SP3), удалите указанные ниже пакеты. Можно игнорировать сообщения об ошибках со сведениями о том, что пакет не удается обнаружить.
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. Установите пакеты цели iSCSI.

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Включите службу цели iSCSI.

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Создайте устройство iSCSI на сервере цели iSCSI.

Подключите новый диск данных к виртуальной машине цели iSCSI, которую можно использовать для этого кластера. Это может быть диск данных объемом в 1 ГБ, который должен быть размещен в учетной записи хранения уровня "Премиум". Кроме того, это может быть управляемый диск уровня "Премиум", что позволит использовать преимущества [соглашения об уровне обслуживания одной виртуальной машины](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Выполните следующую команду на **виртуальной машине цели iSCSI**, чтобы создать диск iSCSI для нового кластера. В следующем примере **cl1** используется для идентификации нового кластера, а **prod-cl1-0** и **prod-cl1-1** — имена узлов кластера. Замените их именами узлов своего кластера.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>Настройка устройства SBD

Подключитесь к устройству iSCSI, созданному на предыдущем шаге, из кластера.
Выполните следующие команды для узлов нового кластера, которые нужно создать.
Ниже приведены элементы с префиксами: **[A]**  — применяется ко всем узлам, **[1**] — применяется только к узлу 1, **[2]**  — применяется только к узлу 2.

1. **[A]**  Подключитесь к устройствам iSCSI.

   Сначала включите службы iSCSI и SBD.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Измените имя инициатора на первом узле.

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Измените содержимое файла в соответствии со списками управления доступом, использованными при создании устройства iSCSI на сервере цели iSCSI.

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Измените имя инициатора на втором узле.

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Измените содержимое файла в соответствии со списками управления доступом, использованными при создании устройства iSCSI на сервере цели iSCSI.

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** Запустите службу iSCSI.

   Теперь запустите службу iSCSI для применения изменений.
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Подключитесь к устройствам iSCSI. В следующем примере 10.0.0.17 является IP-адресом сервера цели iSCSI, а 3260 — это порт по умолчанию. <b>iqn.2006 04.cl1.local:cl1</b> — имя цели, которое отображается при выполнении первой команды.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Убедитесь, что устройство iSCSI доступно, и запишите имя устройства (в следующем примере это /dev/sde).

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Теперь получите идентификатор устройства iSCSI.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   Команда выдает три идентификатора устройства. Мы рекомендуем использовать идентификатор, начинающийся со scsi-3. В приведенном примере это
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** Создайте устройство SBD.

   Используйте идентификатор устройства iSCSI, чтобы создать устройство SBD на первом узле кластера.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]** Адаптируйте конфигурацию SBD.

   Откройте файл конфигурации SBD.

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Измените значение свойства устройства SBD, включите интеграцию Pacemaker и измените режим запуска SBD.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Создание файл конфигурации Softdog.

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Теперь загрузите модуль.

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Установка кластера

Ниже приведены элементы с префиксами: **[A]**  — применяется ко всем узлам, **[1**] — применяется только к узлу 1, **[2]**  — применяется только к узлу 2.

1. **[A]** Обновите SLES.

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** Включите доступ по протоколу SSH.

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]** Включите доступ по протоколу SSH.

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Включите доступ по протоколу SSH.

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Установите расширение для обеспечения высокого уровня доступности.
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]** Установите разрешения имен.   

   Можно использовать DNS-сервер или внести изменения в файл /etc/hosts на всех узлах. В этом примере показано, как использовать файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах. Преимущество использования /etc/hosts в том, что кластер становится независимым от службы DNS, которая также может являться единой точкой отказа.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Вставьте следующие строки в /etc/hosts. Измените IP-адрес и имя узла в соответствии со своей средой.   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Установите кластер.
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** Добавьте узел в кластер.
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** Измените пароль hacluster на тот же пароль.

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** Настройте corosync для использования другого транспорта и добавьте список узлов. Иначе кластер не будет работать.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Добавьте следующее содержимое, выделенное полужирным шрифтом, в файл, если значения отсутствуют или отличаются.
   
   <pre><code> 
   [...]
     <b>token:          5000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Затем перезапустите службу corosync.

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** Измените параметры Pacemaker по умолчанию.

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Создание устройства STONITH

Устройство STONITH использует субъект-службу для авторизации в Microsoft Azure. Чтобы создать субъект-службу, выполните следующее.

1. Перейдите на сайт <https://portal.azure.com>.
1. Откройте колонку "Azure Active Directory".  
   Перейдите в колонку "Свойства" и запишите идентификатор каталога. Это **идентификатор клиента**.
1. Щелкните "Регистрация приложений".
1. Нажмите "Добавить"
1. Введите имя, выберите тип приложения "Веб-приложение или API", введите URL-адрес входа (например, http://localhost)) и нажмите кнопку "Создать".
1. URL-адрес входа не используется и может быть любым допустимым URL-адресом.
1. Выберите новое приложение и щелкните "Ключи" на вкладке "Параметры".
1. Введите описание нового ключа, выберите "Срок действия не ограничен" и нажмите кнопку "Сохранить".
1. Запишите его значение. Он используется в качестве **пароля** субъекта-службы.
1. Запишите идентификатор приложения. Он используется в качестве имени пользователя (**идентификатора для входа** в следующих шагах) субъекта-службы.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Создайте пользовательскую роль для агента ограждения.

У субъекта-службы по умолчанию нет разрешений на доступ к ресурсам Azure. Необходимо предоставить ему разрешения на запуск и остановку (освобождение) всех виртуальных машин кластера. Если вы еще не создали эту пользовательскую роль, ее можно создать с помощью [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) или [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role).

Используйте следующее содержимое для входного файла. Необходимо адаптировать содержимое для ваших подписок, поэтому замените c276fc76-9cd4-44c9-99a7-4fd71546436e и e91d47c4-76f3-4271-a796-21b4ecfe3624 идентификаторами своих подписок. Если у вас имеется только одна подписка, удалите вторую запись в AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** Назначьте пользовательскую роль субъекту-службе.

Назначьте субъекту-службе пользовательскую роль Linux Fence Agent Role, созданную в последней главе. Больше не используйте роль владельца!

1. Перейдите на сайт https://portal.azure.com.
1. Откройте колонку "Все ресурсы".
1. Выберите виртуальную машину первого узла кластера.
1. Выберите "Управление доступом (IAM)".
1. Нажмите "Добавить"
1. Выберите роль Linux Fence Agent Role.
1. Введите имя созданного ранее приложения.
1. Нажмите кнопку "ОК"

Повторите предыдущие шаги для второго узла кластера.

### <a name="1-create-the-stonith-devices"></a>**[1]** Создайте устройства STONITH.

После изменения разрешений для виртуальных машин можно настроить устройства STONITH в кластере.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]** Создайте топологию ограждения для ограждения SBD.

Если вы планируете использовать устройство SBD, все равно рекомендуется использовать агент ограждения Azure в качестве резервного механизма на случай, если сервер цели iSCSI станет недоступен.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** Разрешите использование устройства STONITH.

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Дополнительная информация
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* Дополнительные сведения об установке высокого уровня доступности и плана для аварийного восстановления SAP HANA на виртуальных машинах Azure см. в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure][sap-hana-ha].

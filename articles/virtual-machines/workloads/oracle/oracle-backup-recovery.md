---
title: "Создание резервных копий и восстановление базы данных Oracle Database 12c на виртуальной машине Linux в Azure | Документация Майкрософт"
description: "Узнайте, как создавать резервные копии и выполнять восстановление базы данных Oracle Database 12c в среде Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 5/17/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7a53ca814170a6651fc499c41395fe088a6f869a
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017

---

# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Создание резервных копий и восстановление базы данных Oracle Database 12c на виртуальной машине Linux в Azure

Вы можете использовать Azure CLI, чтобы создать ресурсы Azure и управлять ими из командной строки или с помощью сценариев. В этой статье используются сценарии Azure CLI для развертывания базы данных Oracle Database 12c из коллекции образов Azure Marketplace.

Прежде чем начать, убедитесь, что установлен Azure CLI. Дополнительные сведения см. в [руководстве по установке Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Подготовка среды
### <a name="step-1-assumptions"></a>Шаг 1. Предварительные требования

*   Для резервного копирования и восстановления вам необходимо создать виртуальную машину Linux с установленным экземпляром базы данных Oracle Database 12c. Имя образа Marketplace, который вы будете использовать для создания виртуальных машин, — *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Инструкции по созданию базы данных Oracle см. в статье [Создание базы данных Oracle 12c на виртуальной машине Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Шаг 2. Подключение к виртуальной машине

*   Чтобы создать сеанс Secure Shell (SSH) на виртуальной машине, используйте команду ниже. Замените IP-адрес и имя узла значением `publicIpAddress` для виртуальной машины.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Шаг 3. Подготовка базы данных

1.  Чтобы перейти к выполнению этого шага, у вас должен быть экземпляр Oracle (cdb1), выполняющийся на виртуальной машине с именем *myVM*.

    Запустите суперпользователя *oracle*, а затем инициализируйте прослушиватель:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (Необязательно.) База данных должна выполняться в режиме журнала архивирования:

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  (Необязательно.) Создайте таблицу, чтобы проверить фиксацию:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Проверьте расположение файла резервной копии и его размер и при необходимости измените эти параметры:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Используйте диспетчер восстановления Oracle (RMAN), чтобы создать резервную копию базы данных:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Шаг 4. Согласованное с приложениями резервное копирование для виртуальных машин Linux

Это новая функция службы Azure Backup, которая позволяет пользователям указывать сценарии (с параметрами pre и post) для выполнения до и после создания моментального снимка виртуальной машины.

#### <a name="1-download-vmsnapshotscriptpluginconfigjson-from-httpsgithubcommicrosoftazurebackupvmsnapshotpluginconfig-content-should-be-similar-to-following"></a>1. Скачайте файл VMSnapshotScriptPluginConfig.json со страницы https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Содержимое файла должно выглядеть примерно следующим образом:

```azurecli
{
    "pluginName" : "ScriptRunner",
    "preScriptLocation" : "",
    "postScriptLocation" : "",
    "preScriptParams" : ["", ""],
    "postScriptParams" : ["", ""],
    "preScriptNoOfRetries" : 0,
    "postScriptNoOfRetries" : 0,
    "timeoutInSeconds" : 30,
    "continueBackupOnFailure" : true,
    "fsFreezeEnabled" : true
}
```

#### <a name="2-create-etcazure-folder-on-vm"></a>2) На виртуальной машине создайте папку /etc/azure:

```bash
$ sudo su -
# mkdir /etc/azure
# cd /etc/azure
```

#### <a name="3-copy-vmsnapshotscriptpluginconfigjson-file-to-folder-etcazure"></a>3. Скопируйте файл VMSnapshotScriptPluginConfig.json в папку /etc/azure.

#### <a name="4-edit-the-vmsnapshotscriptpluginconfigjson-file-to-included-the-prescriptlocation-and-postscriptlocation-parameters-for-example"></a>4. Измените файл VMSnapshotScriptPluginConfig.json, чтобы добавить параметры PreScriptLocation и PostScriptlocation. Например:
```azurecli
{
    "pluginName" : "ScriptRunner",
    "preScriptLocation" : "/etc/azure/pre_script.sh",
    "postScriptLocation" : "/etc/azure/post_script.sh",
    "preScriptParams" : ["", ""],
    "postScriptParams" : ["", ""],
    "preScriptNoOfRetries" : 0,
    "postScriptNoOfRetries" : 0,
    "timeoutInSeconds" : 30,
    "continueBackupOnFailure" : true,
    "fsFreezeEnabled" : true
}
```
#### <a name="5-create-the-pre-and-post-script-files"></a>5. Создайте файлы сценариев предварительного и последующего выполнения.

Ниже приведены примеры сценариев предварительного и последующего выполнения для холодного резервного копирования (завершение работы и перезапуск).

Для /etc/azure/pre_script.sh:

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
```

Для /etc/azure/post_script.sh:

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
```

Ниже приведены примеры сценариев предварительного и последующего выполнения для оперативного резервного копирования:

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
```

Для /etc/azure/post_script.sh:

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
```

Для /etc/azure/pre_script.sql необходимо изменить содержимое файла в соответствии с требованиями:

```bash
alter tablespace SYSTEM begin backup;
...
...
alter system switch logfile;
alter system archive log stop;
```

Для /etc/azure/post_script.sql необходимо изменить содержимое файла в соответствии с требованиями:

```bash
alter tablespace SYSTEM end backup;
...
...
alter system archive log start;
```

#### <a name="6-change-file-permissions"></a>6. Измените разрешения файла:

```bash
# chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
# chmod 700 /etc/azure/pre_script.sh
# chmod 700 /etc/azure/post_script.sh
```

#### <a name="7-test-the-scripts-sign-in-as-root-make-sure-no-errors"></a>7. Протестируйте сценарии (выполните вход как суперпользователь) и проверьте отсутствие ошибок.

```bash
# /etc/azure/pre_script.sh
# /etc/azure/post_script.sh
```

Дополнительные сведения см. в записи блога [Announcing application consistent backup for Linux VMs using Azure Backup](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/) (Объявление согласованного с приложениями резервного копирования для виртуальных машин Linux).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Шаг 5. Использование хранилищ служб восстановления Azure для резервного копирования виртуальной машины

1.  Войдите на портал Azure, а затем найдите экземпляр хранилищ служб восстановления.
![Страница хранилищ служб восстановления](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Нажмите кнопку **Добавить**, чтобы добавить новое хранилище.
![Страница добавления хранилищ служб восстановления](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Чтобы продолжить, щелкните раздел **myVault**. Откроется страница сведений о хранилище.
![Страница сведений о хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Нажмите кнопку **Архивация**. Затем вам нужно добавить цель, политику и элементы резервного копирования.
![Страница резервного копирования хранилищ служб восстановления](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Для **цели архивации** используйте значения по умолчанию — **Azure** и **Виртуальная машина**. Чтобы продолжить, нажмите кнопку **ОК**.
![Страница сведений о хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Для **политики резервного копирования** используйте параметры **DefaultPolicy** или **Создание новой политики**. Чтобы продолжить, нажмите кнопку **ОК**.
![Страница сведений о политике резервного копирования хранилищ служб восстановления](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Установите флажок для элемента **myVM1**, нажмите кнопку **ОК**, а затем нажмите кнопку **Enable backup** (Включить резервное копирование).
![Страница сведений об элементах резервного копирования в хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > После нажатия кнопки **Enable backup** (Включить резервное копирование) процесс резервного копирования запустится только в запланированное время. Чтобы настроить немедленное резервное копирование, сделайте следующее:

8.  Щелкните колонку **Элементы архивации**, а затем под разделом **числа элементов архивации** выберите число элементов.

    ![Страница сведений о хранилище myVault в хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_08.png)

9.  В правой области страницы нажмите кнопку с многоточием (**…**), а затем щелкните **Создать резервную копию**.

    ![Команда "Создать резервную копию" в хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_09.png)

10. Нажмите кнопку **Архивация**, дождитесь завершения процесса резервного копирования, а затем перейдите к шагу 5 — удалению файлов базы данных.
Чтобы просмотреть состояние задания резервного копирования, щелкните **Задания**.
![Страница задания в хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_10.png)

    Появится состояние задания резервного копирования:

    ![Страница задания в хранилищах служб восстановления с отображением состояния](./media/oracle-backup-recovery/recovery_service_11.png)

11. Для согласованного с приложениями резервного копирования Сведения об ошибках см. в файле журнала /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Шаг 6. Удаление файлов базы данных 
Далее в этой статье вы узнаете, как выполнить тестирование процесса восстановления. Перед тестированием процесса восстановления вам нужно удалить файлы базы данных.

1.  Удалите файлы табличного пространства и резервных копий:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Необязательно.) Завершите работу экземпляра Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-recovery-services-vaults"></a>Восстановление удаленных файлов из хранилищ служб восстановления
Чтобы восстановить удаленные файлы, сделайте следующее:

1. Войдите на портал Azure, а затем найдите элемент хранилищ служб восстановления для хранилища *myVault*. В правом верхнем углу в разделе **Элементы архивации** щелкните число элементов.
![Элементы архивации хранилища myVault в хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_12.png)

2. В разделе **числа элементов архивации** щелкните число элементов.
![Число элементов архивации виртуальной машины Azure в хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_13.png)

3. Щелкните **File Recovery (Preview)** (Восстановление файлов (предварительная версия)).
![Снимок экрана страницы восстановления файлов в хранилищах служб восстановления](./media/oracle-backup-recovery/recovery_service_14.png)

4. Нажмите кнопку **Скачать скрипт**, а затем сохраните файл скачивания (SH) в папку на клиентском компьютере.
![Параметры сохранения файла скачивания сценария](./media/oracle-backup-recovery/recovery_service_15.png)

5. Скопируйте SH-файл на виртуальную машину.

    В примере ниже показано, как использовать команду безопасного копирования для перемещения файла на виртуальную машину. Вы можете также скопировать содержимое в буфер обмена, а затем вставить его в новый файл, установленный на виртуальной машине.

    > [!IMPORTANT]
    > Чтобы перейти к следующему примеру, вы должны обновить значения IP-адреса и папки. Значения должны соответствовать папке, в которой сохранен файл.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Измените файл, чтобы он принадлежал суперпользователю.

    В следующем примере вы измените файл, чтобы он принадлежал суперпользователю, а затем измените разрешение.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    В примере ниже показано содержимое, которое вы должны увидеть после выполнения предыдущего сценария. Когда появится запрос продолжить, введите **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. Будет подтвержден доступ к подключенным томам.

    Чтобы выйти, введите **q**, а затем найдите подключенные тома. Чтобы создать список добавленных томов, введите в командной строке **df -k**.

    ![команда df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Чтобы скопировать недостающие файлы обратно в папки, используйте следующий сценарий:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. В следующем сценарии используйте RMAN для восстановления базы данных:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Отключите диск.

    На портале Azure нажмите кнопку **Unmount Disks** (Отключить диски).

    ![Команда отключения дисков](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Восстановление всей виртуальной машины

Вместо того чтобы восстанавливать удаленные файлы из хранилищ служб восстановления, можно восстановить всю виртуальную машину.

### <a name="step-1-drop-the-myvm"></a>Шаг 1. Удаление хранилища myVM

*   Войдите на портал Azure, перейдите в хранилище *myVM*, а затем выберите **Удалить**.

    ![Команда удаления](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Шаг 2. Восстановление виртуальной машины

1.  Перейдите в **Хранилища служб восстановления**, а затем выберите **myVault**.
![Запись myVault, которая отображается в пользовательском интерфейсе](./media/oracle-backup-recovery/recover_vm_02.png)

2.  В правом верхнем углу в разделе **Элементы архивации** щелкните число элементов.
![Элементы архивации myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  В разделе **числа элементов архивации** щелкните число элементов.
![Страница восстановления виртуальной машины](./media/oracle-backup-recovery/recover_vm_04.png)

4.  В правой области страницы нажмите кнопку с многоточием (**…**), а затем щелкните **Restore VM** (Восстановить виртуальную машину).
![Команда восстановления виртуальной машины](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Выберите элемент, который нужно восстановить, а затем нажмите кнопку **ОК**.
В разделе ![Выберите точку восстановления](./media/oracle-backup-recovery/recover_vm_06.png) в случае включения согласованного с приложениями резервного копирования появится голубая вертикальная линия.

6.  Выберите **Имя виртуальной машины**, выберите **группу ресурсов**, а затем нажмите кнопку **ОК**.
![Значения восстановления конфигурации](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Чтобы восстановить виртуальную машину, в левом нижнем углу пользовательского интерфейса (см. предыдущий снимок экрана) щелкните **Восстановить**.

8.  Чтобы просмотреть состояние процесса восстановления, щелкните **Задания**.
![Команда состояния заданий резервного копирования](./media/oracle-backup-recovery/recover_vm_08.png)

    На снимке экрана ниже показано состояние процесса восстановления.

    ![Состояние процесса восстановления](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Шаг 3. Задание общедоступного IP-адреса
После восстановления виртуальной машины необходимо настроить общедоступный IP-адрес.

1.  Используйте поле поиска для поиска **общедоступного IP-адреса**.

    ![Список общедоступных IP-адресов](./media/oracle-backup-recovery/create_ip_00.png)

2.  В левом верхнем углу щелкните **Добавить**. Для **имени** выберите общедоступное имя IP-адреса, а для **группы ресурсов** выберите **использование существующей**. Щелкните **Создать**.

    ![Создание IP-адреса](./media/oracle-backup-recovery/create_ip_01.png)

3.  Чтобы связать общедоступный IP-адрес с сетевым интерфейсом для виртуальной машины, найдите и выберите **myVMip**, а затем щелкните **Связать**.

    ![Связывание IP-адреса](./media/oracle-backup-recovery/create_ip_02.png)

4.  Для **типа ресурса** выберите **Сетевой интерфейс**, выберите сетевой интерфейс, используемый экземпляром myVM, а затем нажмите кнопку **ОК**.

    ![Выбор значений типа ресурса и сетевого интерфейса](./media/oracle-backup-recovery/create_ip_03.png)

5.  Найдите и откройте экземпляр myVM, перенесенный из портала. IP-адрес, связанный с виртуальной машиной, появится в правом верхнем углу.

    ![Значение IP-адреса](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Шаг 4. Подключение к виртуальной машине

*   Для подключения выполните сценарий ниже:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Шаг 5. Проверка доступности базы данных
*   Чтобы проверить доступность базы данных, выполните сценарий ниже:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

> [!IMPORTANT]
> Если команда базы данных **startup** вызывает ошибку, то чтобы восстановить базу данных, используйте инструкции, описанные в разделе ниже.

### <a name="step-6-use-rman-to-recover-the-database-optional-step"></a>Шаг 6. Использование RMAN для восстановления базы данных (необязательно)
*   Чтобы восстановить базу данных, выполните сценарий ниже:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Вы успешно выполнили резервное копирование и восстановление базы данных Oracle 12c на виртуальной машине Linux в Azure.
## <a name="delete-the-vm"></a>Удаление виртуальной машины

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Изучите [руководство по созданию высокодоступных виртуальных машин](../../linux/create-cli-complete.md).

[Изучите примеры развертывания виртуальных машин с помощью интерфейса командной строки](../../linux/cli-samples.md).


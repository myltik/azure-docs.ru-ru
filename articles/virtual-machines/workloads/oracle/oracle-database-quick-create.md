---
title: "Создание базы данных Oracle 12c на виртуальной машине Azure | Документы Майкрософт"
description: "Быстрое создание и запуск базы данных Oracle 12c в среде Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/26/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 38cb5c4c1beb0e50c8a6395afb118c63b2750210
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017

---

# <a name="create-an-oracle-database-12c-database-in-an-azure-virtual-machine"></a>Создание базы данных Oracle 12c на виртуальной машине Azure

С помощью Azure CLI можно создать ресурсы Azure и управлять ими из командной строки или с помощью скриптов. В этой статье используются скрипты в Azure CLI для развертывания базы данных Oracle 12c из коллекции образов Azure Marketplace.

Прежде чем начать, убедитесь, что установлен Azure CLI. Дополнительные сведения см. в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="sign-in-to-azure"></a>Вход в Azure 

Для входа в подписку Azure в Azure CLI используйте команду [az login](/cli/azure/#login). Затем выполните инструкции на экране.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов, используйте команду [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>Создание виртуальной машины

Чтобы создать виртуальную машину, выполните команду [az vm create](/cli/azure/vm#create). 

В следующем примере создается виртуальная машина `myVM`. Также создаются ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

После создания виртуальной машины в Azure CLI отображается информация следующего вида. Обратите внимание на значение `publicIpAddress`. Этот адрес используется для доступа к виртуальной машине.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Используйте следующую команду для создания сеанса SSH с виртуальной машиной. Замените IP-адрес общедоступным IP-адресом виртуальной машины (значение `publicIpAddress`).

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Создание базы данных

Программное обеспечение Oracle уже установлено в образе Marketplace. Затем установите базу данных. 

1.  Запустите суперпользователя *oracle* и инициализируйте прослушиватель для ведения журнала.

    ```bash
    sudo su - oracle
    [oracle@myVM /]$ lsnrctl start
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

2.  Создание базы данных

    ```bash
    [oracle@myVM /]$ dbca -silent \
    -createDatabase \
    -templateName General_Purpose.dbc \
    -gdbname cdb1 \
    -sid cdb1 \
    -responseFile NO_VALUE \
    -characterSet AL32UTF8 \
    -sysPassword OraPasswd1 \
    -systemPassword OraPasswd1 \
    -createAsContainerDatabase true \
    -numberOfPDBs 1 \
    -pdbName pdb1 \
    -pdbAdminPassword OraPasswd1 \
    -databaseType MULTIPURPOSE \
    -automaticMemoryManagement false \
    -storageType FS \
    -ignorePreReqs

    Copying database files
    1% complete
    2% complete
    8% complete
    13% complete
    19% complete
    27% complete
    Creating and starting Oracle instance
    29% complete
    32% complete
    33% complete
    34% complete
    38% complete
    42% complete
    43% complete
    45% complete
    Completing Database Creation
    48% complete
    51% complete
    53% complete
    62% complete
    70% complete
    72% complete
    Creating Pluggable Databases
    78% complete
    100% complete
    Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
    ```

## <a name="prepare-for-connectivity"></a>Подготовка к подключению 
Чтобы убедиться, что база данных была правильно инициализирована, проверьте локальное подключение. Проще всего это сделать с помощью командной строки `sqlplus`.  

Перед подключением необходимо настроить некоторые переменные среды, в частности ORACLE_HOME и ORACLE_SID.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

При необходимости можно добавить ORACLE_HOME и ORACLE_SID в файл BASHRC. Это позволит сохранить переменные среды для будущих входов.

```
# Add ORACLE_HOME.
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# Add ORACLE_SID.
export ORACLE_SID=cdb1

```

## <a name="oracle-em-express-connectivity"></a>Подключение к Oracle EM Express

Мы включим в Oracle EM Express инструмент управления графическим интерфейсом пользователя, чтобы была возможность просматривать базу данных. Для подключения к Oracle EM Express сначала необходимо настроить порт в Oracle.

```bash
$ sudo su - oracle

sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> select con_id, name, open_mode from v$pdbs;

    CON_ID NAME                           OPEN_MODE
---------- ------------------------------ ----------
         2 PDB$SEED                       READ ONLY
         3 PDB1                           MOUNT

SQL> alter session set container=pdb1;

Session altered.

SQL> alter database open;

database opened.

SQL> alter session set container=pdb1;

Session altered.

SQL> exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);

PL/SQL procedure successfully completed.
```

## <a name="automate-database-startup-and-shutdown"></a>Автоматизация запуска и завершения работы базы данных

По умолчанию база данных Oracle не запускается автоматически при запуске виртуальной машины. Чтобы настроить базу данных Oracle для запуска при запуске виртуальной машины, выполните вход с правами root. Затем создайте и обновите некоторые системные файлы.

1.  Выполните вход в качестве привилегированного пользователя.

    ```bash
    # sudo su -
    ```

2.  В файле /etc/oratab измените значение по умолчанию `N` на `Y`.

    ```
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Создайте файл /etc/init.d/dbora.

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Измените разрешения.

    ```bash
    # chgrp dba /etc/init.d/dbora
    # chmod 750 /etc/init.d/dbora
    ```

5.  Создайте символьные ссылки для запуска и завершения работы.

    ```bash
    # ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Чтобы проверить изменения, перезапустите виртуальную машину.

    ```bash
    # reboot
    ```

## <a name="open-ports-for-connectivity"></a>Открытие портов для подключения

Последнее, что следует сделать, — настроить несколько внешних конечных точек. Чтобы настроить группу безопасности сети Azure, которая защищает виртуальную машину, сначала следует выйти из сеанса SSH на виртуальной машине. 

1.  Чтобы открыть конечную точку для удаленного доступа к базе данных Oracle, выполните следующую команду: 

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle\
        --protocol tcp --direction inbound --priority 999 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 1521 --access allow
    ```

    Результаты должны выглядеть примерно как в приведенном примере.

    ```
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1521",
    "direction": "Inbound",
    "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
    "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
    "name": "allow-oracle",
    "priority": 999,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

2.  Чтобы открыть конечную точку для удаленного доступа к Oracle EM Express, выполните следующую команду:

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle-EM\
        --protocol tcp --direction inbound --priority 1001 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 5502 --access allow
    ```

    Результаты должны выглядеть примерно как в приведенном примере.

    ```azurecli
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "5502",
    "direction": "Inbound",
    "etag": "W/\"06c68b5e-1b3f-4ae0-bcf6-59b3b981d685\"",
    "id": "/subscriptions/2dad32d6-b188-49e6-9437-ca1d51cec4dd/resourceGroups/kennyRG/providers/Microsoft.Network/networkSecurityGroups/kennyVM1NSG/securityRules/allow-oracle-EM",
    "name": "allow-oracle-EM",
    "priority": 1001,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

3.  Подключите EM Express из своего браузера. 

    ```
    https://<VM hostname>:5502/em
    ```

Можно выполнить вход, используя учетную запись SYS с паролем, указанным во время установки.


## <a name="delete-the-vm"></a>Удаление виртуальной машины

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Изучите [руководство по созданию высокодоступных виртуальных машин](../../linux/create-cli-complete.md).

[Изучите примеры развертывания виртуальных машин с помощью интерфейса командной строки](../../linux/cli-samples.md).


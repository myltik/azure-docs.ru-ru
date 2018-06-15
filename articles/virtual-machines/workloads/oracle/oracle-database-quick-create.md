---
title: Создание базы данных Oracle на виртуальной машине Azure | Документация Майкрософт
description: Быстрое создание и запуск базы данных Oracle 12c в среде Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: rclaus
ms.openlocfilehash: 8ff463b89b395947a66db6d067b6ba32281087ba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657904"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Создание базы данных Oracle на виртуальной машине Azure

В этом руководстве объясняется, как с помощью Azure CLI развернуть виртуальную машину Azure, используя [образ из коллекции Oracle Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview), чтобы создать базу данных Oracle 12c. После развертывания сервера вы подключитесь к нему по протоколу SSH для настройки базы данных Oracle. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Создание виртуальной машины

Чтобы создать виртуальную машину, выполните команду [az vm create](/cli/azure/vm#az_vm_create). 

В следующем примере создается виртуальная машина `myVM`. Также создаются ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

После создания виртуальной машины в Azure CLI отображается информация следующего вида. Обратите внимание на значение `publicIpAddress`. Этот адрес используется для доступа к виртуальной машине.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
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

Программное обеспечение Oracle уже установлено в образе Marketplace. Создайте пример базы данных, как описано ниже. 

1.  Переключитесь на учетную запись суперпользователя *oracle* и инициализируйте прослушиватель для ведения журнала.

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    Выход аналогичен приведенному ниже:

    ```bash
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
    dbca -silent \
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
    ```

    Создание базы данных занимает несколько минут.

3. Задайте переменные Oracle.

Прежде чем подключиться, необходимо задать две переменные среды: *ORACLE_HOME* и *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
При необходимости можно добавить переменные ORACLE_HOME и ORACLE_SID в BASHRC-файл. Это позволит сохранить переменные среды для следующих входов в систему. Добавьте следующие инструкции в файл `~/.bashrc` с помощью любого редактора.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Подключение к Oracle EM Express

Мы включим в Oracle EM Express инструмент управления графическим интерфейсом пользователя, чтобы была возможность просматривать базу данных. Для подключения к Oracle EM Express сначала необходимо настроить порт в Oracle. 

1. Подключитесь к базе данных с помощью SQL Plus.

    ```bash
    sqlplus / as sysdba
    ```

2. После подключения задайте порт 5502 для EM Express.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Откройте контейнер PDB1, если вы еще не сделали этого, но сначала проверьте его состояние.

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Выход аналогичен приведенному ниже:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Если значение переменной OPEN_MODE для `PDB1` отличается от READ WRITE, выполните следующие команды, чтобы открыть PDB1.

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Теперь введите команду `quit` для завершения сеанса sqlplus, а затем `exit` для завершения сеанса пользователя oracle.

## <a name="automate-database-startup-and-shutdown"></a>Автоматизация запуска и завершения работы базы данных

По умолчанию база данных Oracle не запускается автоматически при перезапуске виртуальной машины. Чтобы база данных Oracle запускалась автоматически, войдите с правами привилегированного пользователя. Затем создайте и обновите некоторые системные файлы.

1. Войдите с правами привилегированного пользователя.
    ```bash
    sudo su -
    ```

2.  В любом удобном редакторе откройте файл `/etc/oratab` и замените значение по умолчанию `N` на `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Создайте файл с именем `/etc/init.d/dbora` и вставьте в него следующее содержимое:

    ```
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

4.  Измените разрешения для файлов с помощью команды *chmod*:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Создайте символьные ссылки для запуска и завершения работы:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Чтобы проверить изменения, перезапустите виртуальную машину.

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Открытие портов для подключения

Последнее, что следует сделать, — настроить несколько внешних конечных точек. Чтобы настроить группу безопасности сети Azure, которая защищает виртуальную машину, сначала следует завершить сеанс SSH на виртуальной машине. (Завершение сеанса SSH должно был произойти при перезапуске на предыдущем шаге.) 

1.  Чтобы открыть конечную точку для удаленного доступа к базе данных Oracle, создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create): 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Чтобы открыть конечную точку для удаленного доступа к Oracle EM Express, создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. При необходимости получите общедоступный IP-адрес виртуальной машины еще раз с помощью команды [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Подключите EM Express с помощью браузера. Убедитесь, что браузер совместим с EM Express (также необходимо установить Flash). 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Вы можете войти с помощью учетной записи **SYS** и установить флажок **as sysdba**. Используйте пароль **OraPasswd1**, заданный во время установки. 

![Снимок экрана со страницей входа Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы завершите изучение первой базы данных Oracle в Azure, вы можете удалить ненужную виртуальную машину, группу ресурсов и все связанные с ней ресурсы с помощью команды [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте о других [решениях Oracle в Azure](oracle-considerations.md). 

Ознакомьтесь с руководством по [установке и настройке Oracle ASM](configure-oracle-asm.md).
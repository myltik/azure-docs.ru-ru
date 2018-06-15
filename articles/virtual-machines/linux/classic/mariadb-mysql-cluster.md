---
title: Запуск кластера MariaDB (MySQL) в Azure | Документация Майкрософт
description: Создание кластеров MariaDB + Galera (MySQL) на виртуальных машинах Azure
services: virtual-machines-linux
documentationcenter: ''
author: sabbour
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 4a3eede532345f8628af1722a06531571f01afbf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32191957"
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Кластер MariaDB (MySQL) — руководство по Azure
> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager](../../../resource-manager-deployment-model.md) и классическая модель. В этой статье рассматривается классическая модель развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель Azure Resource Manager.

> [!NOTE]
> Кластер MariaDB Enterprise теперь доступен в Azure Marketplace. Новое предложение автоматически развертывает кластер MariaDB Galera в Azure Resource Manager. Воспользуйтесь новым предложением от [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/).
>
>

В этой статье показано, как создать кластер [Galera](http://galeracluster.com/products/) с несколькими базами данных master [MariaDB](https://mariadb.org/en/about/) — надежную масштабируемую замену MySQL для работы в высокодоступной среде на виртуальных машинах Azure.

## <a name="architecture-overview"></a>Общие сведения об архитектуре
В этой статье объясняется, как выполнить следующие действия:

- Создать кластер с тремя узлами.
- Отделить диски данных от диска операционной системы.
- Создать диски данных при установленном параметре RAID-0/striped для увеличения количества операций ввода-вывода в секунду.
- Использовать Azure Load Balancer для балансировки нагрузки на трех узлах.
- Чтобы свести к минимуму выполнение повторяющихся задач, создайте образ виртуальной машины, содержащий MariaDB + Galera, и используйте его для создания других виртуальных машин кластера.

![Архитектура системы](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> В этом разделе используются инструменты [интерфейса командной строки Azure](../../../cli-install-nodejs.md), поэтому обязательно скачайте и подключите их к своей подписке Azure в соответствии с инструкциями. Если вам нужна справка по командам, доступным в интерфейсе командной строки Azure (Azure CLI), ознакомьтесь со [справочником по командам Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Также вам потребуется [создать ключ SSH для проверки подлинности] и записать расположение PEM-файла.
>
>

## <a name="create-the-template"></a>Создание шаблона
### <a name="infrastructure"></a>Инфраструктура
1. Создайте территориальную группу для централизованного хранения ресурсов.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Создайте виртуальную сеть.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Создайте учетную запись хранения для размещения всех дисков. В одной учетной записи хранения не рекомендуется размещать более 40 интенсивно используемых дисков, чтобы не превысить ограничение в 20 000 операций ввода-вывода в секунду для учетной записи хранения. В нашем случае выполняется намного меньше операций, поэтому все данные будут храниться в одной учетной записи для упрощения описания.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Найдите имя образа виртуальной машины CentOS 7.

        azure vm image list | findstr CentOS
   Результат будет выглядеть примерно так:`5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Используйте это имя на следующем шаге.
5. Создайте шаблон виртуальной машины и замените адрес /path/to/key.pem на путь к папке, в которой вы сохранили созданный ключ SSH в формате PEM.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Присоедините четыре диска данных по 500 ГБ к виртуальной машине для использования в конфигурации RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Используйте SSH для входа в шаблон виртуальной машины, созданной по адресу mariadbhatemplate.cloudapp.net:22, и подключитесь с помощью закрытого ключа.

### <a name="software"></a>Программное обеспечение
1. Получите права привилегированного пользователя.

        sudo su

2. Установка поддержки RAID:

    a. Установите mdadm.

              yum install mdadm

    Б. Создайте конфигурацию RAID0/stripe для файловой системы EXT4.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Создайте каталог точек подключения.

              mkdir /mnt/data
    d. Извлеките UUID созданного RAID-устройства.

              blkid | grep /dev/md0
    д. Измените файл /etc/fstab.

              vi /etc/fstab
    f. Добавьте устройство, чтобы включить автоматическое подключение при перезагрузке, заменив UUID значением, полученным ранее в результате выполнения команды **blkid**.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    ж. Подключите новый раздел.

              mount /mnt/data

3. Установите MariaDB.

    a. Создайте файл MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    Б. Заполните REPO-файл следующим содержимым.

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Чтобы избежать конфликтов, удалите существующие элементы postfix и mariadb-libs.

           yum remove postfix mariadb-libs-*
    d. Установите MariaDB с Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Переместите каталог данных MySQL в блочное устройство RAID.

    a. Скопируйте текущий каталог MySQL в новое расположение и удалите старый каталог.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    Б. Задайте разрешения для нового каталога соответствующим образом.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Создайте символьную ссылку, указывающую на старый каталог в новом расположении раздела RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Так как средство [SELinux будет препятствовать выполнению операций в кластере](http://galeracluster.com/documentation-webpages/configuration.html#selinux), необходимо отключить его во время текущего сеанса. Измените файл `/etc/selinux/config`, чтобы отключить его на случай последующих перезапусков.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Убедитесь, что MySQL работает.

   a. Запустите MySQL.

           service mysql start
   Б. Дождитесь завершения установки MySQL, задайте пароль привилегированного пользователя и удалите анонимных пользователей, чтобы отключить удаленный вход привилегированных пользователей. Также удалите тестовую базу данных.

           mysql_secure_installation
   c. Создайте пользователя в базе данных, чтобы выполнять операции с кластером и по желанию со своими приложениями.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Остановите работу MySQL.

            service mysql stop
7. Создайте заполнитель конфигурации.

   a. Измените конфигурацию MySQL, чтобы создать заполнитель для параметров кластера. Не заменяйте элемент **`<Variables>`** или раскомментируйте его сейчас. Это произойдет после создания виртуальной машины из этого шаблона.

            vi /etc/my.cnf.d/server.cnf
   Б. Измените раздел **[galera]** и очистите его.

   c. Измените раздел **[mariadb]**.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. Откройте необходимые порты на брандмауэре с помощью FirewallD CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Перезагрузите брандмауэр: `firewall-cmd --reload`

9. Оптимизируйте производительность системы. Дополнительные сведения см. в статье [Оптимизация производительности MySQL в виртуальных машинах Azure Linux](optimize-mysql.md).

   a. Снова измените файл конфигурации MySQL.

            vi /etc/my.cnf.d/server.cnf
   Б. Внесите изменения в раздел **[mariadb]** и добавьте следующее содержимое.

   > [!NOTE]
   > Рекомендуемое значение innodb\_buffer\_pool_size составляет 70 % от памяти виртуальной машины. В этом примере для него задано значение 2,45 ГБ для виртуальных машин Azure среднего размера с 3,5 ГБ ОЗУ.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Остановите MySQL, отключите выполнение службы MySQL при запуске, чтобы предотвратить вмешательство в работу кластера при добавлении узла, и отзовите машину.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Запишите виртуальную машину с помощью портала. (В настоящее время [проблема № 1268 со средствами Azure CLI ](https://github.com/Azure/azure-xplat-cli/issues/1268) состоит в том, что в образы, созданные средствами Azure CLI, не записываются на подключенные диски данных.)

    a. Завершите работу машины на портале.

    Б. Щелкните **Запись** и укажите имя образа **mariadb-galera-image**. Введите описание и установите флажок "команда waagent -deprovision запущена на виртуальной машине".
      
      ![Запись виртуальной машины](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Создание кластера
Создайте три виртуальные машины из созданного шаблона, а затем настройте и запустите кластер.

1. Создайте первую виртуальную машину CentOS 7 на основе созданного образа mariadb-galera-image, указав следующую информацию:

 - Имя виртуальной сети: mariadbvnet.
 - Подсеть: mariadb.
 - Размер машины: средний.
 - Имя облачной службы: mariadbha (или любое имя для облачной службы, к которой вы хотите получить доступ по адресу mariadbha.cloudapp.net).
 - Имя машины: mariadb1.
 - Имя пользователя: azureuser.
 - SSH-доступ: включен.
 - Передайте PEM-файл сертификата SSH и замените адрес /path/to/key.pem на путь, по которому вы сохранили созданный ключ SSH в формате PEM.

   > [!NOTE]
   > Следующие команды разделены на несколько строк для ясности, но каждую из них следует вводить в одной строке.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. Создайте еще две виртуальные машины, подключив их к облачной службе mariadbha. Измените имя виртуальной машины, а также замените порт SSH на уникальный порт, не конфликтующий с другими виртуальными машинами в той же облачной службе.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  Для MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. Для выполнения следующего шага потребуется получить внутренний IP-адрес каждой из трех виртуальных машин:

    ![Получение IP-адреса](./media/mariadb-mysql-cluster/IP.png)
4. С помощью протокола SSH войдите на три виртуальные машины и измените файл конфигурации на каждой из них.

        sudo vi /etc/my.cnf.d/server.cnf

    Раскомментируйте **`wsrep_cluster_name`** и **`wsrep_cluster_address`**, удалив **#** в начале строки.
    Кроме того, замените**`<ServerIP>`** в **`wsrep_node_address`** и **`<NodeName>`** в **`wsrep_node_name`** на IP-адрес и имя виртуальной машины соответственно, а также раскомментируйте эти строки.
5. Запустите кластер в MariaDB1 и разрешите его выполнение при запуске.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Запустите MySQL в MariaDB2 и MariaDB3 и разрешите его выполнение при запуске.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Балансировка нагрузки в кластере
При создании кластерных виртуальных машин вы добавляли их в группу доступности с именем clusteravset, чтобы они размещались в разных доменах сбоя и обновления, а служба Azure никогда не выполняла обслуживание на всех машинах одновременно. Эта конфигурация соответствует требованиям к поддержке, изложенным в соответствующем соглашении об уровне обслуживания (SLA).

Теперь распределите запросы между тремя узлами с помощью Azure Load Balancer.

Выполните на машине следующие команды, используя Azure CLI.

Структура параметров команды должна быть следующей: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Интерфейс командной строки задает для интервала пробы подсистемы балансировки нагрузки значение 15 секунд, однако такой интервал может оказаться слишком длинным. Измените его на портале в разделе **Конечные точки** для любой из виртуальных машин.

![Редактирование конечной точки](./media/mariadb-mysql-cluster/Endpoint.PNG)

Установите флажок **Изменить настройку набора балансировки нагрузки**.

![Изменить настройку набора балансировки нагрузки](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Измените значение **интервала пробы** на 5 секунд и сохраните изменения.

![Изменение интервала пробы](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Проверка кластера
Все самое сложное уже проделано. Теперь кластер должен быть доступен по адресу `mariadbha.cloudapp.net:3306`, попадающему в зону действия подсистемы балансировки нагрузки, и обеспечивать легкую и эффективную маршрутизацию запросов между тремя виртуальными машинами.

Используйте избранный клиент MySQL для подключения или подключитесь с одной из виртуальных машин, чтобы проверить работоспособность кластера.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Затем создайте базу данных и заполните ее данными.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Созданная база данных возвращает следующую таблицу:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Дополнительная информация
В этой статье вы создали высокодоступный кластер MariaDB + Galera, состоящий из трех узлов, на виртуальных машинах Azure под управлением CentOS 7. Балансировка нагрузки на виртуальных машинах выполняется с помощью Azure Load Balancer.

Возможно, вам следует ознакомиться с [другим способом кластеризации MySQL в ОС Linux](mysql-cluster.md), а также с методами [оптимизации и тестирования производительности MySQL на виртуальных машинах Azure Linux](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[создать ключ SSH для проверки подлинности]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268

<properties
	pageTitle="Запуск кластера MariaDB (MySQL) в Azure"
	description="Создание кластеров MariaDB + Galera MySQL в виртуальных машинах Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="sabbour"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/15/2015"
	ms.author="v-ahsab"/>

# Кластер MariaDB (MySQL) — учебник по Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.

> [AZURE.NOTE]  Кластер MariaDB Enterprise теперь доступен в Azure Marketplace. Новое предложение автоматически разворачивает кластер MariaDB Galera на ARM. Воспользуйтесь новым предложением от https://azure.microsoft.com/ru-RU/marketplace/partners/mariadb/cluster-maxscale/

Мы создаем кластер [Galera](http://galeracluster.com/products/) с несколькими базами данных master [MariaDB](https://mariadb.org/en/about/) — надежную и масштабируемую замену для MySQL для работы в высокодоступной среде в виртуальных машинах Azure.

## Общие сведения об архитектуре

В этом разделе выполняются следующие действия.

1. Создается кластер из 3 узлов.
2. Диски данных отделяются от диска операционной системы.
3. Диски данных создаются при установленном параметре RAID-0/striped для увеличения количества операций ввода-вывода в секунду.
4. Подсистема балансировки нагрузки Azure используется для балансировки нагрузки для 3 узлов.
5. Чтобы свести к минимуму выполнение повторяющихся задач, создайте образ виртуальной машины, содержащий MariaDB + Galera, и используйте его для создания других виртуальных машин кластера.

![Архитектура](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  В этом разделе используются средства [CLI Azure], поэтому обязательно загрузите и подключите их к вашей подписке Azure в соответствии с инструкциями. Если вам нужна справка по командам, доступным в CLI Azure, щелкните эту ссылку на [справочник по командам CLI Azure]. Также потребуется [создать ключ SSH для проверки подлинности] и записать **расположение PEM-файла**.


## Создание шаблона

### Инфраструктура

1. Создание территориальной группы для хранения ресурсов в одном расположении

		azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Создайте виртуальную сеть

		azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Создайте учетную запись хранилища для размещения всех дисков. Обратите внимание, что в одной учетной записи хранилища не рекомендуется размещать более 40 интенсивно используемых дисков, чтобы избежать превышения ограничения в 20 000 операций ввода-вывода в секунду для учетной записи хранилища. В этом случае выполняется намного меньше операций, поэтому все данные будут храниться в одной учетной записи для упрощения описания.

		azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Найдите имя образа виртуальной машины CentOS 7.

		azure vm image list | findstr CentOS
При этом будет выведено примерно следующее: `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Используйте это имя на следующем шаге.

4. Создайте шаблон виртуальной машины и замените адрес **/path/to/key.pem** на путь, по которому вы сохранили созданный ключ SSH в формате PEM.

		azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Присоедините 4 диска данных по 500 ГБ к виртуальной машине для использования в SSH конфигурации RAID

		FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. в шаблоне виртуальной машины, созданной по адресу **mariadbhatemplate.cloudapp.net:22**, и подключитесь с помощью закрытого ключа.

### Программное обеспечение

1. Получение пароля root

        sudo su

2. Установка поддержки RAID:

     - Установите mdadm.

        		yum install mdadm

     - Создайте конфигурацию RAID0/stripe для файловой системы EXT4.

				mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
				mdadm --detail --scan >> /etc/mdadm.conf
				mkfs -t ext4 /dev/md0

     - Создайте каталог точек подключения.

				mkdir /mnt/data

     - Извлеките UUID созданного RAID-устройства.

				blkid | grep /dev/md0

     - Измените файл /etc/fstab.

        		vi /etc/fstab

     - Добавьте устройство, чтобы включить автоматическое подключение при перезагрузке, заменив UUID значением, полученным в результате выполнения команды **blkid**.

        		UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Подключите новый раздел.

        		mount /mnt/data

3. Установка MariaDB:

     - Создайте файл MariaDB.repo.

              	vi /etc/yum.repos.d/MariaDB.repo

     - Заполните его содержимым, приведенным ниже.

				[mariadb]
				name = MariaDB
				baseurl = http://yum.mariadb.org/10.0/centos7-amd64
				gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
				gpgcheck=1

     - Удалите существующие элементы postfix и mariadb-libs, чтобы избежать конфликтов.

    		yum remove postfix mariadb-libs-*

     - Установите MariaDB с Galera.

    		yum install MariaDB-Galera-server MariaDB-client galera

4. Переместите каталог данных MySQL в блочное устройство RAID.

     - Скопируйте текущий каталог MySQL в новое место и удалите старый каталог.

    		cp -avr /var/lib/mysql /mnt/data  
    		rm -rf /var/lib/mysql

     - Задайте разрешения на новый каталог соответствующим образом.

        	chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Создайте символическую ссылку, указывающую на старый каталог в новом месте раздела RAID.

    		ln -s /mnt/data/mysql /var/lib/mysql

5. Так как средство [SELinux будет препятствовать выполнению операций](http://galeracluster.com/documentation-webpages/configuration.html#selinux) в кластере, необходимо отключить его во время текущего сеанса (до появления совместимой версии). Измените `/etc/selinux/config`, чтобы отключить его на случай последующих перезагрузок.

	        setenforce 0

       Затем измените `/etc/selinux/config`, чтобы задать `SELINUX=permissive`

6. Убедитесь, что MySQL работает.

    - Запустите MySQL.

    		service mysql start

    - Дождитесь завершения установки MySQL, задайте корневой пароль и удалите анонимных пользователей, отключив удаленный корневой вход и тестовую базу данных.

            mysql_secure_installation

    - Создайте пользователя в базе данных, чтобы выполнять операции с кластером и по желанию со своими приложениями:

			mysql -u root -p
			GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Остановите работу MySQL.

			service mysql stop

7. Создание заполнителя конфигурации

	- Измените конфигурацию MySQL, чтобы создать заполнитель для параметров кластера. Не заменяйте элемент **`<Vairables>`** или раскомментируйте его сейчас. Это произойдет после создания виртуальной машины из этого шаблона.

			vi /etc/my.cnf.d/server.cnf

	- Измените раздел **[galera]** и очистите его.

	- Измените раздел **[mariadb]**.

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

8. Откройте необходимые порты на брандмауэре (с помощью FirewallD CentOS 7).

	- MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Перезагрузите брандмауэр: `firewall-cmd --reload`

9.  Оптимизируйте производительность системы. Дополнительные сведения см. в этой статье по [стратегии оптимизации производительности].

	- Снова измените файл конфигурации MySQL.

			vi /etc/my.cnf.d/server.cnf

	- Внесите изменения в раздел **[mariadb]** и добавьте следующие данные.

	> [AZURE.NOTE] Мы рекомендуем, чтобы значение **innodb\_buffer\_pool\_size** составляло 70 % от памяти виртуальной машины. Здесь задано значение 2,45 ГБ для виртуальной машины Azure средней емкости с 3,5 ГБ ОЗУ.

	        innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
			query_cache_size = 0

10. Остановите MySQL, отключите выполнение службы MySQL при запуске, чтобы предотвратить вмешательство в работу кластера при добавлении нового узла, и отзовите машину.

		service mysql stop
        chkconfig mysql off
		waagent -deprovision

11. Запишите виртуальную машину с помощью портала. (В настоящее время [проблема № 1268 со средствами CLI Azure] состоит в том, что в образы, созданные средствами CLI Azure, не записываются на подключенные диски данных.)

	- Завершите работу машины на портале.
    - Щелкните «Записать» и укажите имя образа **mariadb-galera-image** и его описание, а также установите флажок «Я запустил (-а) waagent». ![Запись виртуальной машины](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png) ![Запись виртуальной машины](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## Создание кластера

Создайте 3 виртуальные машины из недавно созданного шаблона, а затем настройте и запустите кластер.

1. Создайте первую виртуальную машину CentOS 7 из созданного образа **mariadb-galera-image**. При этом укажите имя виртуальной сети **mariadbvnet** и подсеть **mariadb**, задайте для размера машины значение **Средний**, установите имя облачной службы **mariadbha** (или другое имя, к которому вы хотите получить доступ по адресу mariadbha.cloudapp.net), задайте для этой машины имя **mariadb1** и имя пользователя **azureuser**, включите доступ по протоколу SSH и передайте PEM-файл сертификата SSH, заменив **/path/to/key.pem** на путь, по которому вы сохранили созданный ключ SSH в формате PEM.

	> [AZURE.NOTE] Следующие команды разделены на несколько строк для ясности, но следует вводить в одной строке.

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

2. Создайте 2 дополнительные виртуальные машины, _подключив_ их к созданной облачной службе **mariadbha**, изменив **имя виртуальной машины**, а также **SSH-порт** на уникальный порт, который не конфликтует с другими виртуальными машинами в этой же облачной службе.

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
и для MariaDB3.

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

	![Получение IP-адреса](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. Введите ключ SSH для 3 виртуальных машин, а затем измените файл конфигурации на каждой из них,

		sudo vi /etc/my.cnf.d/server.cnf

	раскомментировав строки **`wsrep_cluster_name`** и **`wsrep_cluster_address`** за счет удаления стоящего в начале знака **#** и убедившись, что это именно то, что вам нужно. Кроме того, замените **`<ServerIP>`** в **`wsrep_node_address`** и **`<NodeName>`** в **`wsrep_node_name`** на IP-адрес и имя виртуальной машины соответственно, а также раскомментируйте эти строки.

5. Запустите кластер в MariaDB1 и разрешите его выполнение при запуске.

		sudo service mysql bootstrap
        chkconfig mysql on

6. Запустите MySQL на MariaDB2 и MariaDB3 и разрешите его выполнение при запуске.

		sudo service mysql start
        chkconfig mysql on

## Балансировка нагрузки на кластере
Когда вы создавали кластерные виртуальные машины, они добавлялись в группу доступности с именем **clusteravset**, чтобы они размещались в разных доменах сбоя и обновления, а служба Azure никогда не выполняла обслуживание на всех машинах одновременно. Эта конфигурация соответствует требованиям к поддержке, изложенным в соответствующем соглашении об уровне обслуживания (SLA).

Теперь вы будете использовать подсистему балансировки нагрузки Azure, чтобы распределить запросы между нашими 3 узлами.

Выполните на машине следующие команды, используя CLI Azure. Структура параметров команды должна быть следующей: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

	azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Наконец, так как CLI устанавливает для интервала проверки подсистемы балансировки нагрузки значение «15 секунд» (которое может быть слишком длинным), измените его на портале в разделе **Конечные точки** для любых виртуальных машин.

![Редактирование конечной точки](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

Затем щелкните «Перенастроить набор со сбалансированной нагрузкой» и нажмите кнопку «Далее».

![Перенастройка набора балансировки нагрузки](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

После этого измените интервал проверки на «5 секунд» и сохраните изменения.

![Интервал проверки изменений](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## Проверка кластера

Все самое сложное уже проделано. Теперь кластер должен быть доступен по адресу `mariadbha.cloudapp.net:3306`, попадающему в зону действия подсистемы балансировки нагрузки и обеспечивающему легкую и эффективную маршрутизацию запросов между тремя виртуальными машинами.

Используйте избранный клиент MySQL для подключения или подключитесь с одной из виртуальных машин, чтобы проверить работоспособность кластера.

	 mysql -u cluster -h mariadbha.cloudapp.net -p

Затем создайте базу данных и заполните ее данными.

	CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
	INSERT INTO TestTable (value)  VALUES ('Value1');
	INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Будут получены результаты, показанные в следующей таблице.

	+----+--------+
	| id | value  |
	+----+--------+
	|  1 | Value1 |
	|  4 | Value2 |
	+----+--------+
	2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

В этой статье вы создали кластер высокой доступности из 3 узлов MariaDB + Galera на виртуальных машинах Azure под управлением CentOS 7. Балансировка нагрузки на виртуальных машинах выполняется с помощью подсистемы балансировки нагрузки Azure.

Возможно, вам следует ознакомиться с [другим способом кластеризации MySQL в ОС Linux], а также методами [оптимизации и тестирования производительности MySQL на виртуальных машинах Azure Linux].

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[CLI Azure]: ../xplat-cli.md
[справочник по командам CLI Azure]: ../virtual-machines-command-line-tools.md
[создать ключ SSH для проверки подлинности]: http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[стратегии оптимизации производительности]: virtual-machines-linux-optimize-mysql-perf.md
[оптимизации и тестирования производительности MySQL на виртуальных машинах Azure Linux]: virtual-machines-linux-optimize-mysql-perf.md
[проблема № 1268 со средствами CLI Azure]: https://github.com/Azure/azure-xplat-cli/issues/1268
[другим способом кластеризации MySQL в ОС Linux]: virtual-machines-linux-mysql-cluster.md

<!---HONumber=AcomDC_0323_2016-->
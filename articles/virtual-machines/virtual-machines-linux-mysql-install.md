<properties
	pageTitle="Настройка MySQL на виртуальной машине Linux | Microsoft Azure "
	description="Узнайте, как установить стек MySQL на виртуальной машине Linux (из семейства ОС Ubuntu или RedHat) в Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="mingzhan"/>


#Как установить MySQL в Azure


Из этой статьи вы узнаете, как установить и настроить СУБД MySQL на виртуальной машине Azure под управлением Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##Установка MySQL в виртуальной машине

> [AZURE.NOTE] Для выполнения инструкций учебника у вас уже должна быть виртуальная машина Microsoft Azure под управлением Linux. Прежде чем продолжить, создайте и настройте виртуальную машину под управлением Linux с `mysqlnode` в качестве имени виртуальной машины и `azureuser` в качестве пользователя, используя сведения, приведенные в [учебнике по виртуальным машинам Azure под управлением Linux](virtual-machines-linux-quick-create-cli.md).

(В качестве порта MySQL используйте порт 3306.)

Подключитесь к виртуальной машине под управлением Linux, используя утилиту PuTTY. Если вы выполняете такое подключение впервые, ознакомьтесь с информацией о подключении с помощью утилиты PuTTY [здесь](virtual-machines-linux-mac-create-ssh-keys.md).

В качестве примера в этой статье для установки MySQL5.6 мы будем использовать пакет из репозитория. В настоящее время MySQL5.6 имеет улучшенную производительность по сравнению с MySQL5.5. Дополнительные сведения см. [здесь](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###Установка MySQL5.6 в Ubuntu
Здесь мы будем использовать виртуальную машину Linux на базе Ubuntu от Azure.

- Шаг 1. Установка MySQL Server 5.6. Переключитесь на пользователя `root`:

            #[azureuser@mysqlnode:~]sudo su -

    Установите mysql-server 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Во время установки вы увидите всплывающее диалоговое окно, требующее установить пароль для пользователя root в MySQL. Укажите его здесь.

    ![изображение](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Для подтверждения введите пароль еще раз.

    ![изображение](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Шаг 2. Вход на MySQL Server

    После завершения установки MySQL Server служба MySQL запустится автоматически. Вы можете войти на MySQL Server как пользователь `root`. Для входа и ввода пароля используйте представленную ниже команду.

             #[root@mysqlnode ~]# mysql -uroot -p

- Шаг 3: Управление работающей службой MySQL

    (а) Для получения информации о состоянии службы MySQL выполните команду:

             #[root@mysqlnode ~]# service mysql status

    (б) Для запуска службы MySQL выполните команду:

             #[root@mysqlnode ~]# service mysql start

    (в) Для остановки службы MySQL выполните команду:

             #[root@mysqlnode ~]# service mysql stop

    (г) Для перезапуска службы MySQL выполните команду:

             #[root@mysqlnode ~]# service mysql restart


###Установка MySQL в ОС семейства Red Hat, например CentOS, Oracle Linux
Здесь мы будем использовать виртуальную машину Linux с CentOS или Oracle Linux.

- Шаг 1. Добавление репозитория MySQL Yum. Переключитесь на пользователя `root`:

            #[azureuser@mysqlnode:~]sudo su -

    Скачайте и установите пакет выпуска MySQL:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Шаг 2: Отредактируйте указанный ниже файл, чтобы разрешить загрузку пакета MySQL5.6 в репозиторий MySQL.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Обновите каждое значение этого файла на значения, указанные ниже:

        # *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Шаг 3. Установка MySQL из репозитория MySQL. Установите MySQL:

           #[root@mysqlnode ~]#yum install mysql-community-server

    Будут установлены пакет MySQL RPM и все связанные пакеты.

- Шаг 4. Управление работающей службой MySQL

    (а) Для проверки состояния службы сервера MySQL выполните следующую команду:

           #[root@mysqlnode ~]#service mysqld status

    (б) Для проверки работы порта сервера MySQL по умолчанию выполните следующую команду:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (в) Для запуска сервера MySQL выполните следующую команду:

           #[root@mysqlnode ~]#service mysqld start

    (г) Для остановки сервера MySQL выполните следующую команду:

           #[root@mysqlnode ~]#service mysqld stop

    (д) Для запуска MySQL при начальной загрузке системы выполните следующую команду:

           #[root@mysqlnode ~]#chkconfig mysqld on


###Установка MySQL в SUSE Linux
Здесь мы будем использовать виртуальную машину Linux с OpenSUSE.

- Шаг 1. Скачивание и установка MySQL Server

    Переключитесь на пользователя `root` с помощью следующей команды:

           #sudo su -

    Скачайте и установите пакет MySQL:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Шаг 2. Управление работающей службой MySQL

    (а) Для проверки состояния сервера MySQL выполните следующую команду:

           #[root@mysqlnode ~]# rcmysql status

    (б) Для проверки порта сервера MySQL по умолчанию выполните следующую команду:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (в) Для запуска сервера MySQL выполните следующую команду:

           #[root@mysqlnode ~]# rcmysql start

    (г) Для остановки сервера MySQL выполните следующую команду:

           #[root@mysqlnode ~]# rcmysql stop

    (д) Для запуска MySQL при начальной загрузке системы выполните следующую команду:

           #[root@mysqlnode ~]# insserv mysql

###Дальнейшее действие
Дополнительную информацию и сведения об использовании MySQL см. [здесь](https://www.mysql.com/).

<!---HONumber=AcomDC_0824_2016-->
<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Установка стека LAMP" pageTitle="Установка стека LAMP на виртуальную машину Linux" metaKeywords="" description="Узнайте, как установить стек LAMP на виртуальную машину (ВМ) Linux в Azure. Установку можно выполнить на Ubuntu или CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Установка стека LAMP на виртуальную машину Linux в Azure" authors="" solutions="" manager="" editor="" />




#Установка стека LAMP на виртуальную машину Linux в Azure

Стек LAMP состоит из следующих элементов:

- **L**inux - операционная система
- **A**pache - веб-сервер
- **M**ySQL - сервер БД
- **P**HP - язык программирования


##Установка на Ubuntu

Необходимо установить следующие пакеты:

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`
- `libapache2-mod-auth-mysql`
- `libapache2-mod-php5`
- `php5-xsl`
- `php5-gd`
- `php-pear`

Можно выполнить в виде единой команды `apt-get install`:

	apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear


##Установка на CentOS

Необходимо установить следующие пакеты:

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

Можно выполнить в виде единой команды `yum install`:

	yum install httpd mysql mysql-server php-php-mysql


Настройка
----------

1. Настройка **Apache**

	1. Необходимо будет перезапустить веб-сервер Apache. Выполните следующую команду:

			sudo /etc/init.d/apache2 restart

	2. Убедитесь, что установка выполняется. Введите в браузере [http://localhost](http://localhost). Должно появиться сообщение "It works!".

2. Настройка **MySQL**.
	1. Задайте корневой пароль для mysql, запустив следующую команду
	
			mysqladmin -u root -p password yourpassword

	2. Войдите в консоль, используя `mysql` или один из клиентов MySQL.

3. Настройка **PHP**.

	1. Включите модуль Apache PHP, выполнив следующую команду:

			sudo a2enmod php5

	2. Перезапустите Apache, выполнив следующую команду:

			sudo service apache2 restart


##Дополнительные материалы

Существует множество ресурсов, описывающих установку стека LAMP на Ubuntu.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)
- [http://fedorasolved.org/server-solutions/lamp-stack](http://fedorasolved.org/server-solutions/lamp-stack)


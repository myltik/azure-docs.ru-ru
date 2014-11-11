<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Install LAMP stack" pageTitle="Install the LAMP stack on a Linux virtual machine" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Установка стека LAMP на виртуальную машину Linux в Azure

Стек LAMP состоит из следующих элементов:

-   **L**inux — операционная система.
-   **A**pache — веб-сервер.
-   **M**ySQL — сервер БД.
-   **P**HP — язык программирования.

## Установка на Ubuntu

Необходимо установить следующие пакеты:

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`

После выполнения команды `apt-get update` для обновления локального списка пакетов вы сможете установить эти пакеты с помощью одной команды `apt-get install`:

    # sudo apt-get update
    # sudo apt-get install apache2 mysql-server php5 php5-mysql

После выполнения приведенной выше команды вам будет предложено установить эти пакеты и некоторые другие зависимости. Нажмите «y», а затем клавишу ВВОД, чтобы продолжить, и следуйте остальным указаниям по установке административного пароля для MySQL.

Это позволит вам установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL. Выполните следующую команду, чтобы увидеть другие расширения PHP, доступные как пакеты:

    # apt-cache search php5

## Установка на CentOS и Oracle Linux

Необходимо установить следующие пакеты:

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

Вы можете установить эти пакеты с помощью одной команды `yum install`:

    # sudo yum install httpd mysql mysql-server php php-mysql

После выполнения приведенной выше команды вам будет предложено установить эти пакеты и некоторые другие зависимости. Нажмите «y», а затем клавишу ВВОД, чтобы продолжить.

Это позволит вам установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL. Выполните следующую команду, чтобы увидеть другие расширения PHP, доступные как пакеты:

    # yum search php

## Установка на SUSE Linux Enterprise Server

Необходимо установить следующие пакеты:

-   apache2
-   mysql
-   apache2-mod\_php53
-   php53-mysql

Вы можете установить эти пакеты с помощью одной команды `zypper install`:

    # sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

После выполнения приведенной выше команды вам будет предложено установить эти пакеты и некоторые другие зависимости. Нажмите «y», а затем клавишу ВВОД, чтобы продолжить.

Это позволит вам установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL. Выполните следующую команду, чтобы увидеть другие расширения PHP, доступные как пакеты:

    # zypper search php

## Настройка

1.  Настройка **Apache**

    -   Выполните следующую команду, чтобы убедиться, что веб-сервер Apache запущен:

        -   Ubuntu и SLES: `sudo service apache2 restart`

        -   CentOS и Oracle: `sudo service httpd restart`

    -   Apache по умолчанию прослушивает порт 80. Возможно, для удаленного доступа к серверу Apache вам необходимо будет открыть конечную точку. Подробные инструкции см. в документации по [настройке конечных точек][настройке конечных точек].

    -   Вы можете сделать проверку, чтобы убедиться, что Apache запущен и обрабатывает содержимое. Введите в браузере `http://[MYSERVICE].cloudapp.net`, где **[MYSERVICE]** — это имя облачной службы, в которой находится ваша виртуальная машина. На некоторых дистрибутивах может открыться веб-страница по умолчанию с простым сообщением «It works!». На других вы можете увидеть более полную веб-страницу со ссылками на дополнительную документацию и содержимое для настройки сервера Apache.

2.  Настройка **MySQL**

    -   Обратите внимание, что этот шаг необязателен на Ubuntu, где пароль `root` MySQL запрашивается при установке пакета mysql-server.

    -   На других дистрибутивах задайте корневой пароль для MySQL, выполнив следующую команду:

            # mysqladmin -u root -p password yourpassword

    -   Далее можете управлять MySQL с помощью служебных программ `mysql` или `mysqladmin`.

## Дополнительные материалы

Существует множество ресурсов, описывающих установку стека LAMP на Ubuntu.

-   [][]<https://help.ubuntu.com/community/ApacheMySQLPHP></a>
-   [][1]<http://fedorasolved.org/server-solutions/lamp-stack></a>

  [настройке конечных точек]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-set-up-endpoints/
  []: https://help.ubuntu.com/community/ApacheMySQLPHP
  [1]: http://fedorasolved.org/server-solutions/lamp-stack

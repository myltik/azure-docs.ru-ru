<properties
	pageTitle="Как установить MySQL в Azure"
	description="Узнайте, как установить стек MySQL в виртуальной машине Linux в Azure. Можно выполнить установку в Ubuntu или CentOS."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/12/2015"
	ms.author="mingzhan"/>


#Как установить MySQL в Azure


В этом разделе предполагается, что у читателя уже есть учетная запись Azure. В противном случае мы советуем оформить подписку на сайте [Azure](http://azure.microsoft.com).



##Создание образа виртуальной машины в Microsoft Azure
В этом разделе мы создадим новую виртуальную машину на портале управления Microsoft Azure.
###Создание ключа аутентификации SSH
Чтобы получить доступ к порталу Azure, нам понадобится ключ SSH.


- Скачайте программу puttygen [здесь](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) и установите ее. 
- Запустите puttygen.exe.
- Нажмите кнопку «Создать», чтобы создать ключи.


 ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p01.png)
 
- После создания ключа он отобразится, как показано ниже. 
 
 ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p02.png)

- Скопируйте открытый ключ и сохраните его в файле с именем publicKey.key. Не следует нажимать кнопку «Сохранить открытый ключ», так как формат файла сохраненного открытого ключа отличается от необходимого открытого ключа.
- Нажмите кнопку «Сохранить закрытый ключ» и сохраните его как privateKey.ppk. 

###Вход на портал Azure

Перейдите по адресу https://portal.azure.com/ и войдите на портал.

###Создание виртуальной машины Linux

Щелкните «СОЗДАТЬ» в нижнем левом углу и создайте образ, следуя указаниям, выбрав образ Linux в соответствии со своими потребностями. Здесь в качестве примера выбран Ubuntu 14.04.

  ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p03.png)

###Заполнение поля «ИМЯ УЗЛА»

В поле «ИМЯ УЗЛА» указывается URL-адрес, который можно использовать для доступа к виртуальной машине. Вам нужно просто указать DNS-имя, например mysqlnode1, и Azure создаст URL-адрес, например mysqlnode1.cloudapp.net. В поле «КЛЮЧ АУТЕНТИФИКАЦИИ SSH» указывается открытый ключ, созданный с помощью программы puttygen. Его нужно скопировать из содержимого файла publicKey.key.

  ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p04.png)
  

##Открытие порта MySQL по умолчанию
Конечные точки в Microsoft Azure состоят из протокола, а также общего и частного портов. Частный порт — порт, который прослушивает служба на локальном компьютере. Общий порт — это порт, который прослушивает служба извне. Порт 3306 — номер порта по умолчанию, который будет прослушивать MySQL. Щелкните «ОБЗОР» ⇒ «ВИРТУАЛЬНАЯ МАШИНА», а затем щелкните созданное изображение.
 
   ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p05.png)


##Подключение к созданному образу
Вы можете выбрать любые инструменты SSH для подключения к своей виртуальной машине. Здесь в качестве примера мы используем Putty.
 

- Сначала скачайте Putty. URL-адрес для скачивания Putty указан здесь.
- После скачивания запустите исполняемый файл PUTTY.EXE. Установите параметры следующим образом.


     The “Host Name (or IP address)” is the URL as “DNS NAME” when you create an image.
     
     The “Port” we can chose 22.  This is default port of SSH services.

   ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p06.png)
 
- Прежде чем щелкнуть «Открыть», щелкните вкладки «Подключение \> SSH \> Аутентификация», чтобы найти и выбрать файл, созданный Puttygen, который содержит закрытый ключ. Ниже см. снимок экрана поля для заполнения.

   ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p07.png)
 
- Затем щелкните «Открыть». Может появится окно сообщения с информацией о том, что подключаемый компьютер может оказаться не тем компьютером, который требуется подключить. Если вы настроили DNS-имя и номер порта правильно, нажмите кнопку «Да».
  
 ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p08.png)

- После этого появится следующее окно. 
 
 ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p09.png)


##Установка MySQL в виртуальной машине
MySQL поддерживает три способа установки: пакеты двоичных файлов, пакеты RPM и исходные пакеты. Для сохранения производительности в качестве примера для этой статьи мы используем пакеты RPM для MySQL 5.6. Производительность MySQL 5.6 по сравнению с MySQL 5.5 значительно улучшена. Дополнительную информацию см. [здесь](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###Как установить MySQL 5.6 в Ubuntu или Debian
В качестве примера для этой статьи мы используем Ubuntu 14.04 LTS.

- Шаг 1. Установка MySQL Server 5.6.

    Установите mysql-server 5.6, выполнив команду apt-get.

              # azureuser@mysqlnode1:~$ sudo apt-get update
              # azureuser@mysqlnode1:~$ sudo apt-get -y install mysql-server-5.6

    Во время установки вы увидите всплывающее диалоговое окно с запросом задать пароль привилегированного пользователя MySQL. Вам понадобится указать новый пароль привилегированного пользователя MySQL. Ниже приведен снимок экрана.

 ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p10.png)

    Confirm the password again when it is asked.

 ![изображение](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p11.png)
 
- Шаг 2. Вход на MySQL Server.

    После завершения установки MySQL Server служба MySQL запускается автоматически. Вы можете войти на MySQL Server как привилегированный пользователь. Чтобы войти на MySQL Server, используйте следующую команду. При выполнении будет запрошен пароль привилегированного пользователя MySQL, который вы задали при установке MySQL Server.

             # azureuser@mysqlnode1:~$ mysql -uroot -p

- Шаг 3. Проверка службы MySQL в виртуальной машине.
    
    После входа в систему убедитесь, что служба MySQL запущена. Чтобы запустить или перезапустить службу, вы можете воспользоваться следующими командами.

    a\) Чтобы получить информацию о состоянии службы MySQL:

             #sudo service mysql status

    б\) Чтобы запустить службу MySQL:

             #sudo service mysql start

    в\) Чтобы остановить службу MySQL:

             #sudo service mysql stop

    г\) Чтобы перезапустить службу MySQL:

             #sudo service mysql restart


###Как установить MySQL в ОС семейства Redhat или Oracle Linux
- Шаг 1. Добавление репозитория Yum MySQL. Чтобы получить разрешения привилегированного пользователя, выполните команду: 

            #sudo su -
            #[root@azureuser ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm 
            #[root@azureuser ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm 

- Шаг 2. Выбор серии выпуска.
 
            #[root@azureuser ~]# vim /etc/yum.repos.d/mysql-community.repo

    Ниже представлена типичная запись для подрепозитория серии выпуска в файле:

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Шаг 3. Установка MySQL с помощью команды yum install mysql:

           #[root@azureuser ~]#yum install mysql-community-server 

    Эта команда устанавливает пакет для MySQL Server, а также другие необходимые пакеты.

- Шаг 4. Проверка состояния выполнения MySQL.

    Состояние MySQL Server можно проверить, выполнив следующую команду:
   
           #[root@azureuser ~]#service mysqld status

    Вы можете проверить, работает ли порт MySQL Server по умолчанию:

           #[root@azureuser ~]#netstat  –tunlp|grep 3306

- Шаг 5. Запуск и остановка MySQL Server.

    Запустите MySQL Server, выполнив следующую команду:

           #[root@azureuser ~]#service mysqld start

    Остановите MySQL Server, выполнив следующую команду:

           #[root@azureuser ~]#service mysqld stop

    Чтобы настроить MySQL для запуска при загрузке системы, выполните следующую команду:

           #[root@azureuser ~]#chkconfig mysqld on


###Как установить MySQL в Suse Linux

- Шаг 1. Установка MySQL Server.

    Чтобы повысить права для разрешений, выполните следующую команду:

           #sudo su -

    Установите MySQL, выполнив следующую команду:

           #mysql-test:~ # zypper update

           #mysql-test:~ # zypper install mysql-server mysql-devel mysql

- Шаг 2. Проверка состояния выполнения MySQL.

    Состояние MySQL Server можно проверить, выполнив следующую команду:

           #mysql-test:~ # rcmysql status

    Вы можете проверить, работает ли порт MySQL Server по умолчанию:

           #mysql-test:~ # netstat  –tunlp|grep 3306

- Шаг 3. Запуск и остановка MySQL Server.

    Запустите MySQL Server, выполнив следующую команду:

           #mysql-test:~ # rcmysql start

    Остановите MySQL Server, выполнив следующую команду:

           #mysql-test:~ # rcmysql stop

    Чтобы настроить MySQL для запуска при загрузке системы, выполните следующую команду:

           #mysql-test:~ # insserv mysql
<!--HONumber=52-->

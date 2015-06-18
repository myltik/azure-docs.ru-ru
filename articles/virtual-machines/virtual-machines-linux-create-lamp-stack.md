<properties
	pageTitle="Как создать стек LAMP с Microsoft Azure"
	description="Узнайте, как создать стек LAMP в виртуальной машине под управлением Linux в Microsoft Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="2/10/2015"
	ms.author="ningk"/>

#Как создать стек LAMP с Microsoft Azure

Стек "LAMP" - это группа программ с открытым исходным кодом, которые обычно устанавливаются вместе для активации размещения на сервере динамических веб-сайтов и веб-приложений. Этот термин фактически является аббревиатурой, представляющей операционную систему Linux с веб-сервером Apache. Данные сайта хранятся в базе данных MySQL, а динамическое содержимое обрабатывается с помощью PHP.  

В этом руководстве показано, как получить стек LAMP, установленный в образе Linux, и развернуть его в службе Microsoft Azure.  

Вы узнаете:  

-	как создать виртуальную машину Azure;
-	как подготовить виртуальную машину для стека LAMP;
-	как установить программное обеспечение, необходимое для сервера LAMP на виртуальной машине.

Предполагается, что читатель уже имеет подписку Azure.  В противном случае зарегистрируйтесь и получите бесплатную пробную версию по адресу [http://azure.microsoft.com](http://azure.microsoft.com). Если у вас есть подписка MSDN, см. статью [Специальные предложения Microsoft Azure: преимущества для подписчиков MSDN, MPN и Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Дополнительную информацию о службе Azure см. в статье [Что такое Azure?](http://azure.microsoft.com/overview/what-is-azure/)

Если у вас уже есть виртуальная машина и вы просто хотите найти общие сведения об установке стека LAMP на различных дистрибутивах Linux, в дополнение к указанному разделу см. раздел [Установка стека LAMP на виртуальную машину Linux в Azure](../virtual-machines-linux-install-lamp-stack/).

Вы также можете развернуть предварительно настроенные образы LAMP из Azure Marketplace. В следующем 10-минутном видеоролике показано развертывание предварительно созданных образов LAMP из Azure Marketplace.  

> [AZURE.VIDEO lamp-stack-on-azure-vms-with-guy-bowerman]

##Этап 1. Создание образа
На этом этапе вы создадите виртуальную машину с помощью образа Linux в Azure.  

###Шаг 1. Создание ключа проверки подлинности SSH
SSH - это важный инструмент для системных администраторов. Однако полагаться на созданный человеком пароль не всегда целесообразно из соображений безопасности. Надежный ключ SSH позволяет использовать удаленный доступ, не беспокоясь о паролях. Этот метод состоит в проверке подлинности с асимметричным шифрованием. Пользователь проходит проверку подлинности с помощью своего закрытого ключа. Вы можете даже заблокировать учетную запись пользователя, чтобы полностью запретить проверку пароля.

Выполните следующие действия, чтобы создать ключ проверки подлинности SSH.

-	Загрузите и установите puttygen из следующего расположения: [http://www.chiark.greenend.org.uk/~sgtatham/](http://www.chiark.greenend.org.uk/~sgtatham/)putty/download.html
-	Запустите puttygen.exe.
-	Нажмите кнопку **Создать**, чтобы создать ключи. В процессе можно увеличить степень случайности, переместив указатель мыши на пустую область окна.  
![][1]
-	После завершения процесса создания полученный ключ отобразится в средстве Puttygen.exe. Например:  
![][2]
-	Выберите и скопируйте открытый ключ в область **Ключ** и сохраните его в файле с именем **publicKey.pem**. Не щелкайте **Сохранить открытый ключ**, так как формат файла сохраненного открытого ключа отличается от необходимого открытого ключа.
-	Щелкните **Сохранить закрытый ключ** и сохраните его в файл с именем **privateKey.ppk**.

###Шаг 2. Создание образа на портале предварительной версии Azure
На [портале предварительной версии Azure](https://portal.azure.com/) нажмите кнопку **Создать** в панели задач и создайте образ, придерживаясь следующих инструкций и выбрав образ Linux согласно своим потребностям. В этом примере используется образ Ubuntu 14.04.

![][3]

В поле **Имя узла** укажите имя URL-адреса, с помощью которого интернет-клиенты будут получать доступ к этой виртуальной машине. Определите последнюю часть DNS-имени, например LAMPDemo, после чего в службе Azure будет создан URL-адрес Lampdemo.cloudapp.net.

В поле **Имя пользователя** выберите имя, которое впоследствии будет использоваться для входа на виртуальную машину.

В поле **Ключ проверки подлинности SSH** скопируйте значение ключа из файла **publicKey.pem**, который содержит открытый ключ, созданный с помощью средства puttygen.  

![][4]

При необходимости настройте другие параметры и нажмите кнопку **Создать**.

##Этап 2. Подготовка виртуальной машины для стека LAMP
На этом этапе вы настроите конечную точку для веб-трафика и подключитесь к новой виртуальной машине.

###Шаг 1. Открытие HTTP-порта для разрешения веб-доступа
Конечные точки в Azure состоят из протокола (TCP или UDP), а также общего и частного порта. Частный порт - это порт, который прослушивает служба на виртуальной машине. Общий порт - это порт, который облачная служба Azure прослушивает извне на наличие интернет-трафика. В некоторых случаях это тот же номер порта.  

TCP-порт 80 - номер порта по умолчанию, который прослушивает Apache. Открытие этого порта с помощью конечной точки Azure обеспечивает вам и другим интернет-клиентам доступ к веб-серверу Apache.  

На портале предварительной версии Azure щелкните **Обзор -> Виртуальная машина** и выберите созданную виртуальную машину.

![][5]

Чтобы добавить к виртуальной машине конечную точку, щелкните поле **Конечные точки**.

![][6]

Щелкните **Добавить**. При подготовке новой виртуальной машины при необходимости можно включить или отключить конечные точки.

Настройка конечной точки  

1.	Введите имя конечной точки в поле **Конечная точка**.
2.	Введите 80 в поле **Общий порт**. Если изменить значение по умолчанию для прослушивающего порта Apache, следует обновить частный порт, чтобы он совпадал с прослушивающим портом Apache.
3.	Введите 80 в поле **Общий порт**. По умолчанию при трафике HTTP используется порт 80.
Если задано значение 80, нет необходимости включать номер порта в URL-адрес, по которому вы получаете доступ к веб-службе Apache (например, http://lampdemo.cloudapp.net).
Если прослушивающему порту Apache присвоено другое значение, например 81, необходимо добавить номер порта в URL-адрес для доступа к веб-службе Apache (например, http://lampdemo.cloudapp.net:81/).

![][7]

Нажмите кнопку **ОК**, чтобы добавить конечную точку к виртуальной машине.




###Шаг 2. Подключение к созданному образу
Вы можете выбрать любое средство SSH для подключения к новой виртуальной машине. В этом примере мы используем средство Putty.  

Сначала получите DNS-имя виртуальной машины на портале предварительной версии Azure. Щелкните **Обзор -> Виртуальная машина -> ** имя виртуальной машины **-> Свойства**, а затем просмотрите значение в поле **Доменное имя** поле плитки **Свойства**.

Получите номер порта для соединений по протоколу SSH в поле **SSH**.   Ниже приведен пример.  

![][8]

Скачайте средство Putty [здесь](http://www.putty.org/) .  

После загрузки запустите исполняемый файл PUTTY.EXE. Настройте основные параметры, указав имя узла и номер порта, полученные в свойствах виртуальной машины. Пример:

![][9]

В левой области щелкните **Подключение -> SSH -> Проверка подлинности** и нажмите кнопку **Обзор**, чтобы указать расположение файла **privateKey.ppk**, который содержит закрытый ключ, созданный с помощью средства puttygen на этапе 1. Создайте образ. Пример:  

![][10]

Щелкните **Открыть**. Может отобразиться оповещение в окне сообщения. Если вы настроили DNS-имя и номер порта правильно, нажмите кнопку **Да**.

![][11]


Вы увидите следующее.

![][12]

Введите имя пользователя, указанное при создании виртуальной машины на этапе 1. Создайте образ. Должны отобразиться следующие данные.  

![][13]

##Этап 3. Установка стека LAMP

Стек LAMP можно установить по-разному в зависимости от того, какой дистрибутив Linux использовался для создания виртуальной машины. В следующих разделах содержатся типичные шаги для некоторых распространенных операционных систем Linux.

###Red Hat, база CentOS

####Установка Apache
Чтобы установить Apache, откройте терминал и выполните следующую команду:  

	sudo yum install httpd

После установки запустите Apache с помощью следующей команды:  

	sudo service httpd start
####Тестирование Apache
Чтобы проверить, успешно ли установлено приложение Apache, перейдите к DNS-имени сервера Apache (например, по используемому в этой статье URL-адресу http://lampdemo.cloudapp.net/). На странице должны отобразиться слова "It works!" (Сработало!).
![][14]

####Устранение неполадок
Если веб-служба Apache работает, но вы не видите приведенную выше страницу Apache по умолчанию, необходимо проверить следующие компоненты.  

-	Адрес и порт прослушивания веб-службы Apache.
	-	Проверьте параметры конечной точки для виртуальной машины Azure. Убедитесь, что конечная точка настроена правильно. См. инструкции, приведенные в этапе 1 "Создание образа" в этой статье.
	-	Откройте /etc/httpd/conf/httpd.conf и выполните поиск строки "Listen". Убедитесь, что прослушивающий порт Apache совпадает с частным портом, настроенным для конечной точки. По умолчанию для Apache используется порт 80. Ниже приведен пример.  

			......
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			......
			......  

-	Брандмауэр, конфигурация iptables  
Если вы видите страницу Apache по умолчанию из локального узла, возможно, проблема состоит в том, что порт, используемый службой Apache для прослушивания, блокируется брандмауэром. Для просмотра веб-страницы Apache можно использовать средство w3m. Следующие команды позволяют установить средство w3m и перейти на страницу Apache по умолчанию.  

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	Если проблема вызвана брандмауэром или конфигурацией iptables, добавьте следующие строки в /etc/sysconfig/iptables:  

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	Обратите внимание, что вторая строка требуется только для трафика https.  

	Убедитесь, что эти строки расположены над всеми строками, которые глобально ограничивают доступ, например следующими:  

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	Чтобы новые параметры вступили в силу, выполните следующую команду:  

		service iptables restart

####Установка MySQL
Чтобы установить MySQL, откройте терминал и выполните следующие команды:  

	sudo yum install mysql-server
	sudo service mysqld start

В процессе установки MySQL дважды запросит разрешение. MySQL будет установлен после подтверждения обоих запросов.

####Настройка MySQL
После завершения установки можно задать пароль пользователя root MySQL, выполнив следующую команду:  

	sudo /usr/bin/mysql_secure_installation  

Будет запрошен текущий пароль пользователя root.

Так как вы только что установили MySQL, скорее всего, у вас его не будет, поэтому оставьте это поле пустым, нажав клавишу ВВОД.  

	Enter current password for root (enter for none):
	OK, successfully used password, moving on...  

Вам будет предложено установить пароль пользователя root. Затем выберите пункт Y и следуйте инструкциям.  

CentOS автоматизирует процесс настройки MySQL, задавая ряд вопросов, на которые вы должны ответить "Да" или "Нет". Эти вопросы приведены ниже. Выберите Y или N, чтобы внести необходимые настройки. В конце MySQL перезагрузится и внесет новые изменения.  

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...

	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!  

####Установка PHP
PHP - язык веб-сценариев с открытым исходным кодом, который широко используется для создания динамических веб-страниц.  

Чтобы установить PHP на виртуальной машине, откройте терминал и выполните следующую команду:  

	sudo yum install php php-mysql  

Ответьте "y", чтобы загрузить пакеты программного обеспечения. Затем ответьте "y" на запрос импорта ключа GPG 0xE8562897 "CentOS-5 Key" (официального ключ подписывания CentOS 5). Будут установлены средства PHP.

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Debian, база Ubuntu
Протестировано в ОС Ubuntu 14.04.  

В основе Ubuntu лежит Debian. Стек LAMP можно установить таким же образом, как и серию Red Hat. Чтобы упростить задачу, используйте средство Tasksel.

Tasksel - это средство Debian и Ubuntu, которое устанавливает несколько связанных пакетов в рамках одной скоординированной задачи в системе. Дополнительные сведения см. на сайте [Tasksel - вики-сайт справки сообщества](https://help.ubuntu.com/community/Tasksel).

Средство Tasksel используется для установки необходимого программного обеспечения для стека LAMP.

- Чтобы загрузить списки пакетов из репозиториев и обновить их для получения сведений о последних версиях пакетов и их зависимостей, выполните следующие действия.  

		sudo apt-get update
-	Чтобы установить стек LAMP для Ubuntu, используя средство Tasksel, выполните следующие действия.  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

Затем выполните инструкции мастера и выберите **свой пароль пользователя root в MySQL**.

![][15]


##Тестирование LAMP на сервере
Вы можете проверить систему LAMP, создав страницу кратких сведений о php.

Сначала создайте файл.  

	sudo nano /var/www/html/info.php  

Добавьте следующую строку:  

	<?php
	phpinfo();
	?>  

Сохраните данные и закройте программу.  

Перезапустите Apache, чтобы все изменения вступили в силу на машине. Если на виртуальной машине установлена ОС CentOS, выполните следующую команду для перезапуска Apache:  

	sudo service httpd restart

Если на виртуальной машине установлена ОС Ubuntu, выполните следующую команду для перезапуска Apache:  

	sudo service apache2 restart  

Завершите задачу, перейдя на страницу сведений о php (например, для веб-сервера в этом разделе используется URL-адрес http://lampdemo.cloudapp.net/info.php).  

Браузер должен иметь примерно такой вид:

![][16]

##Дополнительные действия

В общем случае рекомендуется изменить некоторые параметры по умолчанию, чтобы подготовиться к развертыванию веб-приложения.  

###Разрешение удаленного доступа к MySQL
Если у вас установлено несколько виртуальных машин с MySQL, и им нужно обмениваться данными, необходимо включить удаленный доступ MySQL и предоставить соответствующие разрешения.  

**Формат справочника по командам:**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**Пример.**  

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

Вам также следует изменить профиль /etc/mysql/my.cnf. При наличии похожих строк:  

	skip-networking
	bind-address = 127.0.0.1  

К ним следует добавить комментарии (добавьте # в начало строки), а затем перезапустить MySQL.  

Сведения о том, как добавить конечную точку и разрешить тем самым удаленный доступ, см. в инструкциях на этапе 1 "Создание образа для создания новой конечной точки". По умолчанию в MySQL для удаленного доступа использует TCP-порт с номером 3306. Пример:

![][17]

###Развертывание веб-приложений на сервере Apache
После успешной настройки стека LAMP можно развернуть существующее веб-приложение на веб-сервере Apache (вашей виртуальной машине). Необходимо выполнить те же действия, что и при развертывании существующего веб-приложения на физическом веб-сервере.

-	Корень веб-сервера находится в папке **/var/www/html**. Предоставьте соответствующие права пользователям, которым необходимо передавать файлы в эту папку. В следующем примере показано, как добавить разрешение на запись в группу с именем lampappgroup и включить в эту группу имя пользователя azureuser.  

		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup

	>[AZURE.NOTE] Возможно, вам снова потребуется войти в систему, чтобы изменить файл в папке /var/www/html/.
-	Используйте любой SFTP-клиент (например, FileZilla) для подключения к DNS-имени виртуальной машины (например, lampdemo.cloudapp.net), а затем перейдите в папку /**www/var/html**, чтобы опубликовать свой сайт.  
![][18]



##Распространенные проблемы и устранение неполадок

###Не удается получить доступ к виртуальной машине с Apache и Moodle из Интернета

-	**Симптом**  
Служба Apache выполняется, но в браузере не отображается страница Apache по умолчанию.
-	**Возможная основная причина**
	1.	Прослушивающий порт Apache отличается от частного порта конечной точки виртуальной машины для веб-трафика.</br>
	Проверьте параметры конечных точек общего и частного портов и убедитесь, что частный порт совпадает с прослушивающим портом Apache. Чтобы получить инструкции по настройке конечных точек для своей виртуальной машины, см. этап 1 "Создание образа".</br>
	Чтобы определить прослушивающий порт Apache, откройте /etc/httpd/conf/httpd.conf (выпуск Red Hat) или /etc/apache2/ports.conf (выпуск Debian) и найдите строку "Listen". Порт по умолчанию - 80.

	2.	Брандмауэр отключил прослушивающий порт Apache.</br>  
	Если страница по умолчанию Apache отображается на локальном узле, скорее всего, порт, который прослушивает служба Apache, блокируется брандмауэром. Для просмотра веб-страницы можно использовать средство w3m. Следующие команды позволяют установить средство w3m и перейти на страницу Apache по умолчанию.  

			sudo yum  install w3m w3m-img
			w3m http://localhost

-	**Решение**

	1.	Если прослушивающий порт Apache отличается от частного порта конечной точки для веб-трафика на виртуальной машине, необходимо изменить частный порт конечной точки, чтобы он совпал с прослушивающим портом Apache.
	2.	Если проблема вызвана брандмауэром или iptables, добавьте следующие строки в /etc/sysconfig/iptables:  

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

		Обратите внимание, что вторая строка требуется только для трафика https.

		Убедитесь, что эти строки расположены над всеми строками, которые глобально ограничивают доступ, например следующими:  

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Чтобы перезагрузить iptables, выполните следующую команду:  

			service iptables restart  

		Протестировано в ОС CentOS 6.3.

###Во время передачи файлов проекта в папку /var/www/html/ вам отказано в доступе.  

-	**Симптом**  
Если вы используете любой SFTP-клиент (такой как FileZilla) для подключения к виртуальной машине и переходите в папку /var/www/html для публикации своего сайта, отображается сообщение об ошибке, похожее на следующее.  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**Возможная основная причина**
У вас нет разрешения на доступ к папке /var/www/html.  
-	**Решение**  
Необходимо получить разрешение из учетной записи root. Можно изменить владельца этой папки из корневой папки на имя пользователя, которое использовалось при подготовке машины. Ниже приведен пример с именем учетной записи azureuser:  

		sudo chown azureuser -R /var/www/html  

	Кроме того, используйте параметр -R, чтобы применить разрешения для всех файлов в каталоге.  

	Обратите внимание, что эта команда также работает для каталогов. Параметр -R изменяет разрешения для всех файлов и каталогов в каталоге. Пример:  

		sudo chown -R username:group directory  

	Эта команда изменяет владельца (как пользователя, так и группу) для всех файлов и каталогов в каталоге и самого каталога.  

	Только следующая команда изменяет разрешение для папки каталога, но оставляет файлы и папки внутри самого каталога.  

		sudo chown username:group directory  

###Не удалось надежно определить полное доменное имя сервера

-	**Симптом**  
При перезапуске сервера Apache с помощью одной из следующих команд:  

		sudo /etc/init.d/apache2 restart  # Debian release  

	или

		sudo /etc/init.d/httpd restart   # Red Hat release  

	Появится следующая ошибка:  

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**Возможная основная причина**
	Вы не задали имя сервера Apache.

-	**Решение**  
	Вставьте строку "ServerName localhost" в файл httpd.conf (выпуск Red Hat) или apache2.conf (выпуск Debian) в /etc/apache2 и перезапустите Apache. Уведомление исчезнет.



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg

<!--HONumber=45--> 
 
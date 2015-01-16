<properties urlDisplayName="Web w/ MySQL + FTP" pageTitle="Веб-сайт на PHP с использованием MySQL и FTP - учебник Azure" metaKeywords="" description="В этом учебнике показано, как создать веб-сайт на PHP, который хранит данные в MySQL и использует развертывание FTP в Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />


#Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью FTP

В этом учебнике показано, как создать веб-сайт PHP-MySQL Azure и развернуть его с помощью FTP. В этом учебнике предполагается, что на компьютере установлены [PHP][install-php], [MySQL][install-mysql], веб-сервер и FTP-клиент. Указания, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. После завершения работы с этим учебником в Azure будет работать веб-сайт PHP/MySQL.
 
Вы узнаете:

* Как создать веб-сайт Azure и базу данных MySQL с помощью портала управления Azure. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как опубликовать приложение в Azure с использованием FTP.
 
Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже:

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Создание веб-сайта Azure и настройка публикации на FTP

Для создания веб-сайта Azure и базы данных MySQL выполните следующие действия.

1. Войдите на [портал управления Azure][management-portal].
2. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Create New Azure Web Site][new-website]

3. Щелкните **Веб-сайт**, а затем **Настраиваемое создание**.

	![Custom Create a new Web Site][custom-create]
	
	Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных MySQL** в раскрывающемся списке **БАЗА ДАННЫХ** и выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Щелкните стрелку в нижней части диалогового окна.

	![Fill in Web Site details][website-details]

4. Введите **ИМЯ** своей базы данных, выберите центр обработки данных для ее размещения в раскрывающемся списке **РЕГИОН** и установите флажок, свидетельствующий о вашем согласии с юридическими условиями и положениями. Щелкните флажок в нижней части диалогового окна.

	![Create new MySQL database][new-mysql-db]

	После создания веб-сайта вы увидите текст **Веб-сайт [ИМЯ_САЙТА] успешно создан**. Теперь можно включить публикацию FTP.

5. В списке веб-сайтов щелкните по имени веб-сайта, чтобы открыть страницу панели управления **Быстрый запуск** для этого веб-сайта.

	![Open web site dashboard][go-to-dashboard]


6. В нижней части страницы **Быстрый запуск** щелкните **Сбросить учетные данные развертывания**. 

	![Reset deployment credentials][reset-deployment-credentials]

7. Чтобы включить публикацию в FTP, необходимо указать имя пользователя и пароль. Запишите создаваемые имя пользователя и пароль.

	![Create publishing credentials][portal-git-username-password]

##Построение и тестирование приложения на локальном ресурсе

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в базе данных MySQL. Приложение состоит из двух файлов:

* **index.php**: отображает форму для регистрации и таблицу, содержащую сведения о зарегистрировавшихся пользователях.
* **createtable.php**: создает таблицу MySQL для приложения. Этот файл будет использоваться только один раз.

Чтобы построить и запустить приложение локально, выполните следующие действия. Предполагается, что на локальном компьютере установлены PHP, MySQL и веб-сервера, а также включено [расширение PDO для MySQL][pdo-mysql].

1. Создайте базу данных MySQL с названием registration. Это можно сделать в командной строке MySQL с помощью следующей команды:

		mysql> create database registration;

2. В корневом каталоге веб-сервера создайте папку с именем registration, а в ней два файла один с именем createtable.php, а другой с именем index.php.

3. Откройте файл createtable.php в текстовом редакторе или интегрированной среде разработки и добавьте приведенный ниже код. Этот код будет использоваться для создания таблицы registration_tbl в базе данных registration.

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
						PRIMARY KEY(id),
						name VARCHAR(30),
						email VARCHAR(30),
						date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	> [WACOM.NOTE] 
	 Потребуется обновить значения для <code>$user</code> и <code>$pwd</code> с использованием имени пользователя и пароля локальной базы данных MySQL.

4. Откройте веб-браузер и перейдите по адресу [http://localhost/registration/createtable.php][localhost-createtable]. В результате в базе данных будет создана таблица registration_tbl.

5. Откройте файл **index.php** в текстовом редакторе или в интегрированной среде разработки и добавьте базовый HTML-код и CSS для страницы (PHP-код будет добавлен позднее).

		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. В тегах PHP add PHP code for connecting to the database.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> > Потребуется снова обновить значения для <code>$user</code> и <code>$pwd</code> с использованием имени пользователя и пароля локальной базы данных MySQL.

7. После кода подключения к базе данных добавьте код для вставки регистрационных данных в базу данных.

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. Наконец, после вышеприведенного кода добавьте код для извлечения данных из базы данных.

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

Теперь можно перейти по адресу [http://localhost/registration/index.php][localhost-index] для тестирования приложения.

##Получение сведений о подключении к MySQL и FTP

Чтобы подключиться к базе данных SQL, которая работает на веб-сайтах Azure, необходимо знать сведения о подключении. Чтобы получить сведения о подключении к MySQL, выполните следующие действия.

1. На панели мониторинга веб-сайта перейдите по ссылке **Просмотреть строки подключения** в правой части страницы.

	![Get database connection information][connection-string-info]
	
2. Запишите значения "База данных", "Источник данных", "Идентификатор пользователя" и "Пароль".

3. На панели мониторинга веб-сайта перейдите по ссылке **Загрузить профиль публикации** в правом нижнем углу страницы.

	![Download publish profile][download-publish-profile]

4. Откройте файл .publishsettings в редакторе XML. 

3. Найдите элемент <publishProfile> с publishMethod="FTP" следующего вида:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Запишите значения атрибутов publishUrl, userName и userPWD.

##Публикация приложения

После локального тестирования приложения его можно опубликовать на веб-сайте Azure с использованием FTP. Тем не менее, необходимо сначала обновить в приложении сведения подключения к базе данных. При помощи полученной ранее информации о подключении к базе данных (см. раздел **Получение сведений о подключении к базе данных SQL**) обновите следующие данные в **обоих** файлах, createdatabase.php и index.php, соответствующими значениями:

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

Теперь все готово для публикации приложения с помощью протокола FTP.

1. Откройте выбранный клиент FTP.

2. Введите в FTP-клиент имя хоста из атрибута publishUrl, записанное ранее.

3. Введите в FTP-клиент атрибуты userName и userPWD, записанные ранее.

4. Установите подключение.

После подключения можно будет отправлять и загружать файлы. Убедитесь, что файлы передаются в корневой каталог, т. е. в каталог /site/wwwroot.

После передачи файлов index.php и createtable.php перейдите по адресу **http://[имя сайта].azurewebsites.net/createtable.php**, чтобы создать таблицу MySQL для приложения, после чего перейдите по адресу **http://[site name].azurewebsites.net/index.php**, чтобы начать пользоваться приложением.
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png

<!--HONumber=35.1-->

<properties urlDisplayName="Web w/ SQL + Git" pageTitle="Веб-сайт на PHP с базой данных SQL и развертыванием с помощью Git - учебник Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#Создание веб-сайта PHP с базой данных SQL и развертывание с помощью Git

В этом учебном курсе рассматривается создание веб-сайта Azure на PHP с базой данных Azure SQL и ее развертывание с помощью Git. В этом учебнике подразумевается, что на вашем компьютере уже установлен [PHP][install-php], [SQL Server Express][install-SQLExpress], [драйвера Майкрософт для SQL Server для PHP][install-drivers], веб-сервер и [Git][install-git]. После завершения работы с этим учебным курсом у вас будет веб-сайт PHP-SQL в Windows Azure.

> [WACOM.NOTE]
> Для установки и настройки PHP, SQL Server Express, драйверов Майкрософт для SQL Server для PHP, а также служб IIS можно воспользоваться <a href="http://www.microsoft.com/web/downloads/platform.aspx"> Установщиком веб-платформы Майкрософт.

Вы узнаете:

* Как создать веб-сайт Azure и базу данных SQL с помощью портала управления Azure. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как опубликовать и повторно опубликовать свое приложение в Azure с помощью Git.
 
Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже:

![Azure PHP Web Site][running-app]

> [WACOM.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Вы можете <a href="http://azure.microsoft.com/ru-ru/pricing/member-offers/msdn-benefits-details/">активировать преимущества подписки MSDN</a> или <a href="http://azure.microsoft.com/ru-ru/pricing/free-trial/">подписаться на бесплатный пробный период использования</a>.
> 
> Если вы хотите ознакомиться с веб-сайтами Azure до создания учетной записи, перейдите по адресу <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, где вы можете быстро создать краткосрочный начальный сайт ASP.NET на веб-сайтах Azure бесплатно. Не требуется вводить данные кредитной карты или брать на себя какие-либо обязательства.

##Создание веб-сайта Azure и настройка публикации в Git

Для создания веб-сайта Azure и базы данных SQL выполните следующие действия.

1. Войдите на [портал управления Azure][management-portal].
2. Щелкните значок **Создать** в нижнем левом углу портала.
![Create New Azure Web Site][new-website]

3. Щелкните **Веб-сайт**, а затем выберите **Настраиваемое создание**.

	![Custom Create a new Web Site][custom-create]

	Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных SQL** из раскрывающегося списка **База данных**, и щелкните **Публикация из системы управления версиями**. Щелкните стрелку в нижней части диалогового окна.

	![Fill in web site details][website-details-sqlazure]

4. Введите имя базы данных в поле **Имя**, выберите **Создать новый сервер базы данных SQL**, укажите учетные данные и выберите регион. Щелкните стрелку в нижней части диалогового окна.

	![Fill in SQL Database settings][database-settings]

5. Выберите **Локальный репозиторий Git** для исходного кода.

	![where is your source code][where-is-code]

	Если репозиторий Git не был настроен предварительно, необходимо указать имя пользователя и пароль.

6. После создания сайта откройте его панель мониторинга и выберите **Просмотреть развертывания**.

	![Web site dashboard][go-to-dashboard]

9. Вы увидите инструкции для передачи ваших файлов приложений в репозиторий. Запишите эти инструкции - они потребуются позже.

	![Git instructions][git-instructions]

##Получение сведений о подключении к базе данных SQL

Чтобы подключиться к экземпляру базы данных SQL, которая работает на веб-сайтах Azure, необходимо знать сведения о подключении. Чтобы получить сведения о подключении к базе данных SQL, выполните следующие действия:

1. На портале управления Azure щелкните **Связанные ресурсы**, а затем щелкните имя нужной базы данных.

	![Linked Resources][linked-resources]

2. Щелкните **Просмотреть строки подключения**.

	![Connection string][connection-string]
	
3. В разделе **PHP** открывшегося диалогового окна запишите значения UID, PWD, Database и $serverName.

##Построение и тестирование приложения на локальном ресурсе

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в экземпляре базы данных SQL. Приложение состоит из двух файлов (код для копирования и вставки приведен ниже):

* **index.php**: отображает форму для регистрации и таблицу, содержащую сведения о зарегистрировавшихся пользователях.
* **createtable.php**: создает таблицу SQL для приложения. Этот файл будет использоваться только один раз.

Чтобы запустить приложение локально, выполните приведенные ниже действия. Обратите внимание, что предполагается наличие на локальном компьютере PHP, SQL Server Express и веб-сервера, а также включение [расширения PDO для SQL Server][pdo-sqlsrv].

1. Создайте базу данных SQL Server под названием registration. Это можно сделать в командной строке sqlcmd с помощью следующих команд:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. В корневом каталоге веб-сервера создайте папку с именем registration, а в ней два файла один с именем createtable.php, а другой с именем index.php.

3. Откройте файл createtable.php в текстовом редакторе или интегрированной среде разработки и добавьте приведенный ниже код. Этот код будет использоваться для создания таблицы registration_tbl в базе данных registration.

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
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

	Вам понадобится обновить значения <code>$user</code> и <code>$pwd</code> именем пользователя и паролем вашего локального SQL Server.

4. Откройте веб-браузер и перейдите по адресу **http://localhost/registration/createtable.php**. В результате в базе данных будет создана таблица registration_tbl.

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

6. Внутри тегов PHP добавьте PHP-код для подключения к базе данных.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    Вам понадобится снова обновить значения <code>$user</code> и <code>$pwd</code> именем пользователя и паролем своей локальной базы данных MySQL.

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

Теперь можно перейти по адресу **http://localhost/registration/index.php** для тестирования приложения.

##Публикация приложения

После локального тестирования приложения его можно опубликовать на веб-сайте Azure с использованием Git. Тем не менее, необходимо сначала обновить в приложении сведения подключения к базе данных. При помощи полученной ранее информации о подключении к базе данных (см. раздел **Получение сведений о подключении к базе данных SQL**) обновите следующие данные в **обоих** файлах, createdatabase.php и index.php, соответствующими значениями:

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> В диалоговом окне <code>$host</code>значение SERVER должно быть должно быть указано с префиксом <code>tcp:</code>, а значение <code>$user</code> должно быть указано как USERNAME, "@" и идентификатор вашего сервера. ИД сервера - первые 10 символов значения параметра SERVER.


Теперь все готово для настройки публикации Git и публикации приложения.

> [WACOM.NOTE]
> Для этого используются те же действия, которые были указаны в конце вышеприведенного раздела "Создание веб-сайта Azure и настройка публикации в Git".


1. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

2. Перейдите по адресу **http://[имя веб-сайта].azurewebsites.net/createtable.php**, чтобы создать таблицу MySQL для приложения.
3. Перейдите по адресу **http://[имя веб-сайта].azurewebsites.net/index.php**, чтобы начать работу с приложением.

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git. 

##Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1. Внесите изменения в приложение локально.
2. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

3. Перейдите по адресу **http://[имя веб-сайта].azurewebsites.net/index.php**, чтобы просмотреть внесенные изменения.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/ru-ru/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/createphpgitsite.png
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/setupdb.png
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/viewdeploy.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/gitsettings.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/setupgit.png

<!--HONumber=35_1-->

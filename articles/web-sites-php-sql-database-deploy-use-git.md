<properties 
	pageTitle="Создание веб-приложения PHP-SQL и развертывание в службе приложений App с помощью Git" 
	description="В этом учебнике показано, как создать веб-приложение на PHP, которое хранит данные в базе данных SQL, и как использовать развертывание Git в службе приложений Azure." 
	services="app-service\web, sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tomfitz"/>

# Создание веб-приложения PHP-SQL и развертывание в службе приложений App с помощью Git

В этом учебном курсе рассматривается создание веб-приложения PHP в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714), которое подключается к базе данных Azure SQL, и его развертывание с помощью Git. В этом учебнике подразумевается, что на вашем компьютере уже установлены [PHP][install-php], [SQL Server Express][install-SQLExpress], [драйверы Microsoft для SQL Server для PHP][install-drivers], веб-сервер и [Git][install-git]. После завершения работы с этим учебником у вас будет работающее в Azure веб-приложение PHP-SQL.

> [AZURE.NOTE]
> Для установки и настройки PHP, SQL Server Express, драйверов Майкрософт для SQL Server для PHP, а также служб IIS воспользуйтесь [Установщиком веб-платформы Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).

Вы узнаете:

* Как создать веб-приложение Azure и базу данных SQL с помощью [портала Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Поскольку язык PHP включен в веб-приложениях службы приложений по умолчанию, никаких дополнительных действий для выполнения кода PHP не требуется.
* Как опубликовать и повторно опубликовать свое приложение в Azure с помощью Git.
 
Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже:

![Azure PHP Web Site](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure еще до создания учетной записи Azure, перейдите на страницу [ознакомительной версии службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там вы сможете сразу создать свое первое веб-приложение, правда срок его службы будет ограничен. Никаких кредитных карт и обязательств.

##Создание веб-приложения Azure и настройка публикации в Git

Для создания веб-приложения Azure и базы данных SQL выполните следующие действия.

1. Войдите на [портал Azure][management-portal].

2. Откройте Azure Marketplace. Для этого щелкните значок **Marketplace** или в левом нижнем углу панели мониторинга щелкните **Создать**, выберите **Интернет и мобильные устройства**, а затем внизу окна - **Azure Marketplace**.
	
3. В окне Marketplace выберите **Веб-приложения**.

4. Щелкните значок**Веб-приложение + SQL**.

5. Прочитав описание "Веб-приложения + SQL", нажмите **Создать**.

6. Поочередно выберите вкладки (**Группа ресурсов**, **Веб-приложение**, **База данных** и **Подписка**) и на каждой из них заполните обязательные поля:
	
	- Введите URL-адрес по своему выбору.	
	- Настройте учетные данные для сервера базы данных.
	- Выберите ближайший к вам регион.

	![configure your app](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)

7. Определив параметры веб-приложения, нажмите кнопку **Создать**.

	После создания веб-приложения на кнопке **Уведомления** загорится зеленым слово **УСПЕШНО** и откроется колонка группы ресурсов, в которой будет видно, что и веб-приложение, и база данных SQL находятся в группе.

4. Щелкните пиктограмму веб-приложения в колонке группы ресурсов, чтобы открыть колонку веб-приложения.

	![web app's resource group](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)

5. Щелкните **Настройка непрерывного развертывания** > **Выбор источника**. Выберите **Локальный репозиторий Git** и нажмите **OK**.

	![where is your source code](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)

	Если репозиторий Git не был настроен предварительно, необходимо указать имя пользователя и пароль. Чтобы это сделать, щелкните в колонке веб-приложения **Настройка учетных данных развертывания**.

	![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)

6. Область **Настройка непрерывного развертывания** становится областью **Развертывания не найдены**. Щелкните ее, чтобы увидеть удаленный URL-адрес Git, который понадобится вам для развертывания приложения PHP позднее.

##Получение сведений о подключении к базе данных SQL

Чтобы подключиться к экземпляру базы данных SQL, связанной с вашим веб-приложением, вам понадобятся сведения о подключении, которые вы указали во время создания базы данных. Чтобы получить информацию о подключении к базе данных SQL, выполните следующие действия.

1. Вернитесь в колонку группы ресурсов и щелкните значок базы данных SQL.

2. В колонке базы данных SQL щелкните **Свойства**, затем щелкните **Показать строки подключения базы данных**. 

	![View database properties](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
	
3. В разделе **PHP** открывшегося диалогового окна обратите внимание на значения  `Server`, `SQL Database` и `User Name`. Эти значения понадобятся вам позднее для публикации веб-приложения PHP в службу приложений Azure.

##Построение и тестирование приложения на локальном ресурсе

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в экземпляре базы данных SQL. Приложение состоит из двух файлов (код для копирования и вставки приведен ниже):

* **index.php**: отображает форму для регистрации и таблицу, содержащую сведения о зарегистрировавшихся пользователях.
* **createtable.php**: создает таблицу SQL для приложения. Этот файл будет использоваться только один раз.

Чтобы запустить приложение локально, выполните приведенные ниже действия. Обратите внимание, что предполагается наличие на локальном компьютере PHP, SQL Server Express и веб-сервера, а также включение [расширение PDO для SQL Server][pdo-sqlsrv].

1. Создайте базу данных SQL Server под названием `registration`. Это можно сделать в командной строке `sqlcmd` с помощью следующих команд:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. В корневом каталоге веб-сервера создайте папку с именем `registration`, а в ней два файла: один с именем `createtable.php`, а другой с именем `index.php`.

3. Откройте файл `createtable.php` в текстовом редакторе или интегрированной среде разработки и добавьте приведенный ниже код. Этот код будет использоваться для создания таблицы `registration_tbl` в базе данных `registration`.

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

	Обратите внимание, что в качестве значений для параметров <code>$user</code> и <code>$pwd</code> необходимо ввести имя пользователя и пароль SQL Server.

4. Откройте веб-браузер и перейдите по адресу **http://localhost/registration/createtable.php**. В результате в базе данных будет создана таблица  `registration_tbl`.

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

    Затем в качестве значений для параметров <code>$user</code> и <code>$pwd</code> необходимо будет ввести имя пользователя и пароля локальной базы данных MySQL.

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

После локального тестирования приложения его можно опубликовать в веб-приложениях службы приложений с использованием Git. Тем не менее, необходимо сначала обновить в приложении сведения подключения к базе данных. При помощи полученной ранее информации о подключении к базе данных (см. раздел **Получение информации о подключении к базе данных SQL**) обновите следующую информацию в **обоих** файлах, `createdatabase.php` и  `index.php`, соответствующими значениями:

	// DB connection info
	$host = "tcp:<value of Server>";
	$user = "<value of User Name>";
	$pwd = "<your password>";
	$db = "<value of SQL Database>";

> [AZURE.NOTE]
> В параметре <code>$host</code> к значению сервера должно быть добавлено <code>tcp:</code>.


Теперь все готово для настройки публикации Git и публикации приложения.

> [AZURE.NOTE]
> Для этого используются те же действия, которые были указаны в конце предыдущего раздела **Создание веб-приложения Azure и настройка публикации с помощью Git**.


1. Откройте GitBash (или терминал, если Git указан в переменной `PATH`), измените каталоги на корневой каталог приложения (каталог **регистрации**) и выполните следующие команды:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

2. Перейдите по адресу **http://[имя веб-приложения].azurewebsites.net/createtable.php**, чтобы создать базу данных SQL для приложения.
3. Перейдите по адресу **http://[имя веб-приложения].azurewebsites.net/index.php**, чтобы начать работу с приложением.

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git. 

##Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1. Внесите изменения в приложение локально.
2. Откройте GitBash (или терминал, если Git включен в переменную  `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

3. Перейдите по адресу **http://[имя веб-приложения].azurewebsites.net/index.php**, чтобы просмотреть внесенные изменения.

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Сведения о переходе со старого портала на новый см. в статье [Справочная информация о навигации по предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).




[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv

<!--HONumber=52-->
<properties 
	pageTitle="Создание веб-приложения PHP-MySQL в службе приложений Azure и развертывание с помощью FTP" 
	description="В этом учебнике показано, как создать веб-приложение PHP, которое хранит данные в MySQL и использует развертывание FTP в Azure." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="hero-article" 
	ms.date="01/12/2016" 
	ms.author="robmcm"/>


#Создание веб-приложения PHP-MySQL в службе приложений Azure и развертывание с помощью FTP

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP — Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP — FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

В этом учебнике показано, как создать веб-приложение PHP-MySQL и развернуть его с помощью FTP. В этом учебнике предполагается, что на компьютере установлены [PHP][install-php], [MySQL][install-mysql], веб-сервер и FTP-клиент. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. По завершении работы с этим учебником у вас будет работающее в Azure веб-приложение PHP-MySQL.
 
Вы узнаете:

* Как создать веб-приложение и базу данных MySQL с помощью портала Azure. Так как язык PHP включен в веб-приложениях по умолчанию, никаких дополнительных действий для выполнения вашего PHP-кода не требуется.
* Как опубликовать приложение в Azure с использованием FTP.
 
Руководствуясь этим учебником, вы создадите на языке PHP простое веб-приложение для регистрации. Приложение будет размещаться в веб-приложении. Снимок экрана завершенного приложения приведен ниже:

![Веб-сайт Azure на PHP][running-app]

>[AZURE.NOTE] Чтобы начать работу со службой приложений Azure до регистрации учетной записи Azure, перейдите в раздел [Пробная версия службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где можно сразу создать кратковременное начальное мобильное веб-приложение в службе приложений. Никаких кредитных карт и обязательств.


##Создание веб-приложения и настройка публикации через FTP

Чтобы создать веб-приложение и базу данных MySQL, выполните следующие действия.

1. Войдите на [портал Azure][management-portal].
2. Щелкните значок **+ Создать** в верхнем левом углу портала Azure.

	![Создание нового веб-сайта Azure][new-website]

3. В поле поиска введите **веб-приложение + MySQL** и щелкните элемент **веб-приложение + MySQL**.

	![Настраиваемое создание нового веб-сайта][custom-create]

4. Щелкните **Создать**. Введите уникальное имя службы приложений, допустимое имя группы ресурсов и новый план обслуживания.

    ![Задание имени группы ресурсов][resource-group]


6. Введите значения для своей новой базы данных и примите условия.

	![Создание новой базы данных MySQL][new-mysql-db]
	
7. Когда веб-приложение будет создано, вы увидите колонку новой службы приложений.


6. Щелкните **Параметры** > **Учетные данные развертывания**.

	![Сброс учетных данных развертывания][set-deployment-credentials]

7. Чтобы включить публикацию в FTP, необходимо указать имя пользователя и пароль. Сохраните учетные данные и запишите имя пользователя и пароль, которые вы создали.

	![Создание учетных данных для публикации][portal-ftp-username-password]

##Создание и тестирование приложения локально

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в базе данных MySQL. Приложение состоит из двух файлов:

* **index.php** — вывод формы для регистрации и таблицы, содержащей сведения о зарегистрировавшихся пользователях.
* **createtable.php** — создание таблицы базы данных MySQL для приложения. Этот файл будет использоваться только один раз.

Чтобы создать и запустить приложение локально, следуйте инструкциям. Обратите внимание, что предполагается наличие на локальном компьютере PHP, MySQL и веб-сервера, а также включение [расширения PDO для MySQL][pdo-mysql].

1. Создание базы данных MySQL с именем `registration`. Это можно сделать в командной строке MySQL с помощью следующей команды:

		mysql> create database registration;

2. В корневом каталоге веб-сервера создайте папку с именем `registration`, а в ней два файла: один с именем `createtable.php`, а другой с именем `index.php`.

3. Откройте файл `createtable.php` в текстовом редакторе или интегрированной среде разработки и добавьте приведенный ниже код. Этот код будет использоваться для создания таблицы `registration_tbl` в базе данных `registration`.

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

	> [AZURE.NOTE] 
	Потребуется обновить значения для <code>$user</code> и <code>$pwd</code> с использованием имени пользователя и пароля локальной базы данных MySQL.

4. Откройте веб-браузер и перейдите по адресу [http://localhost/registration/createtable.php][localhost-createtable]. В результате в базе данных будет создана таблица `registration_tbl`.

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

	> [AZURE.NOTE]
	Потребуется снова обновить значения для <code>$user</code> и <code>$pwd</code> с использованием имени пользователя и пароля локальной базы данных MySQL.

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

Чтобы подключиться к базе данных MySQL, которая работает в веб-приложениях, вам потребуются сведения о подключении. Чтобы получить сведения о подключении к MySQL, выполните следующие действия.

1. В колонке веб-приложения службы приложений щелкните ссылку группы ресурсов:

	![Выбор группы ресурсов][select-resourcegroup]

1. В группе ресурсов щелкните базу данных.

	![Выбор базы данных][select-database]

2. В сводке базы данных щелкните элементы **Параметры** > **Свойства**.

    ![Выбор свойств][select-properties]
	
2. Запишите значения `Database`, `Host`, `User Id` и `Password`.

    ![Запись свойств][note-properties]

3. В веб-приложении в правом нижнем углу страницы щелкните ссылку **Загрузить профиль публикации**.

	![Загрузить профиль публикации][download-publish-profile]

4. Откройте файл `.publishsettings` в редакторе XML.

3. Найдите элемент `<publishProfile >` с методом `publishMethod="FTP"` следующего вида:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Обратите внимание на значения атрибутов `publishUrl`, `userName` и `userPWD`.

##Публикация приложения

После локального тестирования приложения его можно опубликовать в веб-приложении с помощью FTP. Тем не менее, необходимо сначала обновить в приложении сведения подключения к базе данных. При помощи полученных ранее сведений подключения к базе данных (см. раздел **Получение сведений о подключении к базе данных MySQL и FTP**) обновите следующие сведения в **обоих** файлах (`createdatabase.php` и `index.php`) соответствующими значениями.

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

Теперь все готово для публикации приложения через FTP.

1. Откройте выбранный клиент FTP.

2. Введите в FTP-клиент *имя узла* из атрибута `publishUrl`, записанное ранее.

3. Введите в FTP-клиент атрибуты `userName` и `userPWD`, записанные ранее, без изменений.

4. Установите подключение.

После подключения можно будет отправлять и загружать файлы. Убедитесь, что файлы передаются в корневой каталог, то есть в каталог `/site/wwwroot`.

После загрузки `index.php` и `createtable.php` перейдите по адресу **http://[site name].azurewebsites.net/createtable.php**, чтобы создать таблицу базы данных MySQL для приложения, а затем перейдите по адресу **http://[site имя].azurewebsites.net/index.php**, чтобы начать работу с приложением.
 
## Дальнейшие действия

Дополнительные сведения можно найти в [Центре разработчика PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 

<!---HONumber=AcomDC_0323_2016-->
<properties linkid="develop-php-website-with-mysql-and-ftp" urlDisplayName="Веб-сайт с использованием MySQL + FTP" pageTitle="Веб-сайт на PHP с использованием MySQL и FTP — учебник Azure" metaKeywords="" description="Учебный курс показывает, как создать веб-сайт на PHP, который хранит данные в MySQL и использует FTP в Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Создание веб-сайта Azure на PHP-MySQL и развертывание с использованием FTP" authors=""  solutions="" writer="" manager="" editor=""  />


#Создание веб-сайта PHP-MySQL Azure и развертывание его с помощью FTP

В этом учебнике показано, как создать веб-сайт PHP-MySQL Azure и развернуть его с помощью FTP. В этом учебнике предполагается, что на компьютере установлены [PHP][install-php], [MySQL][install-mysql], веб-браузер и FTP-клиент. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. После завершения работы с этим учебником в Azure будет работать веб-сайт PHP/MySQL.
 
Вы узнаете следующее:

* Как создать веб-сайт Azure и базу данных MySQL с использованием портала управления Azure. Поскольку PHP по умолчанию включен для веб-сайтов Azure, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как опубликовать приложение в Azure с использованием FTP.
 
Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже:

![Веб-сайт PHP Azure][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Создание веб-сайта Azure и настройка публикации FTP

Выполните следующие действия для создания веб-сайта Azure и базы данных MySQL:

1. Войдите на [портал управления Azure][management-portal].
2. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Создание нового веб-сайта Azure][new-website]

3. Щелкните **ВЕБ-САЙТ**, а затем — **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

	![Настраиваемое создание нового веб-сайта][custom-create]
	
	Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных MySQL** в раскрывающемся списке **БАЗА ДАННЫХ** и выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Щелкните стрелку в нижней части диалогового окна.

	![Заполните сведения о веб-сайте][website-details]

4. Введите **ИМЯ** вашей базы данных, выберите центр обработки данных для ее размещения в раскрывающемся списке **РЕГИОН** и установите флажок, свидетельствующий о вашем согласии с юридическими условиями и положениями. Щелкните флажок в нижней части диалогового окна.

	![Создание новой базы данных MySQL][new-mysql-db]

	После создания веб-сайта появится сообщение **Создание веб-сайта "[ИМЯ САЙТА]" успешно завершено**. Теперь можно включить публикацию FTP.

5. В списке веб-сайтов щелкните имя веб-сайта, чтобы открыть панель мониторинга **БЫСТРЫЙ ЗАПУСК** для этого веб-сайта.

	![Откройте панель мониторинга веб-сайта][go-to-dashboard]


6. В нижней части страницы **БЫСТРЫЙ ЗАПУСК** щелкните **Сбросить учетные данные развертывания**. 

	![Сбросить учетные данные развертывания][reset-deployment-credentials]

7. Чтобы включить публикацию в FTP, необходимо указать имя пользователя и пароль. Запишите создаваемые имя пользователя и пароль.

	![Создание учетных данных для публикации][portal-git-username-password]

##Построение и тестирование приложения на локальном ресурсе

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в базе данных MySQL. Приложение состоит из двух файлов:

* **index.php** — отображает форму для регистрации и таблицу, содержащую сведения о зарегистрировавшихся пользователях.
* **createtable.php** — создание таблицы базы данных MySQL для приложения. Этот файл будет использоваться только один раз.

Чтобы построить и запустить приложение локально, выполните следующие действия. Обратите внимание, что предполагается наличие на локальном компьютере PHP, MySQL и веб-сервера, а также включение [расширения PDO для MySQL][pdo-mysql].

1. Создайте базу данных MySQL под названием `registration`. Это можно сделать в командной строке MySQL с помощью следующей команды:

		mysql> create database registration;

2. В корневом каталоге веб-сервера создайте папку с именем `registration`, а в ней два файла — один с именем `createtable.php`, а другой с именем `index.php`.

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

	> [WACOM.NOTE] 
	> Потребуется обновить значения для <code>$user</code> и <code>$pwd</code> с использованием имени пользователя и пароля локальной базы данных MySQL.

4. Откройте веб-браузер и перейдите на страницу [http://localhost/registration/createtable.php][localhost-createtable]. В результате в базе данных будет создана таблица `registration_tbl`.

5. Откройте файл **index.php** в текстовом редакторе или в интегрированной среде разработки и добавьте базовый HTML-код и CSS для страницы (PHP-код будет добавлен позднее).

		<html>
		<head>
		<Title>Форма регистрации</Title>
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
		<h1>Зарегистрируйтесь!</h1>
		<p>Введите ваши имя и адрес электронной почты, затем нажмите <strong>Отправить</strong> для регистрации.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Имя  <input type="text" name="name" id="name"/></br>
		      Электронная почта <input type="text" name="email" id="email"/></br>
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

	> [WACOM.NOTE]
	> Снова потребуется обновить значения для <code>$user</code> и <code>$pwd</code> с использованием имени пользователя и пароля локальной базы данных MySQL.

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

Теперь можно перейти к [http://localhost/registration/index.php][localhost-index] для тестирования приложения.

##Получение сведений о подключении к MySQL и FTP

Чтобы подключиться к базе данных MySQL, которая работает на веб-сайтах Azure, необходимо знать сведения о подключении. Чтобы получить сведения о подключении к MySQL, выполните следующие действия.

1. На панели мониторинга веб-сайта щелкните ссылку **Просмотреть строки подключения** с правой стороны страницы:

	![Получение сведений о подключении к базе данных][connection-string-info]
	
2. Запишите значения `Database`, `Data Source`, `User Id` и `Password`.

3. На панели мониторинга веб-сайта щелкните ссылку **Просмотреть строки подключения** в правом нижнем углу страницы:

	![Загрузить профиль публикации][download-publish-profile]

4. Откройте файл `.publishsettings` в редакторе XML. 

3. Найдите элемент `<publishProfile >` с `publishMethod="FTP"` следующего вида:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Обратите внимание на значения атрибутов `publishUrl`, `userName` и `userPWD`

##Публикация приложения

После локального тестирования приложения его можно опубликовать на веб-сайте Azure, используя FTP. Тем не менее, необходимо сначала обновить в приложении сведения подключения к базе данных. При помощи полученных ранее сведений подключения к базе данных (см. раздел **Получение сведений о подключении к базе данных MySQL и FTP**) обновите следующие сведения в **обоих** файлах, `createdatabase.php` и `index.php`, соответствующими значениями:

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

Теперь все готово для публикации приложения с помощью протокола FTP.

1. Откройте выбранный клиент FTP.

2. Введите в FTP-клиент *имя хоста* из атрибута `publishUrl`, записанное ранее.

3. Введите в FTP-клиент атрибуты `userName` и `userPWD`, записанные ранее, без изменений.

4. Установите подключение.

После подключения можно будет отправлять и загружать файлы. Убедитесь, что файлы передаются в корневой каталог, т. е. в каталог `/site/wwwroot`.

После отправки `index.php` и `createtable.php` перейдите к **http://[site name].azurewebsites.net/createtable.php** для создания таблицы MySQL для приложения, затем перейдите к **http://[имя сайта].azurewebsites.net/index.php**, чтобы начать использование приложения.
 

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


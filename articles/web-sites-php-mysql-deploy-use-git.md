<properties urlDisplayName="Web w/ MySQL + Git" pageTitle="Веб-сайт на PHP с использованием MySQL и Git - учебник Azure" metaKeywords="" description="В этом учебнике показано, как создать веб-сайт на PHP, который хранит данные в MySQL и использует развертывание Git в Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />

#Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git

В этом учебнике показано, как создать веб-сайт PHP-MySQL Azure и развернуть его с помощью Git. Вы будете использовать [PHP][install-php], средство командной строки MySQL (которое входит в состав [MySQL][install-mysql]), веб-сервер и [Git][install-git], установленный на компьютере. Указания, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. После завершения работы с этим учебником в Azure будет работать веб-сайт PHP/MySQL.
 
Вы узнаете:

* Как создать веб-сайт Azure и базу данных MySQL с помощью портала управления Azure. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как опубликовать и повторно опубликовать свое приложение в Azure с помощью Git.
 
Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже:

![Azure PHP web site][running-app]

> [WACOM.NOTE]
> Чтобы пройти этот учебный курс, потребуется учетная запись Azure с включенной возможностью веб-сайтов Windows Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительную информацию см. на сайте <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Бесплатное пробное использование Azure</a>.
> 
> Если вы хотите начать работу с веб-сайтами Azure до регистрации учетной записи, перейдите по ссылке <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, где вы сможете бесплатно и быстро создать кратковременный начальный сайт ASP.NET на веб-сайтах Azure. Не требуется вводить данные кредитной карты или брать на себя какие-либо обязательства.

##Настройка среды разработки

При работе с этим учебником предполагается, что на вашем компьютере установлены [PHP][install-php], программа командной строки MySQL (которая входит в состав [MySQL][install-mysql]), веб-сервер и [Git][install-git].

> [WACOM.NOTE]
> При работе с данным учебником в Windows вы можете настроить компьютер для поддержки PHP и автоматически настроить IIS (встроенный веб-сервер Windows), установив <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">пакет SDK для Azure для PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Создание веб-сайта Azure и настройка публикации Git

Вот как можно создать веб-сайт Azure и базу данных MySQL:

1. Войдите на портал управления [Azuremanagement-portal][management-portal].
2. Щелкните значок **Создать** в нижнем левом углу портала.

	![Create New Azure web site][new-website]

3. Щелкните **Веб-сайт**, а затем выберите **Настраиваемое создание**.

	![Custom Create a new web site][custom-create]
	
	Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных MySQL** в раскрывающемся списке **База данных** и укажите центр обработки данных для вашего веб-сайта в раскрывающемся списке **Регион**. Щелкните стрелку в нижней части диалогового окна.

	![Fill in web site details][website-details]

4. Введите **имя** вашей базы данных, выберите центр обработки данных для ее размещения в раскрывающемся списке **Регион** и установите флажок, свидетельствующий о вашем согласии с юридическими условиями и положениями. Щелкните флажок в нижней части диалогового окна.

	![Create new MySQL database][new-mysql-db]

	После создания веб-сайта появится сообщение **Веб-сайт [ИМЯ_САЙТА] успешно создан**. Теперь можно включить публикацию Git.

6. В списке веб-сайтов щелкните по имени веб-сайта, чтобы открыть страницу панели управления **Быстрый запуск** для этого веб-сайта.

	![Open web site dashboard][go-to-dashboard]


7. В нижней части страницы **Быстрый запуск** щелкните **Настроить публикацию Git**. 

	![Set up Git publishing][setup-git-publishing]

8. Чтобы включить публикацию Git, необходимо предоставить имя пользователя и пароль. Запишите создаваемые имя пользователя и пароль. (Если ранее уже был настроен репозиторий Git, этот шаг будет пропущен.)

	![Create publishing credentials][credentials]

	На настройку репозитория уйдет несколько секунд.

9. После настройки репозитория появятся инструкции для передачи ваших файлов приложений в репозиторий. Запишите эти инструкции - они потребуются позже.

	![Git instructions][git-instructions]

##Получение информации об удаленном подключении к MySQL

Чтобы подключиться к базе данных SQL, которая работает на веб-сайтах Azure, необходимо знать сведения о подключении. Чтобы получить сведения о подключении к MySQL, выполните следующие действия.

1. На панели мониторинга веб-сайта перейдите по ссылке **Просмотреть строки подключения** в правой части страницы.

	![Get database connection information][connection-string-info]
	
2. Запишите значения "База данных", "Источник данных", "Идентификатор пользователя" и "Пароль".

##Построение и тестирование приложения на локальном ресурсе

После создания веб-сайта Azure вы можете разработать свое приложение локально, а затем развернуть его после тестирования. 

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в базе данных MySQL. Приложение состоит из одного файла (код для копирования и вставки приведен ниже):

* **index.php**: отображает форму для регистрации и таблицу, содержащую сведения о зарегистрировавшихся пользователях.

Чтобы построить и запустить приложение локально, выполните следующие действия. Обратите внимание, что предполагается наличие на локальном компьютере PHP, средства командной строки MySQL (входит в комплект поставки MySQL) и веб-сервера, а также включение расширения [PDO для MySQL][pdo-mysql].

1. Подключитесь к удаленному серверу MySQL с использованием раннее полученных значений "Источник данных", "Идентификатор пользователя", "Пароль" и "База данных".

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Появится командная строка MySQL:

		mysql>

3. Вставьте команду CREATE TABLE, чтобы создать в базе данных таблицу registration_tbl:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. В корневом каталоге веб-сервера создайте папку с именем registration, а в ней файл с именем index.php.

5. Откройте файл **index.php** в текстовом редакторе или в интегрированной среде разработки и добавьте следующий код, после чего внесите необходимые изменения, обозначенные комментарием "//TODO:".


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
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
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
			// Retrieve data
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
		?>
		</body>
		</html>

Теперь можно перейти по адресу **http://localhost/registration/index.php** для тестирования приложения.


##Публикация приложения

После локального тестирования приложения вы можете опубликовать его на веб-сайте Azure с использованием Git. Необходимо инициализировать локальный репозиторий Git и опубликовать свое приложение.

> [WACOM.NOTE]
> Для этого используются те же действия, которые относятся к порталу и приведены выше в конце раздела "Создание веб-сайта Azure и настройка публикации Git".

1. (Необязательно) Если вы забыли URL-адрес удаленного репозитория Git, перейдите на вкладку портала "Развертывание".
	
	![Get Git URL][git-instructions]

1. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

	![Initial Push to Azure via Git][git-initial-push]

2. Перейдите по адресу **http://[имя сайта].azurewebsites.net/index.php**, чтобы начать использование приложения (эта информация будет сохранена на панели мониторинга учетной записи):

	![Azure PHP web site][running-app]

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git. 

##Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1. Внесите изменения в приложение локально.
2. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

	![Pushing site changes to Azure via Git][git-change-push]

3. Перейдите по адресу **http://[имя сайта].azurewebsites.net/index.php**, чтобы просмотреть приложение и все внесенные изменения:

	![Azure PHP web site][running-app]

4. Вы также можете просмотреть новое развертывание на вкладке портала управления Azure "Развертывания":

	![List of web site deployments][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/ru-ru/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[sql-database-editions]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee621788.aspx

<!--HONumber=35.1-->

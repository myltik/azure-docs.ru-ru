<properties urlDisplayName="Web w/ MySQL + Git" pageTitle="Веб-сайт PHP с MySQL и Git - учебник по Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git

В этом учебнике показано, как создать веб-сайт PHP-MySQL Azure и развернуть его с помощью Git. Мы будем использовать команду [PHP][install-php], средство командной строки MySQL (часть команды [MySQL][install-mysql]), веб-сервер и команду [Git][install-git], которые установлены на вашем компьютере. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. После завершения работы с этим учебником в Azure будет работать веб-сайт PHP/MySQL.
 
Вы узнаете:

* Как создать веб-сайт Azure и базу данных MySQL с помощью портала управления Azure. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как опубликовать и повторно опубликовать свое приложение в Azure с помощью Git.
 
Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже.

![Azure PHP web site][running-app]

<div class="dev-callout"><strong>Примечание</strong> <p>Чтобы пройти этот учебный курс, требуется учетная запись Azure с включенной возможностью веб-сайтов Windows Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Azure Free Trial</a>.</p> </div>

##Set up the development environment

This tutorial assumes you have [PHP][install-php], the MySQL Command-Line Tool (part of [MySQL][install-mysql]), a web server, and [Git][install-git] installed on your computer.

> [WACOM.NOTE]
> If you are performing this tutorial on Windows, you can set up your machine for PHP and automatically configure IIS (the built-in web server in Windows) by installing the <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Azure SDK for PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Create an Azure website and set up Git publishing

Follow these steps to create an Azure website and a MySQL database:

1. Login to the [Azure Management Portal][management-portal].
2. Click the **New** icon on the bottom left of the portal.

	![Create New Azure web site][new-website]

3. Click **WebSite**, then **Custom Create**.

	![Custom Create a new web site][custom-create]
	
	Enter a value for **URL**, select **Create a New MySQL Database** from the **Database** dropdown,  and select the data center for your website in the **Region** dropdown. Click the arrow at the bottom of the dialog.

	![Fill in web site details][website-details]

4. Enter a value for the **Name** of your database, select the data center for your database in the **Region** dropdown, and check the box that indicates you agree with the legal terms. Click the checkmark at the bottom of the dialog.

	![Create new MySQL database][new-mysql-db]

	When the website has been created you will see the text **Creation of Website "[SITENAME]" completed successfully**. Now, you can enable Git publishing.

6. Click the name of the website displayed in the list of websites to open the website's **QuickStart** dashboard.

	![Open web site dashboard][go-to-dashboard]


7. At the bottom of the **QuickStart** page, click **Set up Git publishing**. 

	![Set up Git publishing][setup-git-publishing]

8. To enable Git publishing, you must provide a user name and password. Make a note of the user name and password you create. (If you have set up a Git repository before, this step will be skipped.)

	![Create publishing credentials][credentials]

	It will take a few seconds to set up your repository.

9. When your repository is ready, you will see instructions for pushing your application files to the repository. Make note of these instructions - they will be needed later.

	![Git instructions][git-instructions]

##Get remote MySQL connection information

To connect to the MySQL database that is running in Azure Websites, your will need the connection information. To get MySQL connection information, follow these steps:

1. From your website's dashboard, click the **View connection strings** link on the right side of the page:

	![Get database connection information][connection-string-info]
	
2. Make note of the values for `Database`, `Data Source`, `User Id`, and `Password`.

##Build and test your application locally

Now that you have created an Azure Website, you can develop your application locally, then deploy it after testing. 

The Registration application is a simple PHP application that allows you to register for an event by providing your name and email address. Information about previous registrants is displayed in a table. Registration information is stored in a MySQL database. The application consists of one file (copy/paste code available below):

* **index.php**: Displays a form for registration and a table containing registrant information.

To build and run the application locally, follow the steps below. Note that these steps assume you have PHP, the MySQL Command-Line Tool (part of MySQL), and a web server set up on your local machine, and that you have enabled the [PDO extension for MySQL][pdo-mysql].

1. Connect to the remote MySQL server, using the value for `Data Source`, `User Id`, `Password`, and `Database` that you retrieved earlier:

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. The MySQL command prompt will appear:

		mysql>

3. Paste in the following `CREATE TABLE` command to create the `registration_tbl` table in your database:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. In your web server's root directory, create a folder called `registration` and create a file in it called `index.php`.

5. Open the **index.php** file in a text editor or IDE and add the following code, and complete the necessary changes marked with `//TODO:` comments.


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
			// данные для подключения базы данных
			//СДЕЛАТЬ: обновить значения переменных $host, $user, $pwd и $db
						//используя значения, ранее полученные с портала.
			$host = "значение источника данных";
			$user = "значение ИД пользователя";
			$pwd = "значение пароля";
			$db = "значение базы данных";
						// Подключитесь к базе данных.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			/ / Вставьте сведения о регистрации
			if(!empty($_POST)) {
			try {
				$name = $_POST['name'];
				$email = $_POST['email'];
				$date = date("Y-m-d");
				// Ввод данных
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
			echo "<h3>Регистрация завершена!</h3>";
			}
			// Извлечение данных
			$sql_select = "SELECT * FROM registration_tbl";
			$stmt = $conn->query($sql_select);
			$registrants = $stmt->fetchAll(); 
			if(count($registrants) > 0) {
				echo "<h2>Зарегистрированные пользователи:</h2>";
				echo "<table>";
				echo "<tr><th>Имя</th>";
			echo "<th>Адрес эл. почты</th>";
				echo "<th>Дата</th></tr>";
				foreach($registrants as $registrant) {
					echo "<tr><td>".$registrant['name']."</td>";
					echo "<td>".$registrant['email']."</td>";
					echo "<td>".$registrant['date']."</td></tr>";
		    	}
		 		echo "</table>";
			} else {
				echo "<h3>Зарегистрированных пользователей нет.</h3>";
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
		git remote add azure [URL-адрес удаленного репозитория]
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

	![Initial Push to Azure via Git][git-initial-push]

2. Перейдите на сайт **http://[имя сайта].azurewebsites.net/index.php**, чтобы начать пользоваться приложением (данная информация будет размещена на информационной панели в вашей учетной записи).

	![Azure PHP web site][running-app]

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git. 

##Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1. Внесите изменения в приложение локально.
2. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git add .
		git commit -m "комментарий с описанием изменений"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

	![Pushing site changes to Azure via Git][git-change-push]

3. Перейдите на сайт **http://[имя сайта].azurewebsites.net/index.php**, чтобы увидеть свое приложение и все сделанные вами изменения.

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

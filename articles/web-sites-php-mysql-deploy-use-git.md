<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Веб-сайт c MySQL + Git" pageTitle="Веб-сайт PHP с MySQL и Git – учебник по Azure" metaKeywords="" description="Учебник, в котором показано, как создать веб-сайт PHP, который хранит данные в MySQL и использует развертывание Git в Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Создание веб-сайта PHP-MySQL Azure и развертывание его с помощью Git" authors=""  solutions="" writer="waltpo" manager="" editor="mollybos"  />

#Создание веб-сайта PHP-MySQL Azure и развертывание его с помощью Git

В этом учебнике показано, как создать веб-сайт PHP-MySQL Azure и развернуть его с помощью Git. Вы будете использовать [PHP][install-php], средство командной строки MySQL (которое входит в состав [MySQL][install-mysql]), веб-сервер и [Git][install-git], установленный на компьютере. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. После завершения работы с этим учебником в Azure будет работать веб-сайт PHP/MySQL.
 
Вы узнаете следующее:

* Как создать веб-сайт Azure и базу данных MySQL с использованием портала управления Azure. Поскольку PHP по умолчанию включен для веб-сайтов Azure, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как опубликовать и повторно опубликовать приложение в Azure, используя Git.
 
Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже:

![Веб-сайт PHP Azure][running-app]

<div class="dev-callout"><strong>Примечание.</strong> <p>Чтобы завершить работу с этим руководством, вам потребуется учетная запись Azure с включенными веб-сайтами Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Бесплатная пробная версия Azure</a>.</p> </div>

##Настройка среды разработки

При работе с этим руководством предполагается, что на компьютере установлены [PHP][install-php], интерфейс командной стройки MySQL (входит в состав [MySQL][install-mysql]), веб-сервер и [Git][install-git].

> [WACOM.ПРИМЕЧАНИЕ]
> При работе с данными учебником в Windows можно настроить компьютер для поддержки PHP и автоматически настроить IIS (встроенного веб-сервер Windows), установив <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Azure SDK для PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Создание веб-сайта Azure и настройка публикации Git

Выполните следующие действия для создания веб-сайта Azure и базы данных MySQL.

1. Выполните вход на [портал управления Azure][management-portal].
2. Щелкните значок **Создать** в нижнем левом углу портала.

	![Создание нового веб-сайта Azure][new-website]

3. Щелкните **Веб-сайт**, затем **Настраиваемое создание**.

	![Настраиваемое создание нового веб-сайта][custom-create]
	
	Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных MySQL** в раскрывающемся списке **База данных** и выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **Регион**. Щелкните стрелку в нижней части диалогового окна.

	![Заполните сведения о веб-сайте][website-details]

4. Введите **имя** вашей базы данных, выберите центр обработки данных для ее размещения в раскрывающемся списке **Регион** и установите флажок, свидетельствующий о вашем согласии с юридическими условиями и положениями. Щелкните флажок в нижней части диалогового окна.

	![Создание новой базы данных MySQL][new-mysql-db]

	После создания веб-сайта появится сообщение **Создание веб-сайта [ИМЯ_САЙТА] успешно завершено**. Теперь можно включить публикацию Git.

6. В списке веб-сайтов щелкните имя веб-сайта в списке веб-сайтов, чтобы открыть панель мониторинга **Быстрый запуск** для этого веб-сайта.

	![Откройте панель управления веб-сайта][go-to-dashboard]


7. В нижней части страницы **Быстрый запуск** щелкните **Настроить публикацию Git**. 

	![Настроить публикацию Git][setup-git-publishing]

8. Чтобы включить публикацию Git, необходимо предоставить имя пользователя и пароль. Запишите создаваемые имя пользователя и пароль. (Если ранее уже был настроен репозиторий Git, этот шаг будет пропущен.)

	![Создание учетных данных для публикации][credentials]

	На настройку репозитория уйдет несколько секунд.

9. После настройки репозитория появятся инструкции для передачи ваших файлов приложений в репозиторий. Запишите эти инструкции — они потребуются позже.

	![Инструкции для Git][git-instructions]

##Получение сведений об удаленном подключении к MySQL

Чтобы подключиться к базе данных MySQL, которая работает на веб-сайтах Azure, необходимо знать сведения о подключении. Чтобы получить сведения о подключении к MySQL, выполните следующие действия.

1. На панели мониторинга веб-сайта щелкните ссылку **Просмотреть строки подключения** с правой стороны страницы:

	![Получение сведений о подключении к базе данных][connection-string-info]
	
2. Обратите внимание на значения для `Database`, `Data Source`, `User Id` и `Password`.

##Построение и тестирование приложения на локальном ресурсе

После создания веб-сайта Azure можно разработать свое приложение локально, а затем развернуть его после тестирования. 

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в базе данных MySQL. Приложение состоит из одного файла (код для копирования и вставки приведен ниже):

* **index.php**: отображает форму для регистрации и таблицу, содержащую сведения о зарегистрировавшихся пользователях.

Чтобы построить и запустить приложение локально, выполните следующие действия. Обратите внимание, что предполагается наличие на локальном компьютере PHP, средства командной строки MySQL (входит в комплект поставки MySQL) и веб-сервера, а также включение [расширения PDO для MySQL][pdo-mysql]

1. Подключение к удаленному серверу MySQL с использованием раннее полученных значений `Data Source`, `User Id`, `Password` и `Database`.

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Появится командная строка MySQL:

		mysql>

3. Вставьте команду `CREATE TABLE`, чтобы создать в базе данных таблицу `registration_tbl`:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. В корневом каталоге веб-сервера создайте папку с именем `registration`, а в ней файл с именем `index.php`.

5. Откройте файл **index.php** в текстовом редакторе или в интегрированной среде разработки и добавьте следующий код, после чего внесите необходимые изменения, обозначенные комментарием `//TODO:`.


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
			таблица { margin-top: 0.75em; }
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
			// Сведения о подключении к БД
			//TODO: Обновите значения $host, $user, $pwd и $db,
			//используя значения, ранее полученные с портала.
			$host = "значение источника данных";
			$user = "значение идентификатора пользователя";
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
			echo "<h3>Вы зарегистрированы!</h3>";
			}
			// Retrieve data
			$sql_select = "SELECT * FROM registration_tbl";
			$stmt = $conn->query($sql_select);
			$registrants = $stmt->fetchAll(); 
			if(count($registrants) > 0) {
				echo "<h2>Другие зарегистрированные пользователи:</h2>";
				echo "<table>";
				echo "<tr><th>Имя</th>";
				echo "<th>Эл. почта</th>";
				echo "<th>Дата</th></tr>";
				foreach($registrants as $registrant) {
					echo "<tr><td>".$registrant['name']."</td>";
					echo "<td>".$registrant['email']."</td>";
					echo "<td>".$registrant['date']."</td></tr>";
		    	}
		 		echo "</table>";
			} else {
				echo "<h3>Сейчас еще никто не зарегистрирован.</h3>";
			}
		?>
		</body>
		</html>

Теперь можно перейти к **http://localhost/registration/index.php** для тестирования приложения.


##Публикация приложения

После локального тестирования приложения его можно опубликовать на веб-сайте Azure, используя Git. Необходимо инициализировать локальный репозиторий Git и опубликовать свое приложение.

> [WACOM.NOTE]
> Это те же шаги, которые также относятся к порталу и приведены в конце приведенного выше раздела "Создание веб-сайта Azure и настройка публикации Git".

1. (Необязательно) Если вы забыли URL-адрес удаленного репозитория Git, перейдите на вкладку портала "Развертывание".
	
	![Получение URL-адреса Git][git-instructions]

1. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

	![Исходная передача по методу Push в Azure через Git][git-initial-push]

2. Перейдите к **http://[имени сайта].azurewebsites.net/index.php**, чтобы начать использование приложения (эти сведения будут сохранены на панели мониторинга вашей учетной записи):

	![Веб-сайт PHP Azure][running-app]

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git. 

##Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1. Внесите изменения в приложение локально.
2. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

	![Передача изменений сайта в Azure через Git посредством технологии Push.][git-change-push]

3. Перейдите к **http://[имя веб-сайта].azurewebsites.net/index.php**, чтобы просмотреть приложения и любые внесенные в него изменения:

	![Веб-сайт PHP Azure][running-app]

4. Можно также просмотреть на вкладке портала управления Azure "Развертывание" новое развертывание:

	![Список развертываний веб-сайтов][deployments-list]

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


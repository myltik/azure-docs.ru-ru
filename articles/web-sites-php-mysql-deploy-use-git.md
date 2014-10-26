<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Web w/ MySQL + Git" pageTitle="PHP website with MySQL and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git

В этом учебнике показано, как создать веб-сайт PHP-MySQL Azure и развернуть его с помощью Git. Вы будете использовать [PHP][PHP], программу командной строки MySQL (которая входит в состав [MySQL][install-mysql]), веб-сервер и [Git][Git], установленный на компьютере. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. После завершения работы с этим учебником в Azure будет работать ваш веб-сайт PHP/MySQL.

Вы узнаете:

-   Как создать веб-сайт Azure и базу данных MySQL с помощью портала управления Azure. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
-   Как опубликовать и повторно опубликовать свое приложение в Azure с помощью Git.

Руководствуясь этим учебником, вы создадите в PHP простое веб-приложение регистрации. Приложение будет размещаться на веб-сайте Azure. Снимок экрана завершенного приложения приведен ниже:

![Веб-сайт Azure на PHP][Веб-сайт Azure на PHP]

<div class="dev-callout"><strong>Примечание.</strong> <p>Чтобы пройти этот учебный курс, потребуется учетная запись Azure с включенной функцией веб-сайтов Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Бесплатная пробная версия Azure</a>.</p> </div>

## Настройка среды разработки

При работе с этим учебником предполагается, что на вашем компьютере установлены [PHP][PHP], программа командной строки MySQL (которая входит в состав [MySQL][install-mysql]), веб-сервер и [Git][Git].

> [WACOM.NOTE]
> При работе с данным учебником в Windows вы можете настроить компьютер для поддержки PHP и автоматически настроить IIS (встроенный веб-сервер Windows), установив [пакет SDK для Azure для PHP][пакет SDK для Azure для PHP].

## <span id="create-web-site-and-set-up-git"></span></a>Создание веб-сайта Azure и настройка публикации Git

Вот как можно создать веб-сайт Azure и базу данных MySQL:

1.  Войдите на [портал управления Azure][портал управления Azure].
2.  Щелкните значок **Создать** в нижнем левом углу портала.

    ![Создание нового веб-сайта Azure][Создание нового веб-сайта Azure]

3.  Щелкните **Веб-сайт**, затем **Настраиваемое создание**.

    ![Настраиваемое создание нового веб-сайта][Настраиваемое создание нового веб-сайта]

    Введите значение в поле **URL-адрес**, в раскрывающемся списке **База данных** выберите **Создать новую базу данных MySQL** и в раскрывающемся списке **Регион** выберите центр обработки данных для вашего веб-сайта. Щелкните стрелку в нижней части диалогового окна.

    ![Заполните сведения о веб-сайте][Заполните сведения о веб-сайте]

4.  Введите **имя** вашей базы данных, выберите центр обработки данных для ее размещения в раскрывающемся списке **Регион** и установите флажок, свидетельствующий о вашем согласии с юридическими условиями и положениями. Щелкните флажок в нижней части диалогового окна.

    ![Создание новой базы данных MySQL][Создание новой базы данных MySQL]

    После создания веб-сайта вы увидите текст **Веб-сайт [SITENAME] успешно создан**. Теперь можно включить публикацию Git.

5.  В списке веб-сайтов щелкните имя веб-сайта, чтобы открыть панель мониторинга **Быстрый запуск** для этого веб-сайта.

    ![Откройте панель мониторинга веб-сайта][Откройте панель мониторинга веб-сайта]

6.  В нижней части страницы **Быстрый запуск** щелкните **Настроить публикацию Git**.

    ![Настроить публикацию Git][Настроить публикацию Git]

7.  Чтобы включить публикацию Git, необходимо предоставить имя пользователя и пароль. Запишите создаваемые имя пользователя и пароль. (Если ранее уже был настроен репозиторий Git, этот шаг будет пропущен.)

    ![Создание учетных данных для публикации][Создание учетных данных для публикации]

    На настройку репозитория уйдет несколько секунд.

8.  После настройки репозитория появятся инструкции для передачи ваших файлов приложений в репозиторий. Запишите эти инструкции — они потребуются позже.

    ![Инструкции для Git][Инструкции для Git]

## Получение информации об удаленном подключении к MySQL

Чтобы подключиться к базе данных MySQL, которая работает на веб-сайтах Azure, необходимо знать информацию о подключении. Чтобы получить сведения о подключении к MySQL, выполните следующие действия.

1.  На панели мониторинга веб-сайта щелкните ссылку **Просмотреть строки подключения** в правой части страницы:

    ![Получение сведений о подключении к базе данных][Получение сведений о подключении к базе данных]

2.  Запишите значения `Database`, `Data Source`, `User Id` и `Password`.

## Выполнение сборки и тестирование приложения на локальном компьютере

После создания веб-сайта Azure вы можете разработать свое приложение локально, а затем развернуть его после тестирования.

Приложение регистрации представляет собой простое приложение PHP, которое позволяет регистрироваться на мероприятие, предоставляя свое имя и адрес электронной почты. Сведения о ранее зарегистрировавшихся пользователях отображаются в таблице. Сведения о регистрации хранятся в базе данных MySQL. Приложение состоит из одного файла (код для копирования и вставки приведен ниже):

-   **index.php**: отображает форму для регистрации и таблицу, содержащую информацию о зарегистрировавшихся пользователях.

Чтобы построить и запустить приложение локально, выполните следующие действия. Обратите внимание, что предполагается наличие на локальном компьютере PHP, средства командной строки MySQL (входит в комплект поставки MySQL) и веб-сервера, а также включение [расширения PDO для MySQL][расширения PDO для MySQL]

1.  Подключитесь к удаленному серверу MySQL с использованием раннее полученных значений `Data Source`, `User Id`, `Password` и `Database`:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2.  Появится командная строка MySQL:

        mysql>

3.  Вставьте команду `CREATE TABLE`, чтобы создать в базе данных таблицу `registration_tbl`:

        mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4.  В корневом каталоге веб-сервера создайте папку с именем `registration`, а в ней файл с именем `index.php`.

5.  Откройте файл **index.php** в текстовом редакторе или в интегрированной среде разработки и добавьте следующий код, после чего внесите необходимые изменения, обозначенные комментарием `//TODO:`.

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

## Публикация приложения

После локального тестирования приложения вы можете опубликовать его на веб-сайте Azure с использованием Git. Необходимо инициализировать локальный репозиторий Git и опубликовать свое приложение.

> [WACOM.NOTE]
> Для этого используются те же действия, которые относятся к порталу и приведены выше в конце раздела «Создание веб-сайта Azure и настройка публикации Git».

1.  (Необязательно) Если вы забыли URL-адрес удаленного репозитория Git, перейдите на вкладку портала "Развертывание".

    ![Получение URL-адреса Git][Инструкции для Git]

2.  Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Появится запрос на ввод ранее заданного пароля.

    ![Исходная отправка в Azure через Git][Исходная отправка в Azure через Git]

3.  Перейдите к **http://[имя сайта].azurewebsites.net/index.php**, чтобы начать использование приложения (эта информация будет сохранена на панели мониторинга учетной записи):

    ![Веб-сайт Azure на PHP][Веб-сайт Azure на PHP]

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git.

## Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1.  Внесите изменения в приложение локально.
2.  Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Появится запрос на ввод ранее заданного пароля.

    ![Отправка изменений сайта в Azure через Git][Отправка изменений сайта в Azure через Git]

3.  Перейдите к **http://[имя сайта].azurewebsites.net/index.php**, чтобы просмотреть приложение и все внесенные изменения:

    ![Веб-сайт Azure на PHP][Веб-сайт Azure на PHP]

4.  Вы также можете просмотреть новое развертывание на вкладке портала управления Azure «Развертывания»:

    ![Список развертываний веб-сайтов][Список развертываний веб-сайтов]

  [PHP]: http://www.php.net/manual/en/install.php
  [Git]: http://git-scm.com/
  [Веб-сайт Azure на PHP]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A74E0F923
  [пакет SDK для Azure для PHP]: http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids
  [портал управления Azure]: https://manage.windowsazure.com
  [Создание нового веб-сайта Azure]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
  [Настраиваемое создание нового веб-сайта]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
  [Заполните сведения о веб-сайте]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
  [Создание новой базы данных MySQL]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
  [Откройте панель мониторинга веб-сайта]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
  [Настроить публикацию Git]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
  [Создание учетных данных для публикации]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png
  [Инструкции для Git]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
  [Получение сведений о подключении к базе данных]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
  [расширения PDO для MySQL]: http://www.php.net/manual/en/ref.pdo-mysql.php
  [Исходная отправка в Azure через Git]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
  [http://[имя]: http://[site
  [Отправка изменений сайта в Azure через Git]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
  [Список развертываний веб-сайтов]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png

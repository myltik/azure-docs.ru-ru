<properties linkid="develop-php-sql-database" urlDisplayName="База данных SQL" pageTitle="Использование базы данных SQL (PHP) — руководства по Azure" metaKeywords="база данных SQL Azure PHP, база данных SQL PHP" description="Узнайте, как с помощью PHP создать базу данных SQL Azure и подключиться к ней." metaCanonical="" services="sql-database" documentationCenter="PHP" title="Доступ к базе данных SQL Azure из PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

#Доступ к базе данных SQL Azure из PHP 

В этом руководстве описываются основные принципы использования базы данных SQL Azure из PHP. Примеры написаны на PHP. Приведенные здесь сценарии включают **создание базы данных SQL** и **подключение к базе данных SQL**. В этом руководстве освещается создание базы данных SQL с помощью [портала управления][preview-portal]. Сведения о выполнении этих задач с помощью производственного портала см. в статье [Начало работы с PHP и базой данных SQL][prod-portal-instructions] Дополнительные сведения см. в разделе [Дальнейшие действия](#NextSteps).

##Что такое база данных SQL Azure

База данных SQL Azure предоставляет собой систему управления реляционной базой данных для Azure и основана на технологии SQL Server. С базой данных SQL можно легко подготовить и развернуть решения реляционных баз данных в облаке и воспользоваться преимуществами центра распределенных данных, которые представляют собой корпоративные преимущества доступности, масштабируемости и безопасности вместе со встроенной защитой данных и возможностями самостоятельной регенерации.

##Оглавление

* [Основные понятия](#Concepts)
* [Практическое руководство. Настройка среды](#Setup)
* [Практическое руководство. Создание базы данных SQL](#CreateServer)
* [Практическое руководство. Получение сведений о подключении к базе данных SQL](#ConnectionInfo)
* [Практическое руководство. Подключение к экземпляру базы данных SQL](#Connect)
* [Дальнейшие действия](#NextSteps)

##<a id="Concepts"></a>Основные понятия
Поскольку база данных SQL Azure построена на основе технологий SQL Server, доступ к базе данных SQL из PHP очень похож на доступ из PHP к SQL Server. Можно разработать приложение локально (с использованием SQL Server), а затем подключиться к базе данных SQL, изменив только строку подключения. Но существует ряд различий между базой данных SQL и SQL Server, которые могут повлиять на работу приложения. Дополнительные сведения см. в разделе [Рекомендации и ограничения (база данных SQL)][limitations]

Для доступа к базе данных SQL из PHP рекомендуется использовать [драйверы корпорации Майкрософт для PHP и SQL Server][download-drivers] (Эти драйверы будут использоваться в примерах этой статьи). Драйверы корпорации Майкрософт для PHP и SQL Server работают только в среде Windows.

##<a id="Setup"></a>Практическое руководство. Настройка среды

Для настройки среды разработки рекомендуется использовать [Установщик веб-платформы корпорации Майкрософт][wpi-installer]. Установщик веб-платформы позволит выбрать элементы веб-платформы разработки, а затем автоматически установит и настроит их. Если загрузить установщик веб-платформы и после его запуска выбрать установку WebMatrix, PHP для WebMatrix и SQL Server Express, автоматически будет установлена полная среда разработки.

Либо можно настроить среду вручную:

* Установите PHP и настройте IIS: [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Загрузите и установите SQL Server Express: [http://www.microsoft.com/ru-ru/download/details.aspx?id=29062][install-sql-express]
* Загрузите и установите драйверы корпорации Майкрософт для PHP и SQL Server: [http://php.net/manual/en/sqlsrv.requirements.php][install-drivers].

##<a id="CreateServer"></a>Практическое руководство. Создание базы данных SQL

Выполните следующие действия для создания базы данных SQL Azure.

1. Выполните вход на [портал управления][preview-portal].
2. Щелкните **Создать** в нижнем левом углу портала.

	![Создание нового веб-сайта Azure][new-website]

3. Выберите **СЛУЖБЫ ДАННЫХ**, **БАЗА ДАННЫХ SQL** и **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

	![Настраиваемое создание новой базы данных SQL][custom-create]

4. Введите значение **ИМЯ** для базы данных, выберите **ВЫПУСК** (WEB или BUSINESS), выберите для базы данных **МАКС. РАЗМЕР**, выберите значение параметра **СОРТИРОВКА**, а затем выберите **СОЗДАТЬ сервер базы данных SQL**. Щелкните стрелку в нижней части диалогового окна. (Обратите внимание, что если база данных SQL была создана ранее, можно выбрать существующий сервер в раскрывающемся списке **Выберите сервер**.)

	![Заполните параметры базы данных SQL][database-settings]

5. Введите имя и пароль администратора (и подтвердите пароль), выберите регион, где будет создана новая база данных SQL, и установите флажок `Разрешить службам Azure доступ к серверу`.

	![Создать новый сервер базы данных SQL][create-server]

Чтобы просмотреть сведения о сервере и базе данных, щелкните **Базы данных SQL** на портале управления. Затем для просмотра соответствующих сведений можно выбрать **БАЗЫ ДАННЫХ** или **СЕРВЕРЫ**.

![Просмотр сведений о сервере и базе данных][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Практическое руководство. Получение сведений о подключении к базе данных SQL

Чтобы получить сведения о подключении к базе данных, щелкните **БАЗЫ ДАННЫХ SQL** на портале, затем щелкните имя базы данных.

![Просмотр сведений о базе данных][go-to-db-info]

Затем щелкните **Просмотр строк подключения к базе данных SQL для ADO.NET, ODBC, PHP и JDBC**.

![Показать строки подключения][show-connection-string]

В разделе PHP появившегося окна обратите внимание на значения **СЕРВЕР**, **БАЗА ДАННЫХ** и **ИМЯ ПОЛЬЗОВАТЕЛЯ**. Ваш пароль – это пароль, который вы использовали при создании базы данных SQL.

##<a id="Connect"></a>Практическое руководство. Подключение к экземпляру базы данных SQL

Следующие примеры показывают, как использовать расширения **SQLSRV** и **PDO_SQLSRV** для подключения к базе данных SQL с именем `testdb`. Понадобятся сведения, полученные в вышеприведенном разделе. Замените `SERVER_ID` идентификатором своего сервера из 10 знаков (первые 10 знаков значения "СЕРВЕР", полученного в вышеприведенном разделе) и назначьте правильные значения (свои значения имени и пароля пользователя) переменным `$user` и `$pwd`.

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}


##<a id="NextSteps"></a>Дальнейшие действия
Как уже упоминалось, использование базы данных SQL очень похоже на использование SQL Server. После подключения к базе данных SQL (как показано выше) для вставки, извлечения, обновления и удаления данных можно использовать интерфейсы API **SQLSRV** или **PDO\_SQLSRV**. Сведения об интерфейсах API **SQLSRV** и **PDO\_SQLSRV** см. в [документации на драйверы корпорации Майкрософт для PHP и SQL Server][driver-docs]. Но существует ряд различий между базой данных SQL и SQL Server, которые могут повлиять на работу приложения. Дополнительные сведения см. в разделе [Рекомендации и ограничения (база данных SQL)][limitations].

Пример, показывающий, как использовать базу данных SQL с PHP на платформе Azure, доступен по адресу <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/ru-ru/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29062
[preview-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png



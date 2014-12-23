<properties urlDisplayName="SQL Database" pageTitle="Использование базы данных SQL (PHP). Руководство по компонентам Azure"metaKeywords ="PHP баз данных SQL Azure, PHP баз данных SQL " description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/29/2014" ms.author="tomfitz" />

#Доступ к базе данных SQL Azure из PHP 

В этом руководстве описываются основные принципы использования базы данных SQL Azure с помощью PHP. Примеры написаны на PHP. Приведенные здесь сценарии включают **создание базы данных SQL** и **подключение к базе данных SQL**. В этом руководстве освещается создание базы данных SQL с помощью [портала управления][management-portal]. Сведения о выполнении этих задач с помощью производственного портала см. в статье [Начало работы с PHP и базой данных SQL][prod-portal-instructions]. Дополнительные сведения см. в разделе [Дальнейшие действия](#NextSteps).

##Что такое база данных SQL Azure

База данных SQL Azure, реализованная на базе SQL Server, представляет собой систему управления реляционными базами данных для Azure. Применение базы данных SQL упрощает подготовку и развертывание решений реляционных баз данных в облаке, а также позволяет воспользоваться преимуществами распределенного центра обработки данных: доступность корпоративного класса, масштабируемость, а также безопасность (в том числе со встроенной защитой данных и самовосстановлением).

##Оглавление

* [Основные понятия](#Concepts)
* [Практическое руководство. Настройка среды](#Setup)
* [Практическое руководство. Создание базы данных SQL](#CreateServer)
* [Практическое руководство. Получение сведений о подключении к базе данных SQL](#ConnectionInfo)
* [Практическое руководство. Подключение к экземпляру базы данных SQL](#Connect)
* [Дальнейшие действия](#NextSteps)

##<a id="Concepts"></a>Основные понятия
Поскольку база данных SQL Azure создана на базе технологии SQL Server, доступ к базе данных SQL из PHP очень похож на доступ из PHP к SQL Server. Можно разработать приложение локально (с использованием SQL Server), а затем подключиться к базе данных SQL, изменив только строку подключения. Но существует ряд различий между базой данных SQL и SQL Server, которые могут повлиять на работу приложения. Дополнительные сведения см. в разделе [Рекомендации и ограничения (база данных SQL)][limitations].

Для доступа к базе данных SQL из PHP рекомендуется использовать [драйверы корпорации Майкрософт для PHP и сервера SQL][download-drivers]. (Эти драйверы будут использоваться в примерах этой статьи). Драйверы корпорации Майкрософт для PHP и серверы SQL Server работают только в среде Windows.

##<a id="Setup"></a>Практическое руководство. Настройка среды

Для настройки среды разработки рекомендуется использовать [Установщик веб-платформы Майкрософт][wpi-installer]. Установщик веб-платформы позволит выбрать элементы веб-платформы разработки, а затем автоматически установит и настроит их. Если загрузить установщик веб-платформы и после его запуска выбрать установку WebMatrix, PHP для WebMatrix и SQL Server Express, то автоматически будет установлена полная среда разработки.

Либо можно настроить среду вручную:

* Установка PHP и настройка IIS: [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Загрузка и установка SQL Server Express: [http://www.microsoft.com/ru-ru/download/details.aspx?id=29062][install-sql-express]
* Загрузка и установка драйверов Майкрософт для PHP и SQL Server: [http://php.net/manual/en/sqlsrv.requirements.php][install-drivers].

##<a id="CreateServer"></a>Практическое руководство. Создание базы данных SQL

Выполните следующие действия для создания базы данных SQL Azure.

1. Войдите на [Портал управления][management-portal].
2. Нажмите **Создать** в нижнем левом углу портала.

	![Create New Azure Web Site][new-website]

3. Нажмите **СЛУЖБЫ ДАННЫХ**, **БАЗА ДАННЫХ SQL** затем **БЫСТРОЕ СОЗДАНИЕ**. Введите имя для базы данных, укажите сервер баз данных (использовать имеющийся или создать новый), регион, имя и пароль администратора.

	![Custom Create a new SQL Database][quick-create]


Чтобы просмотреть сведения о сервере и базе данных, нажмите**Базы данных SQL** на портале управления. Затем для просмотра соответствующих сведений выберите **БАЗЫ ДАННЫХ** или **СЕРВЕРЫ**.

![View server and database information][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Практическое руководство. Получение сведений о подключении к базе данных SQL

Чтобы получить сведения о подключении к базе данных, щелкните **БАЗЫ ДАННЫХ SQL** на портале, затем выберите имя базы данных.

![View database information][go-to-db-info]

Затем щелкните **Просмотр строк подключения к базе данных SQL для ADO.NET, ODBC, PHP и JDBC**.

![Show connection strings][show-connection-string]

В разделе PHP результирующего окна обратите внимание на значения параметров: **СЕРВЕР**, **БАЗА ДАННЫХ**и **ИМЯ ПОЛЬЗОВАТЕЛЯ**. Ваш пароль - это пароль, который вы использовали при создании базы данных SQL.

##<a id="Connect"></a>Практическое руководство. Подключение к экземпляру базы данных SQL

В приведенных ниже примерах показано, как использовать расширения **SQLSRV** и **PDO_SQLSRV** для подключения к базе данных SQL с именем "testdb". Понадобятся сведения, полученные в вышеприведенном разделе. Замените "SERVER_ID" вашим десятизначным идентификатором сервера (первые 10 символов значения параметра СЕРВЕР, полученного в предыдущем разделе) и присвойте нужные значения (ваше имя пользователя и пароль) переменным "$user" и "$pwd".

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
Как уже упоминалось, использование базы данных SQL очень похоже на использование сервера SQL. Как только вы установили соединение к базе данных SQL (как показано выше), вы можете использовать API **SQLSRV** или **PDO\_SQLSRV** для ввода, извлечения, обновления и удаления данных. Для получения информации об интерфейсах API **SQLSRV** и **PDO\_SQLSRV** см. [документацию Microsoft о PHP для серверов SQL][, раздел о драйверах]. Но существует ряд различий между базой данных SQL и SQL Server, которые могут повлиять на работу приложения. Дополнительные сведения см. в разделе [Рекомендации и ограничения (базы данных SQL)][; ограничения].

Пример, демонстрирующий использование базы данных SQL с PHP на платформе Azure, доступен по адресу <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/ru-ru/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use-sql-database/create-new-sql.png

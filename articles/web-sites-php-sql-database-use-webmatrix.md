<properties urlDisplayName="Web w/ SQL + WebMatrix" pageTitle="Веб-сайт PHP с базой данных SQL и WebMatrix - Azure" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in SQL Database." metaCanonical="" services="" documentationCenter="" title="Create and Deploy a PHP Website and SQL Database using WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />





#Создание и развертывание веб-сайта на PHP и базы данных SQL с использованием WebMatrix

В этом учебном руководстве показано, как с помощью WebMatrix разработать и развернуть приложение PHP, которое использует базу данных SQL Azure, на веб-сайте Azure. WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-сайта. WebMatrix поддерживает PHP, а также intellisense для разработки PHP. 

В этом учебном курсе предполагается, что на компьютере установлен [SQL Server Express][install-SQLExpress], позволяющий тестировать приложение локально. Тем не менее, этот учебный курс можно пройти без установленного сервера SQL Server Express. Вместо этого можно развернуть приложение непосредственно на веб-сайтах Azure.

После завершения работы с этим учебным курсом у вас будет веб-сайт PHP-SQL на платформе Windows Azure.
 
Вы узнаете:

* Как создать веб-сайт Azure и базу данных SQL с помощью портала управления. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как разработать приложение PHP с использованием WebMatrix.
* Как опубликовать и повторно опубликовать приложение в Azure с помощью WebMatrix.
 
Руководствуясь этим учебником, вы создадите простое веб-приложение списка задач Tasklist на PHP. Приложение будет размещаться на веб-сайте Azure. Снимок экрана работающего приложения приведен ниже.

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Предварительные требования

1. [Загрузите][tasklist-sqlazure-download] файлы приложения Tasklist. Приложение Tasklist представляет простое приложение на PHP, которое позволяет добавлять элементы в список задач, отмечать их как выполненные, а также удалять их из списка. Элементы списка задач хранятся в базе данных SQL (SQL Server Express для локального тестирования). Приложение состоит из следующих файлов:

* **index.php**: отображает задачи и предоставляет форму для добавления элемента в список.
* **additem.php**: добавляет элемент в список.
* **getitems.php**: получает все элементы из базы данных.
* **markitemcomplete.php**: изменяет состояние элемента на "Выполненный".
* **deleteitem.php**: удаляет элемент.
* **taskmodel.php**: содержит функции, которые добавляют, возвращают, обновляют и удаляют элементы в базе данных.
* **createtable.php**: создает таблицу SQL для приложения. Этот файл будет использоваться только один раз.

2. Создайте базу данных SQL Server под названием "tasklist". Вы можете сделать это в командной строке "sqlcmd" с помощью следующих команд.

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Этот шаг необходим только в том случае, если нужно протестировать приложение локально.

## Создайте веб-сайт и базу данных SQL

1. Войдите на [портал управления][preview-portal].
2. Щелкните значок **+ Создать** в левом нижнем углу портала.

	![Create New Azure Web Site][NewWebSite1]

3. Щелкните **ВЕБ-САЙТ**, затем **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

	![Custom Create a new Web Site][NewWebSite2]

	Введите значение для **URL-адреса**, выберите **Создание базы данных SQL** из раскрывающегося списка **БАЗА ДАННЫХ**, затем выберите центр обработки данных для вашего сайта из раскрывающегося списка **РЕГИОН**. Щелкните стрелку в нижней части диалогового окна.

	![Fill in web site details][NewWebSite3_SQL]

4. Введите значение для параметра базы данных **ИМЯ** и выберите **Создать сервер базы данных SQL**. Введите имя для входа и пароль (и подтвердите пароль). Выберите регион, в котором будет создан новый сервер базы данных SQL.

	![Fill in SQL Database settings][NewWebSite4_SQL]

	Когда сайт будет создан, вы увидите текст **Создание веб-сайта "[ИМЯ САЙТА]" успешно выполнено**. Затем будут выведены сведения о подключении к базе данных.

5. Нажмите **СВЯЗАННЫЕ РЕСУРСЫ**, затем нажмите имя базы данных.

	![Linked Resources][NewWebSite6_SQL]

6. Нажмите **Просмотреть строки подключения**.

	![Connection string][NewWebSite7]
	
Из раздела **PHP** открывшегося диалогового окна запишите значения "UID", "PWD", "Database" и "$serverName". Эти сведения будут использоваться вами позже.

##Установка WebMatrix

WebMatrix можно установить с [портала управления][preview-portal]. 

1. После входа перейдите на страницу "Быстрый запуск" своего веб-сайта и щелкните значок WebMatrix в нижней части страницы:

	![Install WebMatrix][InstallWebMatrix]

	Следуйте инструкциям на экране для установки WebMatrix.

2. После установки WebMatrix будет предпринята попытка открыть сайт как проект WebMatrix. Можно непосредственно редактировать активный сайт или загрузить локальную копию. В данном учебном курсе следует выбрать "Изменить локальную копию". 

3. Когда появится запрос на загрузку вашего веб-сайта, выберите **Да, установить из коллекции шаблонов**.

	![Download web site][download-site]

4. Выберите в списке доступных шаблонов **PHP**.

	![Site from template][site-from-template]

5. Выберите шаблон **Пустой сайт**. Укажите имя сайта и нажмите кнопку **ДАЛЕЕ**.

	![Provide name for site][site-from-template-2]

Веб-сайт будет открыт в WebMatrix с некоторыми файлами по умолчанию.

##Разработка приложения

В следующих шагах вам предстоит разработать приложение Tasklist, добавив загруженные ранее файлы и внеся некоторые изменения. Тем не менее вы можете добавить собственные существующие файлы или создать новые файлы.

1. Открыв свой сайт в WebMatrix, добавьте свои файлы приложения, нажав **Добавить существующие**.

	![WebMatrix - Add existing files][edit_addexisting]

В открывшемся диалоговом окне перейдите к загруженным ранее файлам, выделите их все и нажмите кнопку "Открыть". В ответ на приглашение выберите замену файла "index.php". 

2. Далее вам необходимо добавить сведения о подключении к вашей локальной базе данных SQL Server в файл "taskmodel.php". Откройте файл "taskmodel.php", дважды щелкнув его, а затем обновите сведения о подключении к базе данных в функции "connect". (**Примечание**. Если не требуется тестировать приложение локально и вместо этого нужно опубликовать его непосредственно на веб-сайтах Azure, перейдите к разделу [Публикация приложения](#Publish).)

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Сохраните файл "taskmodel.php".

3. Для запуска приложения необходимо создать таблицу "items". Нажмите правой кнопкой файл "createtable.php" и выберите команду **Запустить в браузере**. Это приведет к запуску createtable.php в браузере и выполнению кода, который создает таблицу items в базе данных tasklist.

	![WebMatrix - Launch createtable.php in browser][edit_run]

4. Теперь можно перейти к локальному тестированию приложения. Щелкните правой кнопкой мыши файл "index.php" и выберите команду **Запустить в браузере**. Протестируйте приложение, добавляя элементы, отмечая их как выполненные и удаляя их.   


<h2><a id="Publish"></a>Публикация приложения</h2>

Перед публикацией вашего приложения на веб-сайтах Azure необходимо обновить сведения о подключении к базе данных в файле "taskmodel.php" с указанием полученных ранее данных (при выполнении действий в разделе [Создание веб-сайта Azure и базы данных SQL](#CreateWebsite)).

1. Откройте файл "taskmodel.php", дважды щелкнув его, а затем обновите сведения о подключении к базе данных в функции "connect".

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the website";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Нажмите кнопку **Опубликовать** в WebMatrix, а затем кнопку **Продолжить** в диалоговом окне **Просмотр публикации**.

	![WebMatrix - Publish][edit_publish]

3. Перейдите по адресу http://[имя вашего веб-сайта].azurewebsites.net/createtable.php, чтобы создать таблицу "items".

4. Наконец, перейдите по адресу http://[имя вашего веб-сайта].azurewebsites.net/index.php, чтобы запустить приложение.
	
##Изменение и повторная публикация приложения

Вы можете легко изменить приложение, отредактировав загруженную ранее локальную копию сайта и опубликовав его повторно либо отредактировав непосредственно в удаленном режиме. Здесь вам предстоит внести простое изменение в заголовок файла index.php и сохранить его непосредственно на действующем веб-сайте.

1. Перейдите на вкладку "Удаленный режим" своего веб-сайта в WebMatrix и выберите команду **Открыть удаленное представление**. Это действие сделает доступным непосредственное редактирование удаленного веб-сайта.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Откройте файл "index.php", дважды нажав его.
	![WebMatrix - Open index file][Remote_editIndex]

3. Измените **My ToDo List** на **My Task List** в **названии** и тегах **h1**, после чего сохраните файл.


4. После сохранения нажмите кнопку "Запустить", чтобы увидеть изменения на действующем сайте.
	![WebMatrix - Launch site in Remote][Remote_run]



## Дальнейшие действия

Вы узнали, как создать и развернуть веб-сайт из WebMatrix в Azure. Для получения дополнительных сведений о WebMatrix посетите перечисленные ниже ресурсы.

* [WebMatrix для Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Веб-сайт WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-SQLExpress]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png





















[preview-portal]: https://manage.windowsazure.com









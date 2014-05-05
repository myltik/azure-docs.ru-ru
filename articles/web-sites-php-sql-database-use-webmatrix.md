<properties linkid="develop-php-website-with-sql-database-and-webmatrix" urlDisplayName="Веб с SQL + WebMatrix" pageTitle="Веб-сайт на PHP с базой данных SQL и WebMatrix - Azure" metaKeywords="" description="Учебный курс, в котором показано, как использовать бесплатную интегрированную среду разработки WebMatrix для создания и развертывания веб-сайта на PHP, который хранит данные в базе данных SQL." metaCanonical="" services="" documentationCenter="" title="Создание и развертывание веб-сайта на PHP и базы данных SQL с использованием WebMatrix" authors="" solutions="" manager="" editor="mollybos" />





#Создание и развертывание веб-сайта на PHP и базы данных SQL с использованием WebMatrix

В этом учебном руководстве показано, как с помощью WebMatrix разработать и развернуть приложение PHP, которое использует базу данных SQL Azure, на веб-сайте Azure. WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-сайта. WebMatrix поддерживает PHP, а также intellisense для разработки PHP. 

В этом учебном курсе предполагается, что на компьютере установлен [SQL Server Express][install-SQLExpress], что позволит тестировать приложение локально. Тем не менее, этот учебный курс можно пройти без установленного сервера SQL Server Express. Вместо этого можно развернуть приложение непосредственно на веб-сайтах Azure.

После завершения работы с этим учебным курсом у вас будет веб-сайт PHP-SQL в Azure.
 
Вы узнаете:

* Как создать веб-сайт Azure и базу данных SQL с помощью портала управления. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как разработать приложение PHP с использованием WebMatrix.
* Как опубликовать и повторно опубликовать приложение в Azure с помощью WebMatrix.
 
Руководствуясь этим учебником, вы создадите простое веб-приложение списка задач (Tasklist) на PHP. Приложение будет размещаться на веб-сайте Azure. Снимок экрана работающего приложения приведен ниже:

![Веб-сайт Azure на PHP][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Предварительные требования

1. [Загрузите][tasklist-sqlazure-download] файлы приложения Tasklist. Приложение Tasklist представляет простое приложение на PHP, которое позволяет добавлять элементы в список задач, отмечать их как выполненные, а также удалять их из списка. Элементы списка задач хранятся в базе данных SQL (SQL Server Express для локального тестирования). Приложение состоит из следующих файлов:

* **index.php**: отображение задач и предоставление формы для добавления элемента в список.
* **additem.php**: добавление элемента в список.
* **getitems.php**: получение всех элементов в базе данных.
* **markitemcomplete.php**: изменение состояния элемента на выполненный.
* **deleteitem.php**: удаление элемента.
* **taskmodel.php**: функции для добавления, получения, обновления элементов и их удаления из базы данных.
* **createtable.php**: создание таблицы базы данных SQL для приложения. Этот файл будет использоваться только один раз.

2. Создайте базу данных SQL Server под названием `tasklist`. Это можно сделать в командной строке `sqlcmd` с помощью следующих команд:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Этот шаг необходим только в том случае, если нужно протестировать приложение локально.

## Создание веб-сайта и базы данных SQL

1. Выполните вход на [портал управления][preview-portal].
2. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Создание нового веб-сайта Azure][NewWebSite1]

3. Щелкните **ВЕБ-САЙТ**, а затем — **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

	![Настраиваемое создание нового веб-сайта][NewWebSite2]

	Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных SQL** в раскрывающемся списке **БАЗА ДАННЫХ**, а затем выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Щелкните стрелку в нижней части диалогового окна.

	![Заполните сведения о веб-сайте][NewWebSite3_SQL]

4. Введите значение для параметра базы данных **ИМЯ** и выберите **Создать сервер базы данных SQL** Введите имя для входа и пароль (и подтвердите пароль). Выберите регион, в котором будет создан новый сервер базы данных SQL.

	![Заполните параметры базы данных SQL][NewWebSite4_SQL]

	После создания веб-сайта вы увидите текст **Веб-сайт "[ВЕБ-САЙТ]" успешно создан**. Затем будут выведены сведения о подключении к базе данных.

5. Нажмите кнопку **СВЯЗАННЫЕ РЕСУРСЫ**, затем щелкните имя базы данных.

	![Связанные ресурсы][NewWebSite6_SQL]

6. Щелкните **Просмотреть строки подключения**.

	![Строка подключения][NewWebSite7]
	
В разделе **PHP** открывшегося диалогового окна обратите внимание на значения `ИД пользователя`, `Пароль`, `База данных` и `$serverName`. Эти сведения будут использоваться позже.

##Установка WebMatrix

WebMatrix можно установить на [портале управления][preview-portal] 

1. После входа перейдите на страницу "Быстрый запуск" своего веб-сайта и щелкните значок WebMatrix в нижней части страницы:

	![Установка WebMatrix][InstallWebMatrix]

	Следуйте инструкциям на экране для установки WebMatrix.

2. После установки WebMatrix будет предпринята попытка открыть сайт как проект WebMatrix. Можно непосредственно редактировать активный сайт или загрузить локальную копию. В данном учебном курсе следует выбрать "Изменить локальную копию". 

3. Когда появится запрос на загрузку веб-сайта, выберите **Да, установить из коллекции шаблонов**.

	![Загрузка веб-сайта][download-site]

4. Выберите в списке доступных шаблонов **PHP**

	![Веб-сайт на основе шаблона][site-from-template]

5. Выберите шаблон **Пустой сайт**. Укажите имя сайта и нажмите кнопку **ДАЛЕЕ**.

	![Ввод имени сайта][site-from-template-2]

Веб-сайт будет открыт в WebMatrix с некоторыми файлами по умолчанию.

##Разработка приложения

В следующих шагах вам предстоит разработать приложение Tasklist, добавив загруженные ранее файлы и внеся некоторые изменения. Тем не менее, можно добавить собственные существующие файлы или создать новые файлы.

1. При открытом в WebMatrix своем веб-сайте добавьте свои файлы приложения, нажав кнопку **Добавить существующие**

	![WebMatrix - добавить существующие файлы][edit_addexisting]

	В открывшемся диалоговом окне перейдите к загруженным ранее файлам, выделите их все и нажмите кнопку "Открыть". В ответ на приглашение выберите замену файла `index.php`. 

2. Далее необходимо добавить сведения о подключении к локальной базе данных SQL Server в файл `taskmodel.php`. Откройте файл  `taskmodel.php`, дважды щелкнув его, а затем обновите сведения о подключении к базе данных в функции `connect`. (**Примечание**. Если не требуется тестировать приложение локально и вместо этого нужно опубликовать его непосредственно на веб-сайтах Azure, перейдите к разделу [Публикация приложения](#Publish)).

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Сохраните файл `taskmodel.php`.

3. Для запуска приложения необходимо создать таблицу `items`. Щелкните правой кнопкой мыши файл `createtable.php` и выберите команду **Запустить в браузере** Это приведет к запуску `createtable.php` в браузере и выполнению кода, который создает таблицу `items` в базе данных `tasklist`.

	![WebMatrix - запуск файла createtable.php в браузере][edit_run]

4. Теперь можно перейти к локальному тестированию приложения. Щелкните правой кнопкой мыши файл `index.php` и выберите команду **Запустить в браузере** Протестируйте приложение, добавляя элементы, отмечая их как выполненные и удаляя их.   


<h2><a id="Publish"></a>Публикация приложения</h2>

Перед публикацией приложения на веб-сайтах Azure необходимо обновить сведения о подключении к базе данных в файле `taskmodel.php` с указанием полученных ранее данных (при выполнении действий в разделе [Создание веб-сайта Azure и базы данных SQL](#CreateWebsite)).

1. Откройте файл `taskmodel.php`, дважды щелкнув его, а затем обновите сведения о подключении к базе данных в функции `connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web site";
		$db = "value of Database";
	
	Сохраните файл `taskmodel.php`.

2. Нажмите кнопку **Опубликовать** в WebMatrix, а затем — кнопку **Продолжить** в диалоговом окне **Просмотр публикации**.

	![WebMatrix - опубликовать][edit_publish]

3. Перейдите по адресу http://[имя вашего веб-сайта].azurewebsites.net/createtable.php, чтобы создать таблицу `items`.

4. Наконец, перейдите по адресу http://[имя вашего веб-сайта].azurewebsites.net/index.php, чтобы запустить приложение.
	
##Изменение и повторная публикация приложения

Можно с легкостью изменить приложение путем изменения загруженной ранее локальной копии сайта и его повторной публикации либо редактируя сайт непосредственно в удаленном режиме. Здесь вам предстоит внести простое изменение в заголовке файла `index.php` и сохранить его непосредственно на действующем веб-сайте.

1. Перейдите на вкладку "Удаленный режим" своего веб-сайта в WebMatrix и выберите команду **Открыть удаленное представление**. Это действие сделает доступным непосредственное редактирование удаленного веб-сайта.
	 ![WebMatrix - открыть удаленное представление][OpenRemoteView]
 
2. Откройте файл `index.php`, дважды щелкнув его.
	![WebMatrix - открыть файл index][Remote_editIndex]

3. Измените **Мой список дел** на **Мой список задач** в тегах **title** и **h1**, а затем сохраните файл.


4. После сохранения нажмите кнопку "Запустить", чтобы увидеть изменения на действующем сайте.
	![WebMatrix - запуск сайта в удаленном режиме][Remote_run]



## Дальнейшие действия

Было показано, как создать и развернуть веб-сайт из WebMatrix в Azure. Для получения дополнительных сведений о WebMatrix посетите перечисленные ниже ресурсы:

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



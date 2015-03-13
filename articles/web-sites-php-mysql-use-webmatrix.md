<properties 
	pageTitle="Веб-сайт на PHP с использованием MySQL и WebMatrix - учебник Azure" 
	description="В этом учебнике показано, как использовать бесплатную интегрированную среду разработки WebMatrix для создания и развертывания веб-сайта на PHP, который хранит данные в MySQL." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="tomfitz"/>





#Создание и развертывание веб-сайта PHP-MySQL Azure с помощью WebMatrix

В этом учебнике показано, как создать и развернуть приложение PHP-MySQL на веб-сайте Azure с помощью WebMatrix. WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-сайта. WebMatrix поддерживает PHP, а также intellisense для разработки PHP.

В этом учебном курсе предполагается, что на компьютере установлен [MySQL][install-mysql], что позволит тестировать приложение локально. Тем не менее, этот учебный курс можно пройти без установленного MySQL. Вместо этого можно развернуть приложение непосредственно на веб-сайтах Azure.

После завершения работы с этим учебником в Azure будет работать веб-сайт PHP-MySQL.
 
Вы узнаете:

* Как создать веб-сайт Azure и базу данных MySQL с помощью портала управления. Поскольку PHP для веб-сайтов Azure включен по умолчанию, никаких дополнительных действий для выполнения PHP-кода не требуется.
* Как разработать приложение PHP с использованием WebMatrix.
* Как опубликовать и повторно опубликовать приложение в Azure с помощью WebMatrix.
 
Руководствуясь этим учебником, вы создадите простое веб-приложение списка задач Tasklist на PHP. Приложение будет размещаться на веб-сайте Azure. Снимок экрана работающего приложения приведен ниже:

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Вы можете <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">активировать преимущества подписчика MSDN</a> или <a href="http://azure.microsoft.com/pricing/free-trial/">зарегистрироваться для получения бесплатной пробной версии</a>.
> 
> Если вы хотите ознакомиться с веб-сайтами Azure до создания учетной записи, перейдите по адресу <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, где вы можете быстро создать краткосрочный начальный сайт ASP.NET на веб-сайтах Azure бесплатно. Никаких кредитных карт и обязательств.

##Предварительные требования

1. [Скачайте][tasklist-mysql-download] файлы приложения Tasklist. Приложение Tasklist представляет простое приложение на PHP, которое позволяет добавлять элементы в список задач, отмечать их как выполненные, а также удалять их из списка. Элементы списка задач хранятся в базе данных MySQL. Приложение состоит из следующих файлов:

	* **additem.php**: добавляет элемент в список.
	* **createtable.php**: создает таблицу MySQL для приложения. Этот файл будет использоваться только один раз.
	* **deleteitem.php**: удаляет элемент.
	* **getitems.php**: получает все элементы в базе данных.
	* **index.php**: отображает задачи и предоставляет форму для добавления элемента в список.
	* **markitemcomplete.php**: изменяет состояние элемента на "Выполненный".
	* **taskmodel.php**: содержит функции, которые добавляют, возвращают, обновляют и удаляют элементы в базе данных.

1. Создание локальной базы данных MySQL с именем `tasklist`. Это можно сделать из рабочей области базы данных WebMatrix (после установки, как описано далее в учебнике) или из командной строки MySQL с помощью этой команды:

		mysql> create database tasklist;

	Этот шаг необходим только в том случае, если нужно протестировать приложение локально.

<h2><a id="CreateWebsite"></a>Создание веб-сайта Azure и базы данных MySQL</h2>

1. Войдите на [портал управления][preview-portal].
1. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Create New Azure Web Site][NewWebSite1]

1. Щелкните **ВЕБ-САЙТ**, а затем выберите **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

	![Custom Create a new Web Site][NewWebSite2]

	> [AZURE.NOTE]
	> Вы не можете создать базу данных MySQL для веб-сайта после создания самого веб-сайта. Необходимо создать веб-сайт и базу данных MySQL, как описано в следующих шагах.

1. Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных MySQL** в раскрывающемся списке **БАЗА ДАННЫХ** и выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Щелкните стрелку в нижней части диалогового окна.

	![Fill in web site details][NewWebSite3]

5. Введите **ИМЯ** вашей базы данных, выберите центр обработки данных для ее размещения в раскрывающемся списке **РЕГИОН** и установите флажок, свидетельствующий о вашем согласии с юридическими условиями и положениями. Щелкните флажок в нижней части диалогового окна.

	![Create new MySQL database][NewWebSite4]

	После создания веб-сайта вы увидите текст **Веб-сайт [ИМЯ_САЙТА] успешно создан**.

	Затем нужно получить сведения о подключении к базе данных MySQL.


6. В списке веб-сайтов щелкните имя веб-сайта, чтобы открыть страницу "Быстрый запуск" для этого веб-сайта.

	![Open web site dashboard][NewWebSite5]

7. Выберите вкладку **НАСТРОЙКА**:

	![Configure tab][NewWebSite6]

8. Прокрутите вниз до раздела **строки подключения**. Значения для `Database`, `Data Source`, `User Id` и `Password` являются соответственно именем базы данных, именем сервера, именем пользователя и паролем пользователя. Запишите сведения о подключении к базе данных, поскольку они понадобятся позже.

	![Connection string][ConnectionString]

##Установка WebMatrix и разработка приложения

WebMatrix можно установить на [портале управления][preview-portal]. 

1. После входа перейдите на страницу "Быстрый запуск" своего веб-сайта и щелкните значок WebMatrix в нижней части страницы:

	![Install WebMatrix][InstallWebMatrix]

	Следуйте инструкциям на экране для установки WebMatrix.

2. После установки WebMatrix будет предпринята попытка открыть сайт как проект WebMatrix. Можно непосредственно редактировать активный сайт или загрузить локальную копию. Для прохождения этого учебника выберите  'Edit local copy'. 

3. Когда появится запрос на скачивание веб-сайта, выберите **Да, установить из коллекции шаблонов**.

	![Download web site][download-site]

4. Выберите в списке доступных шаблонов **PHP**

	![Site from template][site-from-template]

5. Выберите шаблон **Пустой сайт**. Укажите имя сайта и нажмите кнопку **ДАЛЕЕ**.

	![Provide name for site][site-from-template-2]

	Веб-сайт будет открыт в WebMatrix с некоторыми файлами по умолчанию.

	В следующих шагах вам предстоит разработать приложение Tasklist, добавив загруженные ранее файлы и внеся некоторые изменения. Тем не менее, можно добавить собственные существующие файлы или создать новые файлы.

6. Добавьте файлы приложения, нажав кнопку **Добавить существующий**:

	![WebMatrix - Add existing files][edit_addexisting]

	В открывшемся диалоговом окне перейдите к загруженным ранее файлам, выделите их все и нажмите кнопку "Открыть". В ответ на запрос выберите замену файла `index.php`. 

7. Далее необходимо добавить информацию о подключении к локальной базе данных MySQL в файл  `taskmodel.php`. Откройте файл `taskmodel.php`, дважды щелкнув его, а затем обновите информацию о подключении к базе данных в функции `connect`. (**Примечание**. Перейдите к [Публикация приложения](#Publish) если не требуется тестировать приложение локально и вместо этого нужно опубликовать его непосредственно на веб-сайтах Azure.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Сохраните файл `taskmodel.php`.

8. Чтобы запустить приложение, необходимо создать таблицу  `items`. Щелкните правой кнопкой мыши файл `createtable.php` и выберите **Запустить в браузере**. Это запустит `createtable.php` в браузере и выполнит код, который создает таблицу `items` в базе данных `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. Теперь можно перейти к локальному тестированию приложения. Щелкните правой кнопкой мыши файл `index.php` и выберите **Запустить в браузере**. Протестируйте приложение, добавляя элементы, отмечая их как выполненные и удаляя их.  


<h2><a id="Publish"></a>Публикация приложения</h2>

Перед публикацией приложения на веб-сайтах Azure необходимо обновить в файле `taskmodel.php` информацию о подключении базы данных, указав информацию о подключении, полученные ранее (в разделе [Создание веб-сайта Azure и базы данных MySQL](#CreateWebsite) ).

1. Откройте файл `taskmodel.php`, дважды щелкнув его, а затем обновите информацию о подключении к базе данных в функции `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Нажмите кнопку **Опубликовать** в WebMatrix, а затем - кнопку **Продолжить** в диалоговом окне **Просмотр публикации**.

	![WebMatrix - Publish][edit_publish]

3. Перейдите к http://[имя вашего веб-сайта].azurewebsites.net/createtable.php для создания таблицы `items`.

4. И наконец, перейдите к http://[имя вашего веб-сайта].azurewebsites.net/index.php  для использования приложения.
	
##Изменение и повторная публикация приложения

Можно с легкостью изменить приложение путем изменения загруженной ранее локальной копии сайта и его повторной публикации либо путем редактирования непосредственно в удаленном режиме. Внесите здесь простое изменение в заголовок файла `index.php` и сохраните его непосредственно на работающем сайте.

1. Перейдите на вкладку "Удаленный режим" своего веб-сайта в WebMatrix и выберите команду **Открыть удаленное представление**. Это действие сделает доступным непосредственное редактирование удаленного веб-сайта.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Откройте файл `index.php`, дважды щелкнув его.
	![WebMatrix - Open index file][Remote_editIndex]

3. Измените **Мой список дел** на **Мой список задач** в тегах **title** и **h1**, а затем сохраните файл.


4. После сохранения нажмите кнопку "Запустить", чтобы увидеть изменения на действующем сайте.
	![WebMatrix - Launch site in Remote][Remote_run]


# Дальнейшие действия

Теперь вы увидели, как создать и развернуть веб-сайт из WebMatrix в Azure. Для получения дополнительных сведений о WebMatrix посетите перечисленные ниже ресурсы:

* [WebMatrix для Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Веб-сайт WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com















<!--HONumber=42-->

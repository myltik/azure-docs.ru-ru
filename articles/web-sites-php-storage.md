<properties urlDisplayName="Web w/ Storage" pageTitle="Создание веб-сайта на PHP с использованием табличного хранилища Azure - учебник Azure" metaKeywords="Azure table storage PHP, Azure PHP website, Azure PHP web site, Azure PHP tutorial, Azure PHP example" description="В этом учебнике показывается, как создать веб-сайт на PHP и использовать службу хранилища таблиц Azure в серверном приложении." metaCanonical="" services="web-sites,storage" documentationCenter="PHP" title="Create a PHP Website using Azure Storage" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/21/2014" ms.author="tomfitz" />

#Создание веб-сайта на PHP с помощью хранилища Azure

В этом учебнике показывается, как создать веб-сайт на PHP и использовать службу хранилища таблиц Azure в серверном приложении. Предполагается, что на вашем компьютере установлены [PHP][install-php] и веб-сервер. Указания, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Linux и Mac. После завершения работы с этим руководством у вас появится веб-сайт на PHP, работающий в Azure и использующий службу хранилища таблиц.
 
Вы узнаете:

* Как устанавливать клиентские библиотеки Azure и включать их в приложение.
* Как использовать клиентские библиотеки для создания таблиц, а также для создания, запроса и удаления объектов таблиц.
* Как создать учетную запись хранения Azure и настроить приложение для ее использования.
* Как создать веб-сайт Azure и выполнить его развертывание с помощью Git.
 
Вы создадите простое веб-приложение списка задач (Tasklist) на PHP. Снимок экрана завершенного приложения приведен ниже:

![Azure PHP web site][ws-storage-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Установка клиентских библиотек Azure

Чтобы установить клиентские библиотеки PHP для Azure с помощью Composer, выполните следующие действия:

1. [Установите Git][install-git]

	> [WACOM.NOTE]
	> В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.

2. Создайте файл с именем **composer.json** в корневом каталоге своего проекта и добавьте в него следующий код:

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Скачайте **[composer.phar][composer-phar]** в корневой каталог проекта.

4. Откройте командную строку и выполните эту команду в корневом каталоге проекта.

		php composer.phar install

##Начало работы с клиентскими библиотеками

При использовании библиотек перед вызовом интерфейса API Azure нужно выполнить четыре основных шага. Будет создан скрипт инициализации, выполняющий следующие действия.

* Создайте файл с именем **init.php**.

* Во-первых, включите скрипт автозагрузчика.

		require_once 'vendor\autoload.php'; 
	
* Включите пространства имен, которые планируется использовать.

	Чтобы создать клиент службы Azure, необходимо использовать класс **ServicesBuilder**.

		use WindowsAzure\Common\ServicesBuilder;

	Для перехвата исключений, созданных любым вызовом API, понадобится класс **ServiceException**:

		use WindowsAzure\Common\ServiceException;
	
* Для создания экземпляра клиента службы также потребуется правильная строка подключения. Формат строк подключения к службе таблиц:

	Для доступа к службе в режиме реального времени:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Для доступа к хранилищу эмулятора:
	
		UseDevelopmentStorage=true

* Используйте встроенный производителем метод ServicesBuilder::createTableService для создания экземпляра оболочки вокруг вызовов службы таблиц.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` содержит метод для каждого вызова REST, доступного для таблиц Azure.


## Создание таблицы

Прежде чем можно будет хранить данные, нужно создать контейнер для них - таблицу. 

* Создайте файл с именем **createtable.php**.

* Во-первых, включите только что созданный скрипт инициализации. Этот скрипт нужно включать в каждый файл, обращающийся к Azure:

		<?php
		require_once "init.php";

* Затем вызовите метод createTable, передав ему имя таблицы. Аналогично другим хранилищам таблиц, не поддерживающим SQL, схема для таблиц Azure не требуется.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Коды ошибок и соответствующие сообщения см. здесь: [http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx][msdn-errors]


##Запрос таблицы

Главная страница приложения Tasklist должна содержать список всех существующих задач и разрешать вставку новых задач.

* Создайте файл с именем **index.php** и вставьте следующий код HTML и PHP, создающий заголовок страницы:
	
		<html>
		<head>
			<title>Index</title>
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
		<h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
		<?php		
		require_once "init.php";

* Для запроса в таблицах Azure **всех объектов**, хранящихся в таблице задач tasks, вызывается метод queryEntities, в который передается только имя таблицы. Ниже в разделе **Обновление объекта** также будет показано, как передавать фильтр для конкретного объекта.

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* Для перебора всех объектов в наборе результатов поиска:

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* после получения объекта Entity, модель для чтения данных можно будет использовать как Entity->getPropertyValue('[name]'):

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Name</td>
					<td>Category</td>
					<td>Date</td>
					<td>Mark Complete?</td>
					<td>Delete?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>No items on list.</h3>";
		?>

* Наконец, необходимо вставить форму, передающую данные в скрипт вставки задачи, и добавить HTML-код.

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Item Name: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Category: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Date: </td>
						<td><input name="date" type="textbox"/></td>
					</tr>
				</table>
				<input type="submit" value="Add item"/>
			</form>
		</body>
		</html>

## Вставка объектов в таблицу

Теперь приложение может считывать все элементы, хранящиеся в таблице. Так как сначала в списке нет никаких элементов, добавим функцию, записывающую данные в базу данных.

* Создайте файл с именем **additem.php**.

* Добавьте в файл следующий код:

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* Первым шагом при вставке объекта является создание экземпляра объекта Entity и определение его свойств.
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* Затем вы можете передать переменную $entity, которую вы только что создали, в метод insertEntity.

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Наконец, чтобы обеспечить возврат на главную страницу после вставки объекта:

		header('Location: index.php');		
		?>
	
## Обновление сущности

У приложения списка задач есть возможность пометить элемент как выполненный, а также снять для него эту пометку. Главная страница передает свойства RowKey и PartitionKey объекта и его конечное состояние (помеченный == 1, непомеченный == 0).

* Создайте файл с именем **markitem.php** и добавьте часть инициализации:

		<?php		
		require_once "init.php";
		

* Первым действием при передаче объекта является его извлечение из таблицы:
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Как можно видеть, для передаваемого в запрос фильтра используется форма "ключ равен значению" (Key eq 'Value'). Полное описание синтаксиса запроса доступно [здесь][msdn-table-query-syntax].

* Затем можно изменить любые свойства:

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* И метод updateEntity выполняет обновление:

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Чтобы обеспечить возврат на главную страницу после вставки объекта:

		header('Location: index.php');		
		?>


## Удаление объекта

Удаление элемента выполняется с помощью одного вызова deleteItem. Переданными значениями являются **PartitionKey** и **RowKey**, вместе образующие первичный ключ объекта. Создайте файл с именем **deleteitem.php** и вставьте следующий код:

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Создание учетной записи хранения Azure

Для хранения данных приложения в облаке необходимо сначала создать в Azure учетную запись хранения, а затем передать соответствующие данные аутентификации в класс Configuration.

1. Войдите на [портал управления Azure][management-portal].

2. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Create New Azure web site][new-website]

3. Последовательно выберите **Службы данных**, **Хранилище** и **Быстрое создание**.

	![Custom Create a new web site][storage-quick-create]
	
	Введите значение для параметра **URL-адрес** и выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Нажмите кнопку **Создать учетную запись хранения** в нижней части диалогового окна.

	![Fill in web site details][storage-quick-create-details]

	После создания учетной записи хранения появится сообщение **Создание учетной записи хранения "[ИМЯ]" успешно завершено**.

4. Выберите вкладку **Хранилище**, а затем выберите в списке только что созданную учетную запись хранения.

5. Щелкните **Управления ключами** внизу на панели приложения.

	![Select Manage Keys][storage-manage-keys]

6. Запишите имя созданной учетной записи хранения и первичный ключ.

	![Select Manage Keys][storage-access-keys]

7. Откройте **init.php** и замените [YOUR_STORAGE_ACCOUNT_NAME] и [YOUR_STORAGE_ACCOUNT_KEY] именем учетной записи и ключом, записанными на последнем шаге. Сохраните файл.


## Создание веб-сайта Azure и настройка публикации с помощью Git

Выполните следующие действия, чтобы создать веб-сайт Azure.

1. Войдите на [портал управления Azure][management-portal].
2. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Create New Azure Web Site][new-website]

3. Щелкните **Вычисления**, **Веб-сайт**, и **Быстро создать**.

	![Custom Create a new web site][website-quick-create]
	
	Введите значение для параметра **URL-адрес** и выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Нажмите кнопку **Создать новый веб-сайт** в нижней части диалогового окна.

	![Fill in web site details][website-quick-create-details]

	После создания веб-сайта вы увидите текст **Веб-сайт "[ИМЯ_САЙТА]" успешно создан**. Теперь можно включить публикацию Git.

5. В списке веб-сайтов щелкните по имени веб-сайта, чтобы открыть страницу панели управления **БЫСТРЫЙ ЗАПУСК** для этого веб-сайта.

	![Open web site dashboard][go-to-dashboard]


6. В правом нижнем углу страницы быстрого запуска выберите пункт **Настройка развертывания из системы управления версиями**.

	![Set up Git publishing][setup-git-publishing]

6. В ответ на вопрос "Где исходный код?" выберите значение **Локальный репозиторий Git** и щелкните стрелку.

	![where is your source code][where-is-code]

7. Чтобы включить публикацию Git, необходимо предоставить имя пользователя и пароль. Запишите создаваемые имя пользователя и пароль. (Если ранее уже был настроен репозиторий Git, этот шаг будет пропущен.)

	![Create publishing credentials][credentials]

	На настройку репозитория уйдет несколько секунд.

8. После подготовки репозитория Git появятся инструкции по командам Git, которые следует использовать для настройки локального репозитория и передачи файлов в Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

	Запишите инструкции, так как они будут использоваться в следующем разделе для публикации приложения.

##Публикация приложения

Для публикации приложения с помощью Git выполните следующие действия.

1. Откройте папку **vendor/microsoft/windowsazure** в корневом каталоге приложения и удалите следующие файлы и папки:
	* .git
	* .gitattributes
	* .gitignore
			
	Когда диспетчер пакетов Composer загружает клиентские библиотеки Azure и их зависимости, он клонирует для этого репозиторий GitHub, в котором они находятся. На следующем шаге приложение будет развернуто с помощью Git путем создания репозитория из корневой папки приложения. Git будет игнорировать вложенный репозиторий, содержащий клиентские библиотеки, пока не будут удалены специфические файлы репозитория.

2. Откройте GitBash (или терминал, если Git указан в переменной среды PATH), перейдите в корневой каталог приложения и выполните следующие команды (**Примечание.** Эти действия совпадают с действиями, записанными в конце раздела **Создание веб-сайта Azure и настройка публикации с помощью Git**):

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

3. Перейдите по адресу **http://[домен веб-сайта]/createtable.php**, чтобы создать таблицу для приложения.
4. Перейдите по адресу **http://[домен веб-сайта]/index.php**, чтобы начать работу с приложением.

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git. 

##Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1. Внесите изменения в приложение локально.
2. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

3. Перейдите по адресу **http://[домен веб-сайта]/index.php**, чтобы просмотреть внесенные изменения. 

[install-php]: http://www.php.net/manual/en/install.php


[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar

[msdn-errors]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179438.aspx



[msdn-table-query-syntax]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://manage.windowsazure.com
[new-website]: ./media/web-sites-php-storage/new_website.jpg

[website-quick-create]: ./media/web-sites-php-storage/createsite.png
[website-quick-create-details]: ./media/web-sites-php-storage/sitedetails.png
[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-quick-create-details]: ./media/web-sites-php-storage/provideurl.png
[storage-manage-keys]: ./media/web-sites-php-storage/accesskeys.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[go-to-dashboard]: ./media/web-sites-php-storage/selectsite.png
[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
[where-is-code]: ./media/web-sites-php-storage/where_is_code.png

<!--HONumber=35.1-->

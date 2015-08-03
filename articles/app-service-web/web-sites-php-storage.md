<properties 
	pageTitle="Создание веб-приложения PHP в службе приложений Azure с помощью хранилища Azure" 
	description="В этом учебнике показывается, как создать веб-приложение на PHP в службе приложений Azure и использовать службу хранилища таблиц Azure в серверном приложении." 
	services="app-service\web, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="tomfitz"/>

# Создание веб-приложения PHP в службе приложений Azure с помощью хранилища Azure

В этом руководстве показывается, как создать веб-приложение на PHP в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) и использовать службу хранилища таблиц Azure на сервере. Предполагается, что на вашем компьютере установлены [PHP][install-php] и веб-сервер. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, включая Windows, Mac и Linux. После завершения работы с этим руководством у вас будет веб-приложение на PHP, работающее в Azure и использующее службу хранилища таблиц.
 
Вы узнаете:

* Как устанавливать клиентские библиотеки Azure и включать их в приложение.
* Как использовать клиентские библиотеки для создания таблиц, а также для создания, запроса и удаления объектов таблиц.
* Как создать учетную запись хранения Azure и настроить приложение для ее использования.
* Как создать веб-приложение Azure и выполнить его развертывание с помощью Git
 
Вы создадите простое веб-приложение списка задач (Tasklist) на PHP. Снимок экрана завершенного приложения приведен ниже:

![Веб-приложение Azure на PHP][ws-storage-app]

[AZURE.INCLUDE [замечание по созданию учетной записи и веб-сайтов](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

##Установка клиентских библиотек Azure

Чтобы установить клиентские библиотеки PHP для Azure с помощью Composer, выполните следующие действия:

1. [Установите Git][install-git].

	> [AZURE.NOTE]В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.

2. Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:

         {
             "repositories": [
                 {
                     "type": "pear",
                     "url": "http://pear.php.net"
                 }
             ],
             "require": {
                 "pear-pear.php.net/mail_mime" : "*",
                 "pear-pear.php.net/http_request2" : "*",
                 "pear-pear.php.net/mail_mimedecode" : "*",
                 "microsoft/windowsazure": "*"
             }
         }

3. Загрузите **[composer.phar][composer-phar]** в корневой каталог проекта.

4. Откройте командную строку и выполните эту команду в корневом каталоге проекта.

		php composer.phar install

##Начало работы с клиентскими библиотеками

При использовании библиотек перед вызовом интерфейса API Azure нужно выполнить четыре основных шага. Будет создан скрипт инициализации, выполняющий следующие действия.

* Создайте файл с именем **init.php**.

* Во-первых, включите скрипт автозагрузчика.

		require_once 'vendor\autoload.php'; 
	
* Включите пространства имен, которые планируется использовать.

	Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**:

		use WindowsAzure\Common\ServicesBuilder;

	Для перехвата исключений, созданных любым вызовом API, понадобится класс **ServiceException**:

		use WindowsAzure\Common\ServiceException;
	
* Для создания экземпляра клиента службы также потребуется правильная строка подключения. Формат строк подключения к службе таблиц:

	Для доступа к службе в режиме реального времени:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Для доступа к хранилищу эмулятора:
	
		UseDevelopmentStorage=true

* Используйте фабричный метод `ServicesBuilder::createTableService` для создания экземпляра оболочки вокруг вызовов службы таблиц.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` содержит метод для каждого вызова REST, доступного для таблиц Azure.


## Создание таблицы

Прежде чем можно будет хранить данные, нужно создать контейнер для них — таблицу.

* Создайте файл с именем **createtable.php**.

* Во-первых, включите только что созданный скрипт инициализации. Этот скрипт нужно включать в каждый файл, обращающийся к Azure:

		<?php
		require_once "init.php";

* Затем вызовите *createTable*, передавая имя таблицы. Аналогично другим хранилищам таблиц, не поддерживающим SQL, схема для таблиц Azure не требуется.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Коды ошибок и сообщения можно найти тут: [http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]


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

* Для запроса в таблицах Azure **всех объектов**, хранящихся в таблице задач *tasks*, вызывается метод *queryEntities*, в который передается только имя таблицы. Ниже в разделе **Обновление объекта** также будет показано, как передавать фильтр для конкретного объекта.

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

* После получения `Entity` используется модель чтения данных `Entity->getPropertyValue('[name]')`:

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

* Первым шагом при вставке объекта является создание экземпляра объекта `Entity` и задание его свойств.
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* Затем можно передать только что созданный объект `$entity` в метод `insertEntity`.

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

У приложения списка задач есть возможность пометить элемент как выполненный, а также снять для него эту пометку. Главная страница передает свойства *RowKey* и *PartitionKey* объекта и его конечное состояние (помеченный == 1, непомеченный == 0).

* Создайте файл с именем **markitem.php** и добавьте часть инициализации:

		<?php		
		require_once "init.php";
		

* Первым действием при передаче объекта является его извлечение из таблицы:
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq ''.$_GET['pk'].'' and RowKey eq ''.$_GET['rk'].''');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Как вы видите, передаваемое в фильтре запроса имеет форму `Key eq 'Value'`. Полное описание синтаксиса запроса доступно [здесь][msdn-table-query-syntax].

* Затем можно изменить любые свойства:

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* После этого метод `updateEntity` выполняет обновление:

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

Удаление элемента выполняется с помощью одного вызова `deleteItem`. Переданными значениями являются **PartitionKey** и **RowKey**, вместе образующие первичный ключ объекта. Создайте файл с именем **deleteitem.php** и вставьте следующий код:

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Создание учетной записи хранения Azure

Для хранения данных приложения в облаке необходимо сначала создать в Azure учетную запись хранения, а затем передать соответствующие данные проверки подлинности в класс *Configuration*.

1. Войдите на [портал Azure][management-portal].

2. Щелкните значок **Создать** в левом нижнем углу страницы, затем выберите **Данные + Хранилище** > **Хранилище**. Присвойте учетной записи хранения уникальное имя и создайте для нее новую [группу ресурсов](../resource-group-overview.md).

	![Создание новой учетной записи хранения][storage-quick-create]
	
	После создания учетной записи хранения на кнопке **Уведомления**загорится зеленым слово **УСПЕШНО** и откроется колонка учетной записи хранения, в которой будет видно, что учетная запись относится к созданной вами группе ресурсов.

5. Щелкните часть **Настройки** в колонке учетной записи хранения. Запишите имя учетной записи и первичный ключ.

	![Выбор управления ключами][storage-access-keys]

7. Откройте файл **init.php** и замените `[YOUR_STORAGE_ACCOUNT_NAME]` и `[YOUR_STORAGE_ACCOUNT_KEY]` именем учетной записи и ключом, записанными на последнем шаге. Сохраните файл .

## Создание веб-приложения Azure и настройка публикации в Git

Выполните следующие действия, чтобы создать веб-приложение Azure:

1. Войдите на [портал Azure][management-portal].

2. Создайте пустое веб-приложение, используя инструкции из статьи [Создание веб-приложения WordPress в службе приложений Azure](../web-sites-create-deploy.md#createawebsiteportal). Не забудьте создать новый [план службы приложений](azure-web-sites-web-hosting-plans-in-depth-overview) и выбрать группу ресурсов, которую вы создали ранее для учетной записи хранения.

	После создания веб-приложения на кнопке **Уведомления** будет мигать зеленым слово **УСПЕШНО** и откроется колонка веб-приложения, в которой будет видно, что веб-приложение принадлежит созданной вами группе ресурсов.

6. В колонке веб-приложения щелкните **Настройка непрерывного развертывания** и выберите **Локальный репозиторий Git**. Нажмите кнопку **ОК**.

	![Настройка публикации Git][setup-git-publishing]

7. Чтобы развернуть локальный репозиторий Git в Azure, сначала настройте учетные данные развертывания. Чтобы настроить учетные данные, в колонке веб-приложения щелкните **Все настройки** > **Учетные данные развертывания**. После завершения настройки нажмите **Сохранить**.

	![Создание учетных данных для публикации][credentials]

	На настройку репозитория уйдет несколько секунд.

8. Когда репозиторий Git будет готов, вы сможете фиксировать свои изменения в нем. Чтобы найти URL-адрес репозитория, щелкните ту же часть развертывания в колонке веб-приложений.

	![Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-приложения.][git-instructions]

	Запишите инструкции, так как они будут использоваться в следующем разделе для публикации приложения.

##Публикация приложения

Для публикации приложения с помощью Git выполните следующие действия.

1. Откройте папку **vendor/microsoft/windowsazure** в корневом каталоге приложения и удалите следующие файлы и папки:
	* .git
	* .gitattributes
	* .gitignore
			
	Загрузку клиентских библиотек Azure и их зависимостей диспетчер пакетов Composer осуществляет путем клонирования репозитория GitHub, в котором они располагаются. На следующем шаге приложение будет развернуто с помощью Git путем создания репозитория из корневой папки приложения. Git будет игнорировать вложенный репозиторий, содержащий клиентские библиотеки, пока не будут удалены специфические файлы репозитория.

2. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), измените каталог на корневой каталог приложения и выполните следующие команды:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

3. Перейдите по адресу **http://[your домен веб-приложения]/createtable.php**, чтобы создать таблицу для приложения.
4. Перейдите по адресу **http://[your домен веб-приложения]/index.php**, чтобы начать работу с приложением.

После публикации приложения можно начать вносить изменения и публиковать их с помощью Git.

##Публикация изменений в приложении

Чтобы опубликовать изменения в приложение, выполните следующие действия.

1. Внесите изменения в приложение локально.
2. Откройте GitBash (или терминал, если Git включен в переменную `PATH`), переключитесь на корневой каталог приложения и выполните следующие команды:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

3. Перейдите по адресу **http://[your домен веб-приложения]/index.php**, чтобы просмотреть внесенные изменения.

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Информацию о смене старого портала на новый см. в [справочнике по навигации на портале](http://go.microsoft.com/fwlink/?LinkId=529715).




[install-php]: http://www.php.net/manual/en/install.php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx

[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://portal.azure.com

[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png

[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
 

<!---HONumber=July15_HO4-->
<properties 
	pageTitle="Создание и развертывание веб-приложения PHP-SQL в службе приложений Azure с помощью WebMatrix" 
	description="В этом учебнике показано, как использовать бесплатную интегрированную среду разработки WebMatrix для создания и развертывания веб-приложения на PHP в службе приложений Azure, которое хранит данные в базе данных SQL." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>





# Создание и развертывание веб-приложения PHP-SQL в службе приложений Azure с помощью WebMatrix

В этом учебном руководстве показано, как с помощью WebMatrix разработать приложение PHP, которое использует базу данных SQL Azure, и развернуть его в веб-приложениях [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-приложений. WebMatrix поддерживает PHP, а также intellisense для разработки PHP.

В этом учебном курсе предполагается, что на компьютере установлен [SQL Server Express][install-SQLExpress], что позволит тестировать приложение локально. Тем не менее, этот учебный курс можно пройти без установленного сервера SQL Server Express. Вместо этого можно развернуть приложение непосредственно в веб-приложениях службы приложений Azure.

После завершения работы с этим учебным курсом у вас будет веб-приложение базы данных PHP-SQL в Azure.
 
Вы узнаете:

* Как создать веб-приложение и базу данных SQL в веб-приложениях службы приложений с помощью [предварительной версии портала Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Так как язык PHP включен в веб-приложениях по умолчанию, никаких дополнительных действий для выполнения вашего PHP-кода не требуется.
* Как разработать приложение PHP с использованием WebMatrix.
* Как опубликовать и повторно опубликовать приложение в Azure с помощью WebMatrix.
 
Руководствуясь этим учебником, вы создадите простое веб-приложение списка задач Tasklist на PHP. Это приложение будет размещено в веб-приложениях службы приложений. Снимок экрана работающего приложения приведен ниже:

![Веб-сайт Azure на PHP][running-app]

[AZURE.INCLUDE [замечание по созданию учетной записи и веб-сайтов](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

##Предварительные требования

1. [Загрузите][tasklist-sqlazure-download] файлы приложения Tasklist. Приложение Tasklist представляет простое приложение на PHP, которое позволяет добавлять элементы в список задач, отмечать их как выполненные, а также удалять их из списка. Элементы списка задач хранятся в базе данных SQL (SQL Server Express для локального тестирования). Приложение состоит из следующих файлов:

	* **index.php** — отображение задач и предоставление формы для добавления элемента в список.
	* **additem.php** — добавление элемента в список.
	* **getitems.php** — получение всех имеющихся в базе данных элементов.
	* **markitemcomplete.php** — изменение состояния элемента на выполненное.
	* **deleteitem.php** — удаление элемента.
	* **taskmodel.php** — функции для добавления, получения, обновления элементов и их удаления из базы данных.
	* **createtable.php**: создание таблицы базы данных SQL для приложения. Этот файл будет использоваться только один раз.

2. Создайте базу данных SQL Server под названием `tasklist`. Это можно сделать в командной строке `sqlcmd` с помощью следующих команд:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Этот шаг необходим только в том случае, если нужно протестировать приложение локально.

## Создание веб-приложения и базы данных SQL

Для создания веб-приложения Azure и базы данных SQL выполните следующие действия.

1. Выполните вход на [портал предварительной версии Azure](https://portal.azure.com).

2. Откройте Azure Marketplace. Для этого щелкните значок **Marketplace** или в левом нижнем углу панели мониторинга щелкните **Создать**, выберите **Веб + мобильное**, а затем внизу окна **Azure Marketplace**.
	
3. В окне Marketplace выберите **Веб-приложения**.

4. Щелкните значок **Веб-приложение + SQL**.

5. Прочитав описание "Веб-приложение + SQL", нажмите **Создать**.

6. Поочередно выберите вкладки (**Группа ресурсов**, **Веб-приложение**, **База данных** и **Подписка**) и на каждой из них заполните обязательные поля.
	
	- Введите URL-адрес по своему выбору.	
	- Настройте учетные данные для сервера базы данных.
	- Выберите ближайший к вам регион.

	![настройка приложения](./media/web-sites-php-sql-database-use-webmatrix/configure-db-settings.png)

7. Определив параметры веб-приложения, нажмите кнопку **Создать**.

	После создания веб-приложения на кнопке **Уведомления** загорится зеленым слово **УСПЕШНО** и откроется колонка группы ресурсов, в которой будет видно, что и веб-приложение, и база данных SQL находятся в группе.

4. Щелкните значок веб-приложения в колонке группы ресурсов, чтобы открыть колонку веб-приложения.

	![группа ресурсов веб-приложений](./media/web-sites-php-sql-database-use-webmatrix/resource-group-blade.png)

##Получение сведений о подключении к базе данных SQL

Чтобы подключиться к экземпляру базы данных SQL, связанной с вашим веб-приложением, вам понадобятся сведения о подключении, указанные во время создания базы данных. Чтобы получить информацию о подключении к базе данных SQL, выполните следующие действия.

1. Вернитесь в колонку группы ресурсов и щелкните значок базы данных SQL.

2. В колонке базы данных SQL щелкните **Свойства**, а затем щелкните **Показать строки подключения базы данных**.

	![Просмотр свойств базы данных](./media/web-sites-php-sql-database-use-webmatrix/view-database-properties.png)
	
3. В разделе **PHP** открывшегося диалогового окна найдите значения `Server`, `SQL Database` и `User Name` и запишите их. Эти значения понадобятся позднее для публикации веб-приложения PHP в службу приложений Azure.

## Создание приложения в WebMatrix

В следующих шагах вам предстоит разработать приложение Tasklist, добавив загруженные ранее файлы и внеся некоторые изменения. Тем не менее, можно добавить собственные существующие файлы или создать новые файлы.

1. Запустите [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Если вы еще не установили этот компонент, сделайте это сейчас.
2. Если это первый случай использования WebMatrix 3, будет выведен запрос на вход в Azure. В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись** Выберите **Вход** с использованием учетной записи Майкрософт.

	![Добавление учетной записи](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-add-account.png)

3. Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт.

	![Вход в Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-sign-in.png)

1. На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов.

	![Новый сайт из коллекции шаблонов](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template.png)

4. Выберите в списке доступных шаблонов **PHP**.

	![Веб-сайт на основе шаблона][site-from-template]

5. Выберите шаблон **Пустой сайт**. Укажите имя сайта и нажмите кнопку **ДАЛЕЕ**.

	![Ввод имени сайта][site-from-template-2]

3. После входа в Azure можно будет создать экземпляр веб-приложений службы приложений Azure для локального сайта. Пока что выберите **Пропустить**.

	![Создание сайта в Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template-azure.png)

1. По завершении создания локального сайта в WebMatrix откроется интегрированная среда разработки WebMatrix. Добавьте файлы приложения, нажав кнопку **Добавить существующий**:

	![WebMatrix — добавить существующие файлы][edit_addexisting]

	В открывшемся диалоговом окне перейдите к загруженным ранее файлам, выделите их все и нажмите кнопку "Открыть". В ответ на запрос выберите замену файла `index.php`.

2. Далее необходимо добавить сведения о подключении к локальной базе данных SQL Server в файл `taskmodel.php`. Откройте файл `taskmodel.php`, дважды щелкнув его, и обновите сведения о подключении базы данных в функции `connect`. (**Примечание**. Перейдите к странице [Публикация приложения](#Publish), если не требуется тестировать приложение локально, а вместо этого нужно опубликовать его непосредственно в веб-приложения службы приложений Azure.)

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Сохраните файл `taskmodel.php`.

3. Чтобы запустить приложение, необходимо создать таблицу `items`. Щелкните правой кнопкой мыши файл `createtable.php` и выберите **Запустить в браузере**. Команда запустит `createtable.php` в браузере и выполнит код, который создает таблицу `items` в базе данных `tasklist`.

	![WebMatrix — запуск файла createtable.php в браузере][edit_run]

4. Теперь можно перейти к локальному тестированию приложения. Щелкните правой кнопкой мыши файл `index.php` и выберите **Запустить в браузере**. Протестируйте приложение, добавляя элементы, отмечая их как выполненные и удаляя их.

<a id="Publish"></a>
## Публикация приложения

Перед публикацией приложения в веб-приложениях службы приложений Azure сведения о подключении к базе данных из `taskmodel.php` необходимо обновить, указав сведения о подключении, полученные ранее (в разделе [Создание веб-приложения и базы данных SQL](#CreateWebsite)).

1. Откройте файл `taskmodel.php`, дважды щелкнув его, а затем обновите сведения о подключении к базе данных в функции `connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web app";
		$db = "value of Database";
	
	Сохраните файл `taskmodel.php`.

2. В WebMatrix щелкните **Опубликовать**.

	![WebMatrix — опубликовать][edit_publish]

3. Щелкните **Выбрать существующий сайт Microsoft Azure**.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. Выберите созданное ранее веб-приложение службы приложений.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Нажимайте **Продолжить**, пока WebMatrix публикует сайт в веб-приложения службы приложений Azure.

3. Перейдите по адресу http://[your имя веб-сайта].azurewebsites.net/createtable.php, чтобы создать таблицу `items`.

4. Наконец, перейдите по адресу http://[your имя вашего веб-сайта].azurewebsites.net/index.php, чтобы запустить приложение.
	
##Изменение и повторная публикация приложения

Можно легко изменить приложение путем изменения локальной копии сайта и его повторной публикации либо путем редактирования непосредственно в **удаленном** режиме. Внесите здесь простое изменение в заголовок из файла `index.php` и сохраните его непосредственно в работающее веб-приложение службы приложений.

1. Перейдите на вкладку **Удаленный режим** своего сайта в WebMatrix и выберите команду **Открыть удаленное представление**. Откроется непосредственное редактирование удаленных файлов (размещенных в веб-приложениях). ![WebMatrix — открыть удаленное представление][OpenRemoteView]
 
2. Откройте файл `index.php`, дважды щелкнув его. ![WebMatrix — открыть файл index][Remote_editIndex]

3. Измените **Мой список дел** на **Мой список задач** в тегах **title** и **h1**, а затем сохраните файл.


4. После сохранения нажмите кнопку "Запустить", чтобы увидеть изменения на действующем веб-приложении службы приложений. ![WebMatrix — запуск сайта в удаленном режиме][Remote_run]



## Дальнейшие действия

Было показано, как создать и развернуть веб-приложение из WebMatrix в веб-приложения службы приложений Azure. Дополнительные сведения о WebMatrix см. в разделе [Веб-сайт WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398).

## Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
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








 

<!---HONumber=62-->
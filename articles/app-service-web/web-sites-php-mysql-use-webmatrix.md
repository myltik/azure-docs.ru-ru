<properties 
	pageTitle="Создание и развертывание веб-приложения PHP-MySQL в службе приложений Azure с помощью WebMatrix" 
	description="В этом учебнике показано, как использовать бесплатную интегрированную среду разработки WebMatrix для создания и развертывания веб-приложения на PHP в службе приложений Azure, которое хранит данные в MySQL."
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="tomfitz"/>





# Создание и развертывание веб-приложения PHP-MySQL в службе приложений Azure с помощью WebMatrix

В этом учебнике показано, как создать приложение PHP-MySQL и развернуть его в веб-приложениях [службы приложений Azure ](http://go.microsoft.com/fwlink/?LinkId=529714) с помощью WebMatrix. WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-сайта. WebMatrix поддерживает PHP, а также intellisense для разработки PHP.

В этом учебном курсе предполагается, что на компьютере установлен [MySQL][install-mysql], что позволит тестировать приложение локально. Тем не менее, этот учебный курс можно пройти без установленного MySQL. Вместо этого можно развернуть приложение непосредственно в веб-приложениях службы приложений Azure.

После завершения работы с этим учебником в веб-приложениях будет работать веб-сайт PHP-MySQL.
 
Вы узнаете:

* Как создать веб-сайт и базу данных MySQL в веб-приложениях службы приложений с помощью [портала Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Так как язык PHP включен в веб-приложениях по умолчанию, никаких дополнительных действий для выполнения вашего PHP-кода не требуется.
* Как разработать приложение PHP с использованием WebMatrix.
* Как опубликовать и повторно опубликовать приложение в веб-приложения с помощью WebMatrix.
 
Руководствуясь этим учебником, вы создадите простое веб-приложение списка задач Tasklist на PHP. Это приложение будет размещено в веб-приложениях службы приложений. Снимок экрана работающего приложения приведен ниже:

![Веб-сайт Azure на PHP][running-app]

[AZURE.INCLUDE [замечание по созданию учетной записи и веб-сайтов](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

##Предварительные требования

1. [Загрузите][tasklist-mysql-download] файлы приложения Tasklist. Приложение Tasklist представляет простое приложение на PHP, которое позволяет добавлять элементы в список задач, отмечать их как выполненные, а также удалять их из списка. Элементы списка задач хранятся в базе данных MySQL. Приложение состоит из следующих файлов:

	* **additem.php** — добавление элемента в список.
	* **createtable.php** — создание таблицы базы данных MySQL для приложения. Этот файл будет использоваться только один раз.
	* **deleteitem.php** — удаление элемента.
	* **getitems.php** — получение всех элементов в базе данных.
	* **index.php** — отображение задач и предоставление формы для добавления элемента в список.
	* **markitemcomplete.php** — изменение состояния элемента на выполненное.
	* **taskmodel.php** — функции для добавления, получения, обновления элементов и их удаления из базы данных.

1. Создание локальной базы данных MySQL с именем `tasklist`. Это можно сделать из рабочей области базы данных WebMatrix (после установки, как описано далее в учебнике) или из командной строки MySQL с помощью этой команды:

		mysql> create database tasklist;

	Этот шаг необходим только в том случае, если нужно протестировать приложение локально.

## Создание веб-приложения и базы данных MySQL

Чтобы создать веб-приложение и базу данных MySQL, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).

2. Щелкните значок **Создать** в нижнем левом углу портала.

	![Создание нового веб-сайта Azure](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. Щелкните **Интернет + мобильные устройства**, а затем **Веб-приложение + MySQL**.

	![Настраиваемое создание нового веб-сайта](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. Укажите действительное имя группы ресурсов.

    ![Задание имени группы ресурсов](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. Введите значения для нового веб-приложения.

    ![Создание веб-приложения](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. Введите значения для своей новой базы данных и примите условия.

	![Создание новой базы данных MySQL](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	Когда веб-приложение будет создано, вы увидите новую группу ресурсов.

## Получение информации об удаленном подключении к MySQL

Чтобы подключиться к базе данных MySQL, которая работает в веб-приложениях, вам потребуются сведения о подключении. Чтобы получить сведения о подключении к MySQL, выполните следующие действия.

1. В группе ресурсов щелкните базу данных.

	![Выбор базы данных](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. В сводке базы данных выберите **Свойства**.

    ![Выбор свойств](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. Запишите значения `Database`, `Host`, `User Id` и `Password`.

    ![Запись свойств](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## Создание приложения в WebMatrix

В следующих шагах вам предстоит разработать приложение Tasklist, добавив загруженные ранее файлы и внеся некоторые изменения. Тем не менее, можно добавить собственные существующие файлы или создать новые файлы.

1. Запустите [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Если вы еще не установили этот компонент, сделайте это сейчас.
2. Если это первый случай использования WebMatrix 3, будет выведен запрос на вход в Azure. В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись** Выберите **Вход** с использованием учетной записи Майкрософт.

	![Добавление учетной записи](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт.

	![Вход в Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов.

	![Новый сайт из коллекции шаблонов](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. Выберите в списке доступных шаблонов **PHP**.

	![Веб-сайт на основе шаблона][site-from-template]

5. Выберите шаблон **Пустой сайт**. Укажите имя сайта и нажмите кнопку **ДАЛЕЕ**.

	![Ввод имени сайта][site-from-template-2]

	Веб-сайт будет открыт в WebMatrix с некоторыми файлами по умолчанию.

	В следующих шагах вам предстоит разработать приложение Tasklist, добавив загруженные ранее файлы и внеся некоторые изменения. Тем не менее, можно добавить собственные существующие файлы или создать новые файлы.

6. Добавьте файлы приложения, нажав кнопку **Добавить существующий**:

	![WebMatrix — добавить существующие файлы][edit_addexisting]

	В открывшемся диалоговом окне перейдите к загруженным ранее файлам, выделите их все и нажмите кнопку "Открыть". В ответ на запрос выберите замену файла `index.php`.

7. Далее необходимо добавить сведения о подключении к локальной базе данных MySQL в файл `taskmodel.php`. Откройте файл `taskmodel.php`, дважды щелкнув его, и обновите сведения о подключении к базе данных в функции `connect`. (**Примечание**. Перейдите к странице [Публикация приложения](#Publish), если не требуется тестировать приложение локально, а вместо этого нужно опубликовать его непосредственно в веб-приложения службы приложений Azure.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Сохраните файл `taskmodel.php`.

8. Чтобы запустить приложение, необходимо создать таблицу `items`. Щелкните правой кнопкой мыши файл `createtable.php` и выберите **Запустить в браузере**. Команда запустит `createtable.php` в браузере и выполнит код, который создает таблицу `items` в базе данных `tasklist`.

	![WebMatrix — запуск файла createtable.php в браузере][edit_run]

9. Теперь можно перейти к локальному тестированию приложения. Щелкните правой кнопкой мыши файл `index.php` и выберите **Запустить в браузере**. Протестируйте приложение, добавляя элементы, отмечая их как выполненные и удаляя их.


## Публикация приложения

Перед публикацией приложения в веб-приложениях службы приложений Azure сведения о подключении базы данных из `taskmodel.php` необходимо обновить, указав сведения о подключении, полученные ранее (в разделе [Создание веб-приложения и базы данных MySQL](#CreateWebsite)).

1. Откройте файл `taskmodel.php`, дважды щелкнув его, а затем обновите сведения о подключении к базе данных в функции `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Сохраните файл `taskmodel.php`.

2. В WebMatrix щелкните **Опубликовать**.

	![WebMatrix — опубликовать][edit_publish]

3. Щелкните **Выбрать существующий сайт из Microsoft Azure**.

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site.png)

3. Выберите созданное ранее веб-приложение службы приложений.

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Нажимайте **Продолжить**, пока WebMatrix публикует сайт в веб-приложения службы приложений Azure.

3. Перейдите по адресу http://[your имя веб-сайта].azurewebsites.net/createtable.php, чтобы создать таблицу `items`.

4. Наконец, перейдите по адресу http://[your имя веб-сайта].azurewebsites.net/index.php, чтобы использовать приложение.
	
##Изменение и повторная публикация приложения

Можно с легкостью изменить приложение путем изменения загруженной ранее локальной копии сайта и его повторной публикации либо путем редактирования непосредственно в удаленном режиме. Внесите здесь простое изменение в заголовок файла `index.php` и сохраните его непосредственно на работающем сайте.

1. Перейдите на вкладку «Удаленный режим» своего веб-сайта в WebMatrix и выберите команду **Открыть удаленное представление**. Это действие сделает доступным непосредственное редактирование удаленного веб-сайта. ![WebMatrix — открыть удаленное представление][OpenRemoteView]
 
2. Откройте файл `index.php`, дважды щелкнув его. ![WebMatrix — открыть файл index][Remote_editIndex]

3. Измените **Мой список дел** на **Мой список задач** в тегах **title** и **h1**, а затем сохраните файл.


4. После сохранения нажмите кнопку "Запустить", чтобы увидеть изменения на действующем сайте. ![WebMatrix — запуск сайта в удаленном режиме][Remote_run]


## Дальнейшие действия

* [Веб-сайт WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).




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













 

<!---HONumber=62-->
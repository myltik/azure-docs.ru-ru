<properties urlDisplayName="Command Line Administration" pageTitle="Администрирование мобильной службы с помощью командной строки: учебник по Azure" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Автоматизация мобильных служб с помощью средств командной строки 

В этом разделе показано, как использовать средства командной строки Azure для автоматизации создания мобильных служб Azure и управления ими. В этом разделе показано, как установить и начать использовать средства командной строки для выполнения следующих задач мобильных служб:

-	[Создание мобильной службы] 
-	[Создание новой таблицы]
-   [Регистрация сценария для операций с таблицами][Register a new table script]
-   [Перечисление таблиц]
- 	[Удаление существующей таблицы]
-	[Перечисление мобильных служб]
-   [Удаление существующей мобильной службы]
 
При объединении в один скрипт или пакетный файл эти отдельные команды автоматизируют процесс создания, проверки и удаления мобильной службы. 

Чтобы использовать средства командной строки Azure для управления мобильными службами, требуется учетная запись Azure, для которой включены мобильные службы Azure.

+ Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительную информацию см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/" target="_blank">Бесплатное пробное использование Azure</a>.

+ Если у вас есть учетная запись, но вам нужно включить предварительную версию мобильных служб Azure, см. раздел <a href="http://azure.microsoft.com/ru-ru/documentation/articles/php-create-account/#enable" target="_blank">Включение предварительных версий компонентов Azure</a>.

В этом разделе описывается набор общих задач администрирования, поддерживаемых средствами командной строки Azure. Дополнительную информацию см. в [документации программ командной строки Azure][reference-docs].

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic. 

+ (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

<h2><a name="install"></a>Установка программ командной строки Azure</h2>

Следующий список содержит сведения по установке программ командной строки в зависимости от операционной системы:

* **Windows**: скачайте [установщик программ командной строки Azure][windows-installer]. Откройте загруженный MSI-файл и выполните установку.

* **Mac**: скачайте [установщик пакета SDK для Azure][mac-installer]. Откройте загруженный файл .pkg и выполните установку.

* **Linux**: установите последнюю версию [Node.js][nodejs-org] - см. статью [Install Node.js via Package Manager][install-node-linux] (Установка Node.js через диспетчер пакетов - затем выполните следующую команду:

		npm install azure-cli -g

Чтобы проверить правильность установки, введите "azure" в командной строке. Если установка прошла успешно, вы увидите список всех доступных команд "azure".
<h2><a name="import-account"></a>Загрузка и импорт параметров публикации</h2>

Чтобы начать работу, необходимо сначала загрузить и импортировать параметры публикации. Это позволит вам использовать средства для создания и управления службами Azure. Чтобы загрузить параметры публикации, используйте команду "account download":

		azure account download

После этого откроется браузер по умолчанию и появится запрос на вход в портал управления. После входа будет скачан PUBLISHSETTINGS-файл. Запишите расположение сохраненного файла.

Затем импортируйте PUBLISHSETTINGS-файл, выполнив следующую команду, заменив <path-to-settings-file> на путь к вашему PUBLISHSETTINGS-файл:

		azure account import <path-to-settings-file>

Всю информацию, сохраненную с помощью команды <code>импорт</code> можно удалить с помощью команды <code>account clear</code> .

		azure account clear

Чтобы просмотреть список параметров для команд "account", используйте параметр "-help":

		azure account -help

После импорта настроек публикации следует удалить PUBLISHSETTINGS-файл по соображениям безопасности. Дополнительную информацию см. в статье [Установка программ командной строки Azure для Mac и Linux]. Теперь вы можете приступить к созданию мобильных служб Azure и начать управлять ими с помощью командной строки или пакетных файлов.  

<h2><a name="create-service"></a>Как создать мобильную службу</h2>

Средства командной строки можно использовать для создания нового экземпляра мобильной службы. При создании мобильной службы также создается экземпляр базы данных SQL на новом сервере. 

Следующая команда создает новый экземпляр мобильной службы в вашей подписке, где <service-name> - имя новой мобильной службы, <server-admin> - имя входа нового сервера, а <server-password> - пароль для нового имени входа:

		azure mobile create <service-name> <server-admin> <server-password>

Команда "mobile create" завершается ошибкой, если указанная мобильная служба существует. В скриптах автоматизации следует попытаться удалить мобильную служб, прежде чем попытаться создать ее еще раз.

<h2><a name="list-services"></a>Перечисление существующих мобильных служб в подписке</h2>

Следующая команда возвращает список всех мобильных служб в подписке Azure:

		azure mobile list

Эта команда также показывает текущее состояние и URL-адрес каждой мобильной службы.

<h2><a name="delete-service"></a>Как удалить существующую мобильную службу</h2>

Средства командной строки можно использовать для удаления существующей мобильной службы вместе со связанной базой данных SQL и сервером. Следующая команда удаляет мобильную службу, где <service-name> - имя мобильной службы, которую требуется удалить:

		azure mobile delete <service-name> -a -q

Если добавить параметры "-a" и "-q", эта команда также удалит базу данных SQL и сервер, используемые мобильной службой, без отображения запроса.

<div class="dev-callout"><strong>Примечание.</strong> 
   <p>Если вы не укажете параметр <code>-q</code> вместе с параметром <code>-a</code> или <code>-d</code>, выполнение будет приостановлено и отобразится запрос для выбора параметров удаления базы данных SQL. Используйте только параметр <code>-a</code> если другие службы не используют базу данных или сервер. В противном случае используйте параметр <code>-d</code> , чтобы удалить только те данные, которые принадлежат удаляемой мобильной службе.</p>
</div>

<h2><a name="create-table"></a>Как создать таблицу в мобильной службе</h2>

Следующая команда создает таблицу в указанной мобильной службе, где <service-name> - имя мобильной службы, а<table-name>- это имя создаваемой таблицы:

		azure mobile table create <service-name> <table-name>

Эта команда создает новую таблицу с установленными по умолчанию разрешениями, "application", для операций с таблицей: insert, read, update и delete. 

Следующая команда создает новую таблицу с открытым разрешением на чтение (read), но разрешение на удаление (delete) предоставляется только администраторам:

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

В следующей таблице показано значение разрешения сценария в сравнении со значением разрешения на [портале управления Azure].

<table border="1" width="100%"><tr><th>Значение сценария</th><th>Значение портала управления</th></tr>
<tr><td><code>public</code></td><td>Все</td></tr>
<tr><td><code>в приложении</code> (по умолчанию)</td><td>Все с ключом приложения</td></tr>
<tr><td><code>user</code></td><td>Только прошедшие проверку пользователи</td></tr>
<tr><td><code>admin	</code></td><td>Только скрипты и администраторы</td></tr></table>

Команда "mobile table create" завершается ошибкой, если указанная таблица уже существует. В скриптах автоматизации следует попытаться удалить таблицу, прежде чем попытаться создать ее еще раз.

<h2><a name="list-tables"></a>Перечисление существующих таблиц в мобильной службе</h2>

Следующая команда возвращает список всех таблиц в мобильной службе, где <service-name> - имя мобильной службы:

		azure mobile table list <service-name>

Эта команда также показывает число индексов в каждой таблице и число строк данных в таблице на текущий момент.

<h2><a name="delete-table"></a>Как удалить существующую таблицу из мобильной службы</h2>

Следующая команда удаляет таблицу из указанной мобильной службы, где <service-name> - имя мобильной службы, а<table-name>- это имя удаляемой таблицы:

		azure mobile table delete <service-name> <table-name> -q

В сценариях автоматизации используйте параметр "-q" для удаления таблицы без отображения запроса подтверждения, который блокирует выполнение.

<h2><a name="register-script"></a>Как зарегистрировать сценарий для операций с таблицами</h2>

Следующая команда передает и регистрирует функцию для операции с таблицей, где <service-name> - имя мобильной службы,<table-name>- имя таблицы, а <operation> - операция с таблицей (read, insert, update или delete):

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

Обратите внимание, что эта операция загружает файл JavaScript (JS) с локального компьютера. Имя файла должно состоять из имен таблицы и операции, а сам файл должен находиться во вложенной папке "table" относительно расположения, где выполняется команда. Например, следующая операция передает и регистрирует новый сценарий "insert", который принадлежит таблице TodoItems:

		azure mobile script upload todolist table/todoitems.insert.js

Объявление функции в файле скрипта также должно соответствовать зарегистрированной табличной операции. Это значит, что для сценария "insert" переданный сценарий содержит функцию со следующей сигнатурой:

		function insert(item, user, request) {
		    ...
		} 

Дополнительную информацию о регистрации сценариев см. в [справочнике по серверным сценариям мобильных служб].

<!--<h2><a name="test-service"></a>Test the new mobile service</h2>

When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to 

## <a name="nextsteps"> </a>Next Steps
Next steps here....
-->
<!-- Anchors. -->
[Закачка и установка программ командной строки]: #install
[Закачка и импорт параметров публикации]: #import
[Создание мобильной службы]: #create-service
[Получение основного ключа]: #get-master-key
[Создание новой таблицы]: #create-table
[Регистрация нового табличного сценария]: #register-script
[Удаление существующей таблицы]: #delete-table
[Удаление существующей мобильной службы]: #delete-service
[Тестирование мобильной службы]: #test-service
[Перечисление мобильных служб]: #list-services
[Перечисление таблиц]: #list-tables
[Дальнейшие действия]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Портал управления Azure]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /ru-ru/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Установка программ командной строки Azure для Mac и Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795


<!--HONumber=35_1-->

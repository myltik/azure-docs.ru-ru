<properties linkid="web-sites-python-ptvs-flask-mongodb" title="Flask and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask и MongoDB на Azure с использованием средств Python 2.1 для Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask и MongoDB на Azure с использованием средств Python 2.1 для Visual Studio

В этом учебнике мы создадим простое приложение для опроса, используя один из образцов шаблонов расширения PTVS (Python Tools для Visual Studio).

Приложение для опросов определяет абстракцию для своего репозитория, так что вы сможете легко переключаться между различными типами репозиториев (In-Memory, хранилище таблиц Azure, MongoDB).

Вы узнаете, как использовать одну из служб MongoDB, размещенных в Azure, как настроить приложение для работы с MongoDB, а затем опубликовать приложение на веб-сайте Azure.

Чтобы ознакомиться с другими статьями, посвященными разработке веб-сайтов Azure с PTVS с помощью веб-платформ Bottle, Flask и Django, а также служб MongoDB, хранилища таблиц Azure, MySQL и SQL Database, см. [Центр разработчика Python][].  Хотя данная статья посвящена веб-сайтам Azure, описываемые действия схожи с процедурой разработки [Облачных служб Azure][].

+ [Предварительные требования](#prerequisites)
+ [Создание проекта](#create-the-project)
+ [Создание базы данных MongoDB](#create-a-mongodb-database)
+ [Настройка проекта](#configure-the-project)
+ [Просмотр базы данных MongoDB](#explore-the-mongodb-database)
+ [Публикация на веб-сайте Azure](#publish-to-an-azure-website)
+ [Настройка веб-сайта Azure](#configure-the-azure-website)
+ [Дальнейшие действия](#next-steps)

##<a name="prerequisites"></a>Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Пакет инструментов Azure SDK для VS 2013][] или [Пакет инструментов Azure SDK для VS 2012][]
 - [Python 2.7, 32-разрядная версия][] или [Python 3.4, разрядная версия][]
 - [RoboMongo][] (дополнительно)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Создание проекта

В этом разделе мы создадим проект Visual Studio, используя образец шаблона.  Мы создадим виртуальную среду и установим необходимые пакеты.  Затем запустите приложение локально, используя стандартный репозиторий, хранимый в памяти.

1.  В Visual Studio выберите **Файл**, **Новый проект**. 

1.  Шаблоны из PTVS Samples VSIX находятся здесь: **Python**, **Примеры**.  Чтобы создать проект, выберите **Веб проект для опросов Flask** и нажмите кнопку "ОК".

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Выберите **Установить в виртуальной среде**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  В качестве базового интерпретатора выберите **Python 2.7** или **Python 3.4**.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  Убедитесь, что приложение запущено, нажав клавишу <kbd>F5</kbd>.  По умолчанию приложение использует репозиторий в памяти, который не требует настройки.  Если веб-сервер остановится, все данные будут утеряны.

1.  Щелкните **Создать примеры опросов**, затем нажмите на опрос и проголосуйте.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Создание базы данных MongoDB

В качестве базы данных мы создадим базу данных MongoLab, размещенную в Azure.

Также можно создать виртуальную машину, работающую на Azure, а затем установить и управлять MongoDB самостоятельно.

Вы можете создать бесплатную пробную версию с помощью MongoLab, выполнив следующие действия.

1.  Войдите в [Портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png)

1.  Щелкните **МАГАЗИН**, затем **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png)

1.  В поле "Имя" введите значение для службы базы данных.

1.  Выберите регион или территориальную группу, где требуется расположить службу базы данных. Если вы планируете пользоваться базой данных из вашего приложения Azure, выберите тот же регион, в котором будет развернуто приложение.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png)

1.  Щелкните **ПРИОБРЕСТИ**.

##<a name="configure-the-project"></a>Настройка проекта

В данном разделе рассматривается настройка приложения для использования базы данных MongoDB, которую вы только что создали.  Здесь вы научитесь получать параметры подключения от портала Azure  Затем мы запустим приложение локально.

1.  На [Портале управления Azure][], выберите **НАДСТРОЙКИ**, а затем выберите созданную ранее службу MongoLab.

1.  Щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**.  Чтобы поместить значение **MONGOLAB\_URI** в буфер обмена используйте кнопку копирования.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  В Visual Studio щелкните правой кнопкой мыши по узлу проекта в обозревателе решений и выберите **Свойства**.  Щелкните вкладку **Отладка**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  Установите значения переменных среды, которые запрашивает приложение, в **Команде отладки сервера**, **Среда**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Это позволит настроить переменные среды, когда вы **Начнете отладку**.  Если вы хотите задать переменные при **Запуске без отладки**, установите те же значения и в **Команде запуска сервера**.

    Также можно определить переменные среды с помощью панели управления Windows.  Это более удобный вариант, если вы не хотите сохранять учетные данные в исходном коде или файле проекта.  Обратите внимание, что для того, чтобы новые значения среды стали доступны приложению, потребуется перезапустить Visual Studio.

1.  Код, который реализует репозиторий MongoDB, находится в **models/mongodb.py**.

1.  Запустите приложение, нажав клавишу <kbd>F5</kbd>.  Опросы, которые создаются командой **Создать примеры опросов** и данные, полученные при голосовании, будут сериализованы в базе данных MongoDB.

1.  Зайдите на страницу **Сведения** и убедитесь, что приложение использует репозиторий **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Просмотр базы данных MongoDB

Вы можете воспользоваться таким приложением как [RoboMongo][], чтобы отправлять запросы и вносить изменения в базу данных MongoDB.  В этом разделе рассматривается использование RoboMongo для просмотра содержимого базы данных приложения с опросами.

1.  Создайте новое подключение.  Вам понадобится значение **MONGOLAB\_URI**, полученное в предыдущем разделе.

    Обратите внимание на формат URI: `mongodb://<name>:<password>@<address>:<port>/<name>`

    Имя совпадает с именем, которое вы ввели при создании службы в Azure.  Оно одновременно является именем базы данных и именем пользователя.

1.  На странице подключения в поле **Имя** задайте любое имя, которое вы хотите использовать для подключения.  Также задайте значения полей **Адрес** и **Порт** в виде *address* и *port* из **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  На странице проверки подлинности задайте значения полей **База данных** и **Имя пользователя** в виде *name* из **MONGOLAB\_URI**.  Также установите для поля **Пароль** значение *password* из **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Сохраните настройки и подключитесь к базе данных.  Теперь вы можете отправлять запросы в подключение для опросов.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS обеспечивает простое развертывание веб-приложения на веб-сайте Azure.

1.  В **Обозревателе решений** щелкните правой кнопкой мыши по узлу проекта и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Для создания нового сайта щелкните **Создать**.

1.  Выберите **Имя сайта** и **Регион**, а затем нажмите **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png)

1.  Примите все значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш веб-браузер автоматически откроет опубликованный сайт.  Если вы вернетесь на страницу сведений, то увидите, что он использует репозиторий памяти **In-Memory**, а не **MongoDB**.

    Поскольку переменные среды не заданы для веб-сайта Azure, он использует значения по умолчанию, установленные в **settings.py**.

##<a name="configure-the-azure-website"></a>Настройка веб-сайта Azure

В этом разделе вы научитесь настраивать переменные среды для сайта.

1.  На [Портале управления Azure][] выберите сайт, созданный в предыдущем разделе.

1.  В верхнем меню щелкните **НАСТРОЙКА**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Прокрутите вниз до пункта **Настройки приложения** и задайте значения элементов **REPOSITORY\_NAME**, **MONGODB\_HOST** и **MONGODB\_DATABASE**, как описано в предыдущем разделе.

  	![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  В нижнем меню щелкните **СОХРАНИТЬ**, затем **ПЕРЕЗАПУСТИТЬ** и **ОБЗОР**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Вы увидите, что приложение работает правильно, используя репозиторий **MongoDB**.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Для получения более подробной информации о средствах Python для Visual Studio, Flask и MongoDB.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для Облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Flask][]
- [MongoDB][]
- [Документация по работе с PyMongo][]
- [PyMongo][]


<!--Link references-->
[Центр разработчиков Python]: /ru-ru/develop/python/
[Облачные службы Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Портал управления Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Образцы VSIX средств Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Средства SDK Azure SDK для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Средства Azure SDK Tools для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Документация по Flask]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[Документация по работе с PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Удаленная отладка в Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты для Облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

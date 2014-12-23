<properties linkid="web-sites-python-ptvs-bottle-mongodb" title="Bottle and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Bottle и MongoDB в Azure с использованием средств Python 2.1 для Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Bottle application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Bottle и MongoDB в Azure с использованием средств Python 2.1 для Visual Studio

В этом учебнике мы создадим простое приложение для опроса, используя один из образцов шаблонов расширения PTVS (Python Tools для Visual Studio).

Приложение для опросов определяет абстракцию для своего репозитория, так что вы сможете легко переключаться между различными типами репозиториев (In-Memory, хранилище таблиц Azure, MongoDB).

Вы узнаете, как использовать одну из служб MongoDB, размещенных в Azure, как настроить приложение для работы с MongoDB, а затем опубликовать приложение на веб-сайте Azure.

Статьи о разработках веб-сайтов Azure средствами Python для Visual Studio с использованием веб-платформ Bottle, Flask и Django и службами MongoDB, хранилища таблиц Azure, баз данных MySQL и SQL см. в [Центре разработчиков Python][].  В данной статье показано использование веб-сайтов Azure, однако эти шаги аналогичны действиям при разработке [облачных служб Azure][].

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
 - [RoboMongo][] (необязательно)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Создание проекта

В этом разделе мы создадим проект Visual Studio, используя образец шаблона.  Мы создадим виртуальную среду и установим необходимые пакеты.  Затем локально запустим приложение с помощью репозитория внутренней памяти по умолчанию.

1.  В Visual Studio последовательно выберите в меню пункты **Файл**, **Новый проект**. 

1.  Шаблоны проектов из образцов VSIX средств Python для Visual Studio можно найти в меню **Python**, **Образцы**.  Чтобы создать проект, выберите **Опросы Bottle для веб-проекта** и нажмите кнопку "ОК".

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Выберите **Установить в виртуальную среду**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  В роли основного интерпретатора выберите **Python 2.7** или **Python 3.4**.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Подтвердите, что приложение запущено, нажав <kbd>F5</kbd>.  По умолчанию приложение использует репозиторий в памяти, который не требует настройки.  При остановке веб-сервера все данные будут утеряны.

1.  Нажмите **Создать шаблоны опросов**, затем щелкните опрос и голосование.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Создание базы данных MongoDB

В качестве базы данных мы создадим базу данных MongoLab, размещенную в Azure.

Также можно создать виртуальную машину, работающую на Azure, а затем установить и управлять MongoDB самостоятельно.

Вы можете создать бесплатную пробную версию с помощью MongoLab, выполнив следующие действия.

1.  Выполните вход на [портал управления Azure][].

1.  В нижней части панели навигации нажмите **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  Последовательно щелкните **МАГАЗИН** и **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  В поле "Имя" введите значение для службы базы данных.

1.  Выберите регион или территориальную группу, где требуется расположить службу базы данных. При использовании базы данных в приложении Azure выберите область, в которой будет разворачиваться приложение.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  Нажмите **ПРИОБРЕСТИ**.

##<a name="configure-the-project"></a>Настройка проекта

В этом разделе мы настроим приложение для использования базы данных MongoDB, созданной на предыдущем этапе.  Здесь вы научитесь получать параметры подключения от портала Azure и локально запускать приложение.

1.  На [портале управления Azure][] щелкните **НАДСТРОЙКИ**, а затем выберите ранее созданную службу MongoLab.

1.  Нажмите **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**.  С помощью кнопки "Копировать" поместите значение **MONGOLAB\_URI** в буфер обмена.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  В Visual Studio щелкните правой кнопкой мыши узел проекта в обозревателе решений и выберите **Свойства**.  Перейдите на вкладку **Отладка**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  В окне **Команда отладки сервера** в поле **Среда** настройте значения переменных среды, требуемые приложением.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Таким образом, при **запуске отладки** будут настроены переменные среды.  Если необходимо настроить переменные среды при **запуске без отладки**, дополнительно задайте те же значения в поле **Выполнить команду сервера**.

    Кроме того, вы можете определить переменные среды с помощью панели управления Windows.  Это более удобный вариант, если вы не хотите сохранять учетные данные в исходном коде или файле проекта.  Обратите внимание на необходимость перезапустить Visual Studio, чтобы новые значения среды вступили в силу для приложения.

1.  Код, реализующий репозиторий MongoDB, находится в файле **models/mongodb.py**.

1.  Запустите приложение клавишей <kbd>F5</kbd>.  Опросы, созданные с помощью команды **Создать шаблон опроса**, и данные, отправляемые при голосовании, будут упорядочены в MongoDB.

1.  Перейдите на страницу **Сведения** и проверьте, использует ли приложение репозиторий **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Просмотр базы данных MongoDB

Для отправки запросов и редактирования базы данных MongoDB можно использовать такое приложение, как [RoboMongo][].  В этом разделе мы будем использовать RoboMongo при просмотре содержимого базы данных приложения для опросов.

1.  Создайте новое подключение.  Вам потребуется **MONGOLAB\_URI**, полученный на предыдущем этапе.

    Соблюдайте формата URI: "mongodb://<имя>:<пароль>@<адрес>:<порт>/<имя>"

    Поле "Имя" соответствует имени, указанному при создании службы с помощью Azure.  Оно используется как для имени базы данных, так и для имени пользователя.

1.  На странице подключений в поле **Имя** задайте любое имя для нового подключения.  Затем в полях **Адрес** и **Порт** укажите *адрес* и *порт* из **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  На странице проверки подлинности укажите **Базу данных** и **Имя пользователя**, указав *имя* из **MONGOLAB\_URI**.  Также задайте **Пароль**, указав *пароль* из **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Сохраните изменения и подключитесь к базе данных.  Теперь можно отправить запрос на сбор опросов.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS обеспечивает простое развертывание веб-приложения на веб-сайте Azure.

1.  Нажмите правой кнопкой узел проекта в **обозревателе решений** и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Нажмите **Веб-сайты Microsoft Azure**.

1.  Создайте новый сайт, нажав кнопку **Создать**.

1.  Укажите **Имя сайта** и **Область**, затем нажмите **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  Примите другие параметры по умолчанию и нажмите **Опубликовать**.

1.  Ваш веб-браузер автоматически откроется на опубликованном сайте.  Если вы перейдете на страницу "Сведения", вы увидите, что сайт использует репозиторий **внутренней памяти**, а не репозиторий **MongoDB**.

    Это связано с тем, что переменные среды не установлены на веб-сайте Azure, а значит, сайт использует параметры по умолчанию, указанные в файле **settings.py**.

##<a name="configure-the-azure-website"></a>Настройка веб-сайта Azure

В этом разделе вы научитесь настраивать переменные среды для сайта.

1.  На [портале управления Azure][] щелкните сайт, созданный на предыдущем этапе.

1.  В верхнем меню выберите пункт **НАСТРОЙКА**.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Прокрутите до раздела **Настройки приложения** и задайте значения в полях **REPOSITORY\_NAME**, **MONGODB\_HOST** и **MONGODB\_DATABASE**, как указано в предыдущих разделах.

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  В нижнем меню последовательно щелкните **СОХРАНИТЬ**, **ПЕРЕЗАПУСК** и **ОБЗОР**.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Вы увидите, что приложение работает, как предполагалось, и использует репозиторий **MongoDB**.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Перейдите по следующим ссылкам для получения дополнительных сведений о средствах Python для Visual Studio, Bottle и MongoDB.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для Облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по работе с Bottle][]
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
[Документация по работе с Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Документация по работе с PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Удаленная отладка в Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты для Облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

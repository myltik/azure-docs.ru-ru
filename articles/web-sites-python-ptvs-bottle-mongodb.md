<properties 
	pageTitle="Использование Bottle и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio" 
	description="Информация об использовании инструментов Python для Visual Studio для создания приложения Bottle, которое хранит данные в экземпляре базы данных MongoDB и может быть развернуто на веб-сайте." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="huvalo"/>




# Использование Bottle и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio

В этом учебнике мы создадим простое приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия](https://www.youtube.com/watch?v=8hQMyf8p_Jo) данного учебника

Приложение опросника реализует абстракцию для своего репозитория, что позволяет вам легко переключаться между разными типами репозиториев (размещенный в памяти, табличное хранилище Azure, MongoDB).

Мы узнаем, как использовать одну из служб MongoDB в Azure, как настроить приложение для использования MongoDB, а также как опубликовать приложение на веб-сайте Azure.

Перейдите в [Центр по разработке для Python][], чтобы узнать больше о разработке веб-сайтов Azure с PTVS при помощи веб-платформ Bottle, Flask и Django, с использованием MongoDB, табличного хранилища Azure, MySQL и служб Базы данных SQL.  Несмотря на то, что эта статья сфокусирована на веб-сайтах Azure, для разработки [облачных служб Azure][] шаги останутся теми же.

+ [Необходимые условия](#prerequisites)
+ [Создание проекта](#create-the-project)
+ [Создание базы данных MongoDB](#create-a-mongodb-database)
+ [Настройка проекта](#configure-the-project)
+ [Изучение базы данных MongoDB](#explore-the-mongodb-database)
+ [Публикация на веб-сайте Azure](#publish-to-an-azure-website)
+ [Настройка веб-сайта Azure](#configure-the-azure-website)
+ [Дальнейшие действия](#next-steps)

##<a name="prerequisites"></a>Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Пакет инструментов SDK Azure для VS 2013][] или [Пакет инструментов SDK Azure для VS 2012][]
 - [Python 2.7 (32-разрядный)][] или [Python 3.4 (32-разрядный)][]
 - [RoboMongo][] (необязательно)

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Создание проекта

В этом разделе мы создадим проект Visual Studio с помощью шаблона.  Мы создадим виртуальную среду и установим необходимые пакеты.  Затем мы запустим приложение локально в размещенном в памяти репозитории.

1.  В Visual Studio выберите **Файл**, **Создать проект**. 

1.  В шаблонах проекта PTVS Samples VSIX в секции **Python** выберите **Примеры**.  Выберите **Веб-проект опросов Bottle** и нажмите кнопку "ОК", чтобы создать проект.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Нажмите кнопку **Установить в виртуальной среде**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Выберите **Python 2.7** или **Python 3.4** в качестве базового интерпретатора.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Убедитесь, что приложение работает, нажав клавишу <kbd>F5</kbd>.  По умолчанию приложение использует размещенный в памяти репозиторий, который не требует настройки.  При остановке веб-сервера все данные будут утеряны.

1.  Щелкните **Создать примеры опросов**, а затем щелкните опросник, чтобы проголосовать.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Создание базы данных MongoDB

В качестве базы данных мы создадим базу данных MongoLab с размещением в Azure.

В качестве альтернативного решения вы можете создать свою собственную виртуальную машину в Azure, а затем установить и настроить MongoDB самостоятельно.

Выполнив следующие шаги, вы создадите базу данных MongoLab с бесплатным пробным периодом.

1.  Выполните вход на [портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  Щелкните **МАГАЗИН**, затем выберите **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  На вкладке "Имя" введите имя службы базы данных.

1.  Выберите регион или территориальную группу, где требуется расположить службу базы данных. При использовании базы данных в приложении Azure, выберите тот же самый регион, в котором будет развернуто приложение.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  Щелкните **КУПИТЬ**.

##<a name="configure-the-project"></a>Настройка проекта

В этой секции мы настроим приложение для использования только что созданной базы данных MongoDB.  Мы узнаем, как получить настройки соединения с портала Azure.  После этого мы запустим приложение локально.

1.  на [портале управления Azure][] щелкните **НАДСТРОЙКИ**, а затем выберите созданную ранее службу MongoLab.

1.  Щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**.  Вы можете воспользоваться кнопкой копирования, чтобы поместить значение **MONGOLAB\_URI** в буфер обмена.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  В Visual Studio щелкните правой кнопкой мыши узел проекта в обозревателе решений и выберите **Свойства**.  Откройте вкладку **Отладка**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Установите необходимые для приложения значения переменных среды с помощью **Отладка команды сервера**, **Среда**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Это позволит настроить переменные среды при **запуске с отладкой**.  Если вы хотите настроить переменные при **запуске без отладки**, установите необходимые значения с помощью **Выполнение команды сервера**.

    Также можно определить значения переменных среды с помощью панели управления Windows.  Это будет наилучшим решением, если вы не желаете хранить учетные данные в исходном коде или файле проекта.  Чтобы новые значения переменных стали доступными для приложения, будет необходимо перезапустить Visual Studio.

1.  Код с реализацией репозитория MongoDB находится в файле **models/mongodb.py**.

1.  Запустите приложение, нажав клавишу <kbd>F5</kbd>.  Опросы, созданные с помощью **Создать примеры опросов** и отправленных данных голосования, будут сериализованы в MongoDB.

1.  Перейдите на страницу **О**, чтобы убедиться в том, что приложение использует репозиторий **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Изучение базы данных MongoDB

Вы можете использовать приложение, такое как [RoboMongo][], для запросов и внесения изменений в базу данных MongoDB.  В этом разделе мы будем использовать RoboMongo для просмотра содержимого базы данных приложения опросов.

1.  Создайте новое соединение.  Вам потребуется **MONGOLAB\_URI**, который мы получили в предыдущем разделе.

    Обратите внимание на формат универсального кода ресурса: `mongodb://<name>:<password>@<address>:<port>/<name>`

    Имя будет совпадать с именем созданной службы Azure.  Оно будет использоваться как в качестве имени базы данных, так и имени пользователя.

1.  На странице соединения в поле **Имя** укажите желаемое имя для данного соединения.  В полях **Адрес** и **Порт** задайте значения *address* и *port* из **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  На странице аутентификации с помощью полей **База данных** и **Имя пользователя** укажите *name* из **MONGOLAB\_URI**.  Также укажите в поле **Пароль** значение *password* из **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Сохраните подключение к базе данных.  Теперь вы можете обращаться к коллекциям опросов.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS предоставляет простой способ развертывания веб-приложения на веб-сайт Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Щелкните **Создать**, чтобы создать новый сайт.

1.  Укажите **Имя сайта** и **Регион**, после чего нажмите кнопку **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  Примите значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш браузер автоматически откроет опубликованный сайт.  Если перейти на страницу "О программе", вы увидите, что используется **размещенный в памяти** репозиторий, а не репозиторий **MongoDB**.

    Это объясняется тем, что переменные среды не устанавливаются на веб-сайте Azure, поэтому используются значения по умолчанию, указанные в файле **settings.py**.

##<a name="configure-the-azure-website"></a>Настройка веб-сайта Azure

В этом разделе мы произведем настройку переменных среды для сайта.

1.  На [портале управления Azure][] щелкните сайт, созданный в предыдущем разделе.

1.  Выберите в меню вверху **НАСТРОИТЬ**.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Прокрутите список вниз до раздела **Параметры приложений** и присвойте переменным **REPOSITORY\_NAME**, **MONGODB\_HOST** и **MONGODB\_DATABASE** значения, описанные выше.

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  Щелкните в нижнем меню **СОХРАНИТЬ**, затем **ПЕРЕЗАПУСТИТЬ** и, наконец, **ОБЗОР**.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Вы должны увидеть, что приложение, как и ожидалось, работает с репозиторием **MongoDB**.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Bottle и MongoDB.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по работе с Bottle][]
- [MongoDB][]
- [Документация по работе с PyMongo][]
- [PyMongo][]


<!--Link references-->
[Центр по разработке для Python]: /ru-ru/develop/python/
[Облачные службы Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[портале управления Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Образцы VSIX средств Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Пакет инструментов SDK Azure для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Пакет инструментов SDK Azure для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32-разрядный)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32-разрядный)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Документация по работе с Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Документация по работе с PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Удаленная отладка в Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты для облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project



<!--HONumber=42-->

<properties 
	pageTitle="Использование Bottle и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio" 
	description="В этой статье рассказывается, как создать приложение Bottle, которое хранит данные в экземпляре базы данных MongoDB, с помощью инструментов Python для Visual Studio и развернуть его в веб-приложениях службы приложений Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>


# Использование Bottle и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio

> **Примечание.** В настоящее время рабочий процесс MongoLab не поддерживается в предварительной версии портала.

В этом учебнике мы используем [Средства Python для Visual Studio], чтобы создать простое веб-приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия](https://www.youtube.com/watch?v=8hQMyf8p_Jo) этого учебника.

Веб-приложение опросника реализует абстракцию для своего репозитория, что позволяет легко переключаться между разными типами репозиториев (размещенный в памяти, табличном хранилище Azure или MongoDB).

Мы узнаем, как использовать одну из служб MongoDB в Azure, настроить приложение для использования MongoDB, а также опубликовать приложение в [веб-приложениях службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Зайдите в [Центр по разработке для Python], где можно найти статьи о разработке веб-приложений службы приложений Azure с PTVS при помощи веб-платформ Bottle, Flask и Django, с использованием MongoDB, табличного хранилища Azure, MySQL и служб Базы данных SQL. Хотя эта статья ориентирована в первую очередь на службу приложений, при разработке для [облачных служб Azure] используются аналогичные процедуры.

## Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio]
 - [Образцы VSIX средств Python 2.1 для Visual Studio]
 - [Пакет инструментов SDK Azure для VS 2013] или [Пакет инструментов SDK Azure для VS 2012]
 - [Python 2.7 (32-разрядный)] или [Python 3.4 (32-разрядный)]
 - [RoboMongo](optional)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Создание проекта

В этом разделе мы создадим проект Visual Studio с помощью шаблона. Мы создадим виртуальную среду и установим необходимые пакеты. Затем мы запустим приложение локально в размещенном в памяти репозитории.

1.  В Visual Studio выберите **Файл** > **Создать проект**. 

1.  В шаблонах проекта PTVS Samples VSIX в секции **Python** выберите **Примеры**. Выберите **Веб-проект опросов Bottle** и нажмите кнопку «ОК», чтобы создать проект.

  	![Диалоговое окно «Новый проект»](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Вам будет предложено установить внешние пакеты. Выберите вариант **Установить в виртуальной среде**.

  	![Диалоговое окно «Внешние пакеты»](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Выберите **Python 2.7** или **Python 3.4** в качестве базового интерпретатора.

  	![Диалоговое окно «Добавление виртуальной среды»](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Убедитесь в том, что приложение работает, нажав клавишу <kbd>F5</kbd>. По умолчанию приложение использует размещенный в памяти репозиторий, который не требует настройки. При остановке веб-сервера все данные будут утеряны.

1.  Щелкните **Создать примеры опросов**, а затем выберите опросник, чтобы проголосовать.

  	![Веб-браузер](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

## Создание базы данных MongoDB

В качестве базы данных мы создадим базу данных MongoLab с размещением в Azure.

В качестве альтернативного решения вы можете создать свою собственную виртуальную машину в Azure, а затем установить и настроить MongoDB самостоятельно.

Выполнив следующие шаги, вы создадите базу данных MongoLab с бесплатным пробным периодом.

1.  Выполните вход на [портал управления Azure].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png) -->

1.  Выберите **MARKETPLACE**, затем **MongoLab**, затем нажмите кнопку **Далее**.

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png) -->

1.  В разделе **Имя** введите имя службы базы данных.

1.  Выберите **регион**, где требуется расположить службу базы данных. При использовании базы данных в приложении Azure, выберите тот же самый регион, в котором будет развернуто приложение.

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png) -->

1.  Нажмите кнопку **Далее**, затем **КУПИТЬ**.

## Настройка проекта

В этой секции мы настроим приложение для использования только что созданной базы данных MongoDB. Мы узнаем, как получить настройки соединения с портала Azure. После этого мы запустим приложение локально.

1.  На [портале управления Azure] щелкните **MARKETPLACE**, а затем выберите созданную ранее службу MongoLab.

1.  Щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**. Значение **MONGOLAB\_URI** можно поместить в буфер обмена с помощью кнопки копирования.

  	<!--![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png) -->

1.  В обозревателе решений щелкните правой кнопкой мыши проект и выберите **Свойства**. Откройте вкладку **Отладка**.

  	![Параметры отладки проекта](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Установите значения переменных среды для своего приложения в разделе **Команда отладки сервера** > **Среда**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    В результате переменные среды будут заданы при **запуске отладки**. Чтобы настроить переменные для **запуска без отладки**, также установите необходимые значения в разделе **Команда запуска сервера**.

    Также можно определить значения переменных среды с помощью панели управления Windows. Это будет наилучшим решением, если вы не желаете хранить учетные данные в исходном коде или файле проекта. Чтобы новые значения переменных стали доступными для приложения, будет необходимо перезапустить Visual Studio.

1.  Код с реализацией репозитория MongoDB находится в файле **models/mongodb.py**.

1.  Нажмите клавишу `F5`, чтобы запустить приложение. Опросы, созданные с помощью команды **Создать примеры опросов**, и отправленные данные голосования будут сериализованы в MongoDB.

1.  Откройте страницу **О программе** и убедитесь в том, что приложение использует репозиторий **MongoDB**.

  	![Веб-браузер](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

## Изучение базы данных MongoDB

Приложение наподобие [RoboMongo] можно использовать для отправки запросов и внесения изменений в базу данных MongoDB. В этом разделе мы используем RoboMongo для просмотра содержимого базы данных приложения опросов.

1.  Создайте новое соединение. Вам потребуется **MONGOLAB\_URI**, который мы получили в предыдущем разделе.

    Обратите внимание на формат универсального кода ресурса: `mongodb://<name>:<password>@<address>:<port>/<name>`

    Имя будет совпадать с именем созданной службы Azure. Оно будет использоваться как в качестве имени базы данных, так и имени пользователя.

1.  На странице соединения в поле **Имя** укажите имя соединения. В полях **Адрес** и **Порт** укажите значения *адреса* и *порта* из **MONGOLAB\_URI**.

  	![Диалоговое окно «Параметры подключения»](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  На странице аутентификации в полях **База данных** и **Имя пользователя** укажите *имя* из **MONGOLAB\_URI**. В поле **Пароль** укажите *пароль* из **MONGOLAB\_URI**.

  	![Диалоговое окно «Параметры подключения»](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Сохраните параметры и подключитесь к базе данных. Теперь вы можете обращаться к коллекциям опросов.

  	![Результаты запроса RoboMongo](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

## Публикация веб-приложения в Azure

С помощью пакета SDK .NET для Azure вы можете легко развернуть свое приложение в среде Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

1.  Щелкните **Веб-приложения Microsoft Azure**.

3. Если вы еще не вошли в службу Azure, нажмите кнопку **Вход** и укажите данные учетной записи Майкрософт для своей подписки Azure.

2.  Нажмите **Создать**, чтобы создать новое веб-приложение.

1.  Заполните перечисленные ниже поля и нажмите кнопку **Создать**.
	-	**Имя веб-приложения**
	-	**План обслуживания приложения**
	-	**Группа ресурсов**
	-	**Регион**
	-	Для параметра **Сервер баз данных** оставьте значение **Без базы данных**

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png) -->

1.  Примите значения по умолчанию и щелкните **Опубликовать**.

1.  Опубликованное веб-приложение автоматически откроется в вашем браузере. Если перейти на страницу «О программе», вы увидите, что используется размещенный **в памяти** репозиторий, а не репозиторий **MongoDB**.

    Это объясняется тем, что переменные среды не устанавливаются на экземпляре веб-приложения Azure, поэтому используются значения по умолчанию, указанные в файле **settings.py**.

## Настройка экземпляра веб-приложений

В этом разделе мы настроим переменные среды для экземпляра веб-приложений.

1.  На [портале управления Azure] щелкните веб-приложение, созданное в предыдущем разделе.

1.  В верхнем меню выберите **НАСТРОИТЬ**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png) -->

1.  Прокрутите список вниз до раздела **Параметры приложений** и присвойте переменным **REPOSITORY\_NAME**, **MONGODB\_HOST** и **MONGODB\_DATABASE** значения, описанные выше.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png) -->

1.  Щелкните в нижнем меню **СОХРАНИТЬ**, затем **ПЕРЕЗАПУСТИТЬ** и, наконец, **ОБЗОР**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  Вы должны увидеть, что приложение, как и ожидалось, работает с репозиторием **MongoDB**.

    Поздравляем!

  	![Веб-браузер](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

## Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Bottle и MongoDB.

- [Документация по средствам Python для Visual Studio]
  - [Веб-проекты]
  - [Проекты для облачной службы]
  - [Удаленная отладка в Microsoft Azure]
- [Документация по работе с Bottle]
- [MongoDB]
- [Документация по работе с PyMongo]
- [PyMongo]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Link references-->
[Центр по разработке для Python]: /develop/python/
[облачных служб Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[портал управления Azure]: https://manage.windowsazure.com
[портале управления Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Средства Python для Visual Studio]: http://aka.ms/ptvs
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
 

<!---HONumber=August15_HO6-->
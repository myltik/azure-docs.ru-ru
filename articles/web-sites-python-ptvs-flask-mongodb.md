<properties 
	pageTitle="Использование Flask и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio" 
	description="Информация об использовании инструментов Python для Visual Studio для создания приложения Flask, которое хранит данные в экземпляре базы данных MongoDB и может быть развернуто на веб-сайте." 
	services="app-service\web" 
	tags="python"
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
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Использование Flask и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio

В этом учебнике мы будем использовать [Документация по средствам Python для Visual Studio][], чтобы создать простое приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия](https://www.youtube.com/watch?v=eql-crFgrAE) данного учебника

Приложение опросника реализует абстракцию для своего репозитория, что позволяет вам легко переключаться между разными типами репозиториев (размещенный в памяти, табличное хранилище Azure, MongoDB).

Мы узнаем, как использовать одну из служб MongoDB в Azure, как настроить приложение для использования MongoDB, а также как опубликовать приложение на веб-сайте Azure.

Перейдите в [Центр по разработке для Python][], чтобы узнать больше о разработке веб-сайтов Azure с PTVS при помощи веб-платформ Bottle, Flask и Django, с использованием MongoDB, табличного хранилища Azure, MySQL и служб Базы данных SQL.  Несмотря на то, что эта статья сфокусирована на веб-сайтах Azure, для разработки [Облачные службы Azure][] шаги останутся теми же.

## Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Средства пакета SDK для Azure для VS 2013][] или [Средства пакета SDK для Azure для VS 2012][]
 - [Python 2.7 (32-разрядная версия)][] или [Python 3.4 (32-разрядная версия)][]
 - [RoboMongo][] (необязательно)

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Создание проекта

В этом разделе мы создадим проект Visual Studio с помощью шаблона.  Мы создадим виртуальную среду и установим необходимые пакеты.  Затем мы запустим приложение локально в размещенном в памяти репозитории.

1.  В Visual Studio выберите **Файл**, **Создать проект**. 

1.  В шаблонах проекта PTVS Samples VSIX в секции **Python** выберите **Примеры**.  Выберите **Веб-проект опросов Flask** и нажмите кнопку "ОК", чтобы создать проект.

  	![Диалоговое окно "Новый проект"](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Нажмите кнопку **Установить в виртуальной среде**.

  	![Диалоговое окно "Внешние пакеты"](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  Выберите **Python 2.7** или **Python 3.4** в качестве базового интерпретатора.

  	![Диалоговое окно "Добавление виртуальной среды"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  Подтвердите, что приложение работает, нажав <kbd>F5</kbd>.  По умолчанию приложение использует размещенный в памяти репозиторий, который не требует настройки.  При остановке веб-сервера все данные будут утеряны.

1.  Щелкните **Создать примеры опросов**, а затем щелкните опросник, чтобы проголосовать.

  	![Веб-браузер](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

## Создание базы данных MongoDB

В качестве базы данных мы создадим базу данных MongoLab с размещением в Azure.

В качестве альтернативного решения вы можете создать свою собственную виртуальную машину в Azure, а затем установить и настроить MongoDB самостоятельно.

Выполнив следующие шаги, вы создадите базу данных MongoLab с бесплатным пробным периодом.

1.  Выполните вход на [портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![Кнопка "Создать"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png)

1.  Щелкните **МАГАЗИН**, затем выберите **MongoLab**.

  	![Диалоговое окно "Выбор надстройки"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png)

1.  На вкладке "Имя" введите имя службы базы данных.

1.  Выберите регион или территориальную группу, где требуется расположить службу базы данных. При использовании базы данных в приложении Azure, выберите тот же самый регион, в котором будет развернуто приложение.

  	![Диалоговое окно "Персонализация надстройки"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png)

1.  Щелкните **КУПИТЬ**.

## Настройка проекта

В этой секции мы настроим приложение для использования только что созданной базы данных MongoDB.  Мы узнаем, как получить настройки соединения с портала Azure.  После этого мы запустим приложение локально.

1.  на [Портал управления Azure][] щелкните **НАДСТРОЙКИ**, а затем выберите созданную ранее службу MongoLab.

1.  Щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**.  Вы можете воспользоваться кнопкой копирования, чтобы поместить значение **MONGOLAB\_URI** в буфер обмена.

  	![Диалоговое окно "Сведения о подключении"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  В Visual Studio щелкните правой кнопкой мыши узел проекта в обозревателе решений и выберите **Свойства**.  Откройте вкладку **Отладка**.

  	![Параметры отладки проекта](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  Установите необходимые для приложения значения переменных среды с помощью **Отладка команды сервера**, **Среда**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Это позволит настроить переменные среды при **запуске с отладкой**.  Если вы хотите настроить переменные при **запуске без отладки**, установите необходимые значения с помощью **Выполнение команды сервера**.

    Также можно определить значения переменных среды с помощью панели управления Windows.  Это будет наилучшим решением, если вы не желаете хранить учетные данные в исходном коде или файле проекта.  Чтобы новые значения переменных стали доступными для приложения, будет необходимо перезапустить Visual Studio.

1.  Код с реализацией репозитория MongoDB находится в файле **models/mongodb.py**.

1.  Запустите приложение с помощью клавиши <kbd>F5</kbd>.  Опросы, созданные с помощью **Создать примеры опросов** и отправленных данных голосования, будут сериализованы в MongoDB.

1.  Перейдите на страницу **О**, чтобы убедиться в том, что приложение использует репозиторий **MongoDB**.

  	![Веб-браузер](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

## Изучение базы данных MongoDB

Вы можете использовать приложение, такое как [RoboMongo][], для запросов и внесения изменений в базу данных MongoDB.  В этом разделе мы будем использовать RoboMongo для просмотра содержимого базы данных приложения опросов.

1.  Создайте новое соединение.  Вам потребуется **MONGOLAB\_URI**, который мы получили в предыдущем разделе.

    Обратите внимание на формат универсального кода ресурса: `mongodb://<name>:<password>@<address>:<port>/<name>`

    Имя будет совпадать с именем созданной службы Azure.  Оно будет использоваться как в качестве имени базы данных, так и имени пользователя.

1.  На странице соединения в поле **Имя** укажите желаемое имя для данного соединения.  В полях **Адрес** и **Порт** задайте значения *address* и *port* из **MONGOLAB\_URI**.

  	![Диалоговое окно "Параметры подключения"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  На странице аутентификации с помощью полей **База данных** и **Имя пользователя** укажите *name* из **MONGOLAB\_URI**.  Также укажите в поле **Пароль** значение *password* из **MONGOLAB\_URI**.

  	![Диалоговое окно "Параметры подключения"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Сохраните параметры и подключитесь к базе данных.  Теперь вы можете обращаться к коллекциям опросов.

  	![Результаты запроса RoboMongo](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

## Публикация на веб-сайте Azure

PTVS предоставляет простой способ развертывания веб-приложения на веб-сайт Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

  	![Диалоговое окно "Публикация веб-сайта"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Щелкните **Создать**, чтобы создать новый сайт.

1.  Укажите **Имя сайта** и **Регион**, после чего нажмите кнопку **Создать**.

  	![Диалоговое окно "Создание сайта в Microsoft Azure"](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png)

1.  Примите значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш браузер автоматически откроет опубликованный сайт.  Если перейти на страницу "О программе", вы увидите, что используется **размещенный в памяти** репозиторий, а не репозиторий **MongoDB**.

    Это объясняется тем, что переменные среды не устанавливаются на веб-сайте Azure, поэтому используются значения по умолчанию, указанные в файле **settings.py**.

## Настройка веб-сайта Azure

В этом разделе мы произведем настройку переменных среды для сайта.

1.  На [Портал управления Azure][] щелкните сайт, созданный в предыдущем разделе.

1.  Выберите в меню вверху **НАСТРОИТЬ**.

  	![Верхнее меню](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Прокрутите список вниз до раздела **Параметры приложений** и присвойте переменным **REPOSITORY\_NAME**, **MONGODB\_HOST** и **MONGODB\_DATABASE** значения, описанные выше.

  	![Параметры приложения](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  Щелкните в нижнем меню **СОХРАНИТЬ**, затем **ПЕРЕЗАПУСТИТЬ** и, наконец, **ОБЗОР**.

  	![Нижнее меню](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Вы должны увидеть, что приложение, как и ожидалось, работает с репозиторием **MongoDB**.

    Поздравляем!

  	![Веб-браузер](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

## Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Flask и MongoDB.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Flask][]
- [MongoDB][]
- [Документация по работе с PyMongo][]
- [PyMongo][]


<!--Link references-->
[Центр по разработке для Python]: /develop/python/
[Облачные службы Azure]: cloud-services-python-ptvs.md

<!--External Link references-->
[Портал управления Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Средства Python для Visual Studio]: http://aka.ms/ptvs
[Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Образцы VSIX средств Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Средства пакета SDK для Azure для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Средства пакета SDK для Azure для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Документация по Flask]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[Документация по работе с PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Удаленная отладка в Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты для облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

<!--HONumber=52-->
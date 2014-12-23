<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django и MySQL на платформе Azure с расширением Python Tools 2.1 для Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django и MySQL на платформе Azure с расширением Python Tools 2.1 для Visual Studio 

В этом учебнике мы создадим простое приложение для опроса, используя один из образцов шаблонов расширения PTVS (Python Tools для Visual Studio).

Вы узнаете, как использовать службу MySQL, размещенную на платформе Azure, как настроить приложение для работы с MySQL и как опубликовать приложение на веб-сайте Azure.

Статьи о разработках веб-сайтов Azure средствами Python для Visual Studio с использованием веб-платформ Bottle, Flask и Django и службами MongoDB, хранилища таблиц Azure, баз данных MySQL и SQL см. в [Центре разработчиков Python][].  В данной статье показано использование веб-сайтов Azure, однако эти шаги аналогичны действиям при разработке [облачных служб Azure][].

+ [Предварительные требования](#prerequisites)
+ [Создание проекта](#create-the-project)
+ [Создайте базу данных MySQL](#create-a-mysql-database)
+ [Настройка проекта](#configure-the-project)
+ [Публикация на веб-сайте Azure](#publish-to-an-azure-website)
+ [Дальнейшие действия](#next-steps)

##<a name="prerequisites"></a>Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Пакет инструментов Azure SDK для VS 2013][] или [Пакет инструментов Azure SDK для VS 2012][]
 - [Python 2.7 (32-разрядная версия)][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Создание проекта

В этом разделе мы создадим проект Visual Studio, используя образец шаблона. Мы создадим виртуальную среду и установим необходимые пакеты.  Мы создадим локальную базу данных с помощью sqlite.  Затем мы запустим приложение локально.

1.  В Visual Studio выберите **Файл**, **Создать проект**.

1.  Шаблоны проектов из образцов VSIX средств Python для Visual Studio можно найти в меню **Python**, **Образцы**.  Выберите **Опросы веб-проект Django** и нажмите кнопку "ОК", чтобы создать проект.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Выберите **Установить в виртуальную среду**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Выберите **Python 2.7** в качестве базового интерпретатора.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Нажмите правой кнопкой узел проекта и выберите **Python**, **Django синхронизация БД**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Откроется консоль управления Django.  Следуйте инструкциям на экране, чтобы создать учетную запись пользователя.

    В папке проекта будет создана база данных sqlite.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Убедитесь, что приложение запущено, нажав клавишу <kbd>F5</kbd>.

1.  Нажмите **Войти** на панели навигации вверху.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Введите учетные данные пользователя, учетную запись которого вы создали при синхронизации базы данных.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Нажмите **Создать примеры опросов**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Нажмите на опрос и проголосуйте.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Создайте базу данных MySQL

В качестве базы данных мы создадим ClearDB MySQL, размещенную в Azure.

В качестве альтернативы вы можете создать свою собственную виртуальную машину в Azure, а затем установить и администрировать базу данных MySQL самостоятельно.

Можно создать базу данных с бесплатным планом, выполнив следующие шаги.

1.  Войдите на [портал управления Azure][].

1.  В нижней части панели навигации нажмите **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Нажмите **ХРАНИЛИЩЕ**, затем **база данных ClearDB MySQL**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  В поле "Имя" введите значение для службы базы данных.

1.  Выберите регион или территориальную группу, где требуется расположить службу базы данных. Если вы планируете пользоваться базой данных из вашего приложения Azure, выберите тот же регион, в котором будет развернуто приложение.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Нажмите **ПРИОБРЕСТИ**.

##<a name="configure-the-project"></a>Настройка проекта

В этом разделе мы настроим наше приложение для использования базы данных MySQL, которую мы только что создали.  Здесь вы научитесь получать параметры подключения от портала Azure  Также мы установим дополнительные пакеты Python, необходимые для использования баз данных MySQL с Django.  Затем мы запустим приложение локально.

1.  На [Портале управления Azure][] нажмите **Надстройки**, затем нажмите службу базы данных ClearDB MySQL, созданную вами ранее.

1.  Нажмите **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**.  Вы можете использовать кнопку копирования, чтобы скопировать значение **CONNECTIONSTRING** в буфер обмена.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  В Visual Studio откройте **settings.py** в папке *Название проекта*.  Временно вставьте строку подключения в редакторе.  Строка подключения представлена в следующем формате.

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

Измените базу данных по умолчанию **ENGINE**, чтобы она использовала MySQL, и задайте значения для **NAME**, **USER**, **PASSWORD** и **HOST** из **CONNECTIONSTRING**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  В обозревателе решений в разделе **Среды Python** нажмите правой кнопкой на виртуальной среде и выберите **Установить пакет Python**.

1. Установите пакет "mysql-python" с помощью **easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Нажмите правой кнопкой узел проекта и выберите **Python**, **Django синхронизация БД**.  

    Будут созданы таблицы базы данных MySQL, созданной нами в предыдущем разделе.  Следуйте инструкциям на экране, чтобы создать учетную запись пользователя, отличающуюся от учетной записи пользователя в базе данных sqlite, созданной в первом разделе.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Запустите приложение клавишей <kbd>F5</kbd>.  Опросы, созданные командой **Создать примеры опросов**, и данные, полученные при голосовании, будут упорядочены в базе данных MySQL.

##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS обеспечивает простое развертывание веб-приложения на веб-сайте Azure.

1.  Нажмите правой кнопкой узел проекта в **обозревателе решений** и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Нажмите **Веб-сайты Microsoft Azure**.

1.  Создайте новый сайт, нажав кнопку **Создать**.

1.  Укажите **Имя сайта** и **Область**, затем нажмите **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Примите другие параметры по умолчанию и нажмите **Опубликовать**.

1.  Ваш веб-браузер автоматически откроется на опубликованном сайте.  Вы должны увидеть, что приложение работает, как и ожидалось, используя базу данных **MySQL**, размещенную на Azure.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Используйте следующие ссылки для получения дополнительных сведений о Python Tools for Visual Studio, Django и MySQL.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для Облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Django][]
- [MySQL][]


<!--Link references-->
[Центр разработчиков Python]: /ru-ru/develop/python/
[Облачные службы Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Портал управления Azure]: https://manage.windowsazure.com
[Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Образцы VSIX средств Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Средства SDK Azure SDK для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Средства Azure SDK Tools для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Удаленная отладка в Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты для Облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Документация по Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/

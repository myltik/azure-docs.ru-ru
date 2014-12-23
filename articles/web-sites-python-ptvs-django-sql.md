<properties linkid="web-sites-python-ptvs-django-sql" title="Django and SQL Database on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django и база данных SQL на платформе Azure с расширением Python Tools 2.1 для Visual Studio (PTVS)" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django и база данных SQL на платформе Azure с расширением Python Tools 2.1 для Visual Studio (PTVS) 

В этом учебнике мы создадим простое приложение для опроса, используя один из образцов шаблонов расширения PTVS (Python Tools для Visual Studio).

Вы узнаете, как использовать размещенные на платформе Azure базы данных SQL, как настраивать приложение для работы базой данных SQL, а затем публиковать приложение на веб-сайте Azure.

Чтобы ознакомиться с другими статьями, посвященными разработке веб-сайтов Azure с PTVS с помощью веб-платформ Bottle, Flask и Django, а также служб MongoDB, хранилища таблиц Azure, MySQL и SQL Database, см. [Центр разработчика Python][].  Хотя данная статья посвящена веб-сайтам Azure, описываемые действия схожи с процедурой разработки [Облачных служб Azure][].

+ [Предварительные требования](#prerequisites)
+ [Создание проекта](#create-the-project)
+ [Создание базы данных SQL](#create-a-sql-database)
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

1.  В Visual Studio выберите **Файл**, **Новый проект**.

1.  Шаблоны из PTVS Samples VSIX находятся здесь: **Python**, **Примеры**.  Чтобы создать проект, выберите **Веб проект для опросов Django**, затем нажмите кнопку "ОК".

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Выберите **Установить в виртуальной среде**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Выберите **Python 2.7** в качестве базового интерпретатора.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Щелкните правой кнопкой мыши по узлу проекта и выберите **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Откроется консоль управления Django.  Следуйте инструкциям на экране, чтобы создать учетную запись пользователя.

    В папке проекта будет создана база данных sqlite.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Убедитесь, что приложение запущено, нажав клавишу <kbd>F5</kbd>.

1.  Щелкните **Вход** в панели навигации наверху.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Введите учетные данные пользователя, учетную запись которого вы создали при синхронизации базы данных.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Щелкните **Создать пример опроса**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Нажмите на опрос и проголосуйте.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>Создание базы данных SQL

Далее мы создадим базу данных SQL Azure.

Чтобы создать базу данных, выполните следующие действия.

1.  Войдите в [Портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Щелкните **СЛУЖБЫ ДАННЫХ**, затем **БАЗА ДАННЫХ SQL**, затем **Быстро создать**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Выберите вариант "Создать сервер базы данных SQL".

1.  Выберите регион или территориальную группу, где требуется расположить базу данных. Если вы планируете пользоваться базой данных из вашего приложения Azure, выберите тот же регион, в котором будет развернуто приложение.

##<a name="configure-the-project"></a>Настройка проекта

В этом разделе мы настроим приложение для использования базы данных SQL, которую только что мы создали.  Вы научитесь получать настройки подключения из портала Azure.  Вы также установите дополнительные пакеты Python, необходимые для использования баз данных SQL с Django.  Затем мы запустим приложение локально.

1.  На [Портале управления Azure][] выберите **БАЗЫ ДАННЫХ SQL**, а затем выберите созданную ранее базу данных.

1.  Нажмите кнопку **УПРАВЛЕНИЕ**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Вам будет предложено обновить правила брандмауэра. Нажмите кнопку **ДА**.  Это действие разрешает подключение к серверу базы данных с компьютера, на котором ведется разработка.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Щелкните **БАЗЫ ДАННЫХ SQL**, затем **СЕРВЕРЫ**.  Выберите сервер для вашей базы данных, затем выберите **НАСТРОЙКА**.

1.  На этой странице вы увидите IP-адрес каждого компьютера, имеющего разрешение на подключение к серверу баз данных.  Должен отобразиться IP-адрес вашего компьютера.

    Ниже в разделе **Разрешенные службы** убедитесь, что службам Azure разрешен доступ к серверу.  Когда приложение запущено на веб-сайте Azure (см. следующий раздел этого учебника), ему будет разрешено подключаться к базе данных.  Нажмите кнопку **СОХРАНИТЬ**, чтобы применить изменения.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  В Visual Studio откройте **settings.py** в папке *Название проекта*. Измените определение "БАЗ ДАННЫХ".

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<User>@<ServerName>',
                'PASSWORD': '<Password>',
                'HOST': '<ServerName>.database.windows.net',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

    Значения элементов "<DatabaseName>", "<Пользователь>" и "<Пароль>" были заданы при создании базы данных и сервера.

    Значения элементов "<ServerName>" и "<ServerPort>" генерируются средой Azure при создании сервера. Они находятся в разделе **Подключение к базе данных**.

1.  В обозревателе решений в разделе **Среды Python** нажмите правой кнопкой мыши на виртуальную среду и выберите **Установить пакет Python**.

1.  Установите пакет "pyodbc" с помощью **easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Установите пакет "django-pyodbc-azure" с помощью **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Щелкните правой кнопкой мыши по узлу проекта и выберите **Python**, **Django Sync DB**.  

    Будут созданы таблицы для базы данных SQL, созданной в предыдущем разделе.  Следуйте инструкциям на экране, чтобы создать учетную запись пользователя, отличающуюся от учетной записи пользователя в базе данных sqlite, созданной в первом разделе.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Запустите приложение, нажав клавишу <kbd>F5</kbd>.  Опросы, созданные командой **Создать примеры опроса**, и данные, полученные при голосовании, будут упорядочены в базе данных SQL.


##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS обеспечивает простое развертывание веб-приложения на веб-сайте Azure.

1.  В **Обозревателе решений** щелкните правой кнопкой мыши по узлу проекта и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Для создания нового сайта щелкните **Создать**.

1.  Выберите **Имя сайта** и **Регион**, а затем нажмите **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Примите все значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш веб-браузер автоматически откроет опубликованный сайт.  Вы увидите, что приложение работает правильно, используя базу данных **SQL**, размещенную на Azure.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Используйте следующие ссылки для получения дополнительных сведений о Python Tools for Visual Studio, Django и базе данных SQL.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для Облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Django][]
- [База данных SQL][]


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
[База данных SQL]: /ru-ru/documentation/services/sql-database/

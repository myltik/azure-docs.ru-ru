<properties 
	pageTitle="Использование Django и базы данных SQL в Azure с помощью инструментов Python 2.1 для Visual Studio" 
	description="Информация об использовании инструментов Python для Visual Studio для создания приложения Django, которое хранит данные в экземпляре базы данных SQL и может быть развернуто на веб-сайте. 
	services="" 
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




# Использование Django и базы данных SQL в Azure с помощью инструментов Python 2.1 для Visual Studio 

В этом учебнике мы создадим простое приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия](https://www.youtube.com/watch?v=ZwcoGcIeHF4) данного учебника

Вы узнаете, как использовать размещенные на платформе Azure базы данных SQL, как настраивать приложение для работы базой данных SQL, а затем публиковать приложение на веб-сайте Azure.

Перейдите в [Центр по разработке для Python][], чтобы узнать больше о разработке веб-сайтов Azure с PTVS при помощи веб-платформ Bottle, Flask и Django, с использованием MongoDB, табличного хранилища Azure, MySQL и служб Базы данных SQL.  Несмотря на то, что эта статья сфокусирована на веб-сайтах Azure, для разработки [облачных служб Azure][] шаги останутся теми же.

+ [Необходимые условия](#prerequisites)
+ [Создание проекта](#create-the-project)
+ [Создание базы данных SQL](#create-a-sql-database)
+ [Настройка проекта](#configure-the-project)
+ [Публикация на веб-сайте Azure](#publish-to-an-azure-website)
+ [Дальнейшие действия](#next-steps)

##<a name="prerequisites"></a>Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Пакет инструментов SDK Azure для VS 2013][] или [Пакет инструментов SDK Azure для VS 2012][]
 - [Python 2.7 (32-разрядная версия)][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Создание проекта

В этом разделе мы создадим проект Visual Studio с помощью шаблона. Мы создадим виртуальную среду и установим необходимые пакеты.  Мы создадим локальную базу данных с помощью sqlite.  После этого мы запустим приложение локально.

1.  В Visual Studio выберите **Файл**, **Создать проект**.

1.  В шаблонах проекта PTVS Samples VSIX в секции **Python** выберите **Примеры**.  Выберите **Веб-проект опросов Django** и нажмите кнопку "ОК", чтобы создать проект.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Нажмите кнопку **Установить в виртуальной среде**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Выберите **Python 2.7** в качестве базового интерпретатора.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Откроется консоль управления Django.  Следуйте инструкциям на экране для создания пользователя.

    Будет создана база данных sqlite в папке проекта.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Убедитесь, что приложение работает, нажав клавишу <kbd>F5</kbd>.

1.  Щелкните **Войти в систему** в панели навигации сверху.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Введите учетные данные пользователя, который был создан при синхронизации базы данных.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Щелкните **Создать примеры опросов**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Выберите опрос и проголосуйте.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>Создание базы данных SQL

В качестве базы данных мы создадим базу данных SQL Azure.

Выполнив следующие шаги, вы создадите базу данных.

1.  Выполните вход на [портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Последовательно выберите **СЛУЖБЫ ДАННЫХ**, **БАЗА ДАННЫХ SQL** и **БЫСТРОЕ СОЗДАНИЕ**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Выберите создание нового сервера базы данных SQL.

1.  Выберите регион или территориальную группу, где требуется расположить базу данных. При использовании базы данных в приложении Azure, выберите тот же самый регион, в котором будет развернуто приложение.

##<a name="configure-the-project"></a>Настройка проекта

В этой секции мы настроим приложение для использования только что созданной базы данных SQL.  Мы узнаем, как получить настройки соединения с портала Azure.  Мы также установим дополнительные пакеты Python, необходимые для использования баз данных SQL с Django.  После этого мы запустим приложение локально.

1.  На [портале управления Azure][] щелкните **БАЗЫ ДАННЫХ SQL**, а затем выберите созданную ранее базу данных.

1.  Щелкните **УПРАВЛЕНИЕ**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Вам будет предложено обновить правила брандмауэра. Щелкните **ДА**.  Это позволит вам подключаться к серверу базы данных со своего компьютера разработчика.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Щелкните **БАЗЫ ДАННЫХ SQL**, затем **СЕРВЕРЫ**.  Щелкните сервер своей базы данных, затем нажмите **НАСТРОИТЬ**.

1.  На этой странице вы увидите IP-адрес каждой машины, имеющей разрешение на подключение к серверу базы данных.  Вы должны увидеть IP-адрес своей машины.

    Внизу, в разделе **Разрешенные службы**, убедитесь, что службам Azure разрешен доступ к серверу.  Когда приложение запускается на веб-сайте Azure (что мы сделаем в следующем разделе данного учебника), ему разрешается подключаться к базе данных.  Чтобы применить изменение, нажмите кнопку **СОХРАНИТЬ**.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  Откройте в Visual Studio файл **settings.py**, который находится в папке *ProjectName*. Измените определение `DATABASES`.

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

    `<DatabaseName>`, `<User>` и `<Password>` - это значения, которые вы указали при создании базы данных и сервера.

    Значения для `<ServerName>` и `<ServerPort>` генерируются Azure при создании сервера и доступны в разделе **Подключение к базе данных**.

1.  В обозревателе решений в разделе **Среды Python** щелкните правой кнопкой мыши виртуальную среду и выберите **Установить пакет Python**.

1.  Установите пакет `pyodbc`, используя **easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Установите пакет `django-pyodbc-azure`, используя **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.  

    В результате будут созданы таблицы для базы данных SQL, созданной в предыдущем разделе.  Следуя подсказкам, создайте пользователя, отличного от пользователя базы данных sqlite, которого мы создали в первом разделе.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Запустите приложение, нажав клавишу <kbd>F5</kbd>.  Опросы, созданные с помощью **Создать примеры опросов** и отправленных данных голосования, будут сериализованы в базе данных SQL.


##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS предоставляет простой способ развертывания веб-приложения на веб-сайт Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Щелкните **Создать**, чтобы создать новый сайт.

1.  Укажите **Имя сайта** и **Регион**, после чего нажмите кнопку **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Примите значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш браузер автоматически откроет опубликованный сайт.  Вы должны увидеть, что приложение, как и ожидалось, работает с базой данных **SQL**, размещенной в Azure.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Django и базе данных SQL.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Django][]
- [База данных SQL][]


<!--Link references-->
[Центр по разработке для Python]: /ru-ru/develop/python/
[Облачные службы Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Портал управления Azure]: https://manage.windowsazure.com
[Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Образцы VSIX средств Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Средства пакета SDK для Azure для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Средства пакета SDK для Azure для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Удаленная отладка в Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты для облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Документация по Django]: https://www.djangoproject.com/
[База данных SQL]: /ru-ru/documentation/services/sql-database/


<!--HONumber=42-->

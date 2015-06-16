<properties 
	pageTitle="Использование Django и базы данных SQL в Azure с помощью инструментов Python 2.1 для Visual Studio" 
	description="Информация об использовании инструментов Python для Visual Studio для создания приложения Django, которое хранит данные в экземпляре базы данных SQL и может быть развернуто на веб-сайте." 
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




# Использование Django и базы данных SQL в Azure с помощью инструментов Python 2.1 для Visual Studio 

В этом учебнике мы будем использовать [Средства Python для Visual Studio][], чтобы создать простое приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия](https://www.youtube.com/watch?v=ZwcoGcIeHF4) данного учебника

Вы узнаете, как использовать размещенные на платформе Azure базы данных SQL, как настраивать приложение для работы базой данных SQL, а затем публиковать приложение на веб-сайте Azure.

Перейдите в [Центр по разработке для Python][], чтобы узнать больше о разработке веб-сайтов Azure с PTVS при помощи веб-платформ Bottle, Flask и Django, с использованием MongoDB, табличного хранилища Azure, MySQL и служб Базы данных SQL.  Несмотря на то, что эта статья сфокусирована на веб-сайтах Azure, для разработки [Облачные службы Azure][] шаги останутся теми же.

## Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Средства пакета SDK для Azure для VS 2013][] или [Средства пакета SDK для Azure для VS 2012][]
 - [Python 2.7 (32-разрядная версия)][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Создание проекта

В этом разделе мы создадим проект Visual Studio с помощью шаблона. Мы создадим виртуальную среду и установим необходимые пакеты.  Мы создадим локальную базу данных с помощью sqlite.  После этого мы запустим приложение локально.

1.  В Visual Studio выберите **Файл**, **Создать проект**.

1.  В шаблонах проекта PTVS Samples VSIX в секции **Python** выберите **Примеры**.  Выберите **Веб-проект опросов Django** и нажмите кнопку "ОК", чтобы создать проект.

  	![Диалоговое окно "Новый проект"](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Нажмите кнопку **Установить в виртуальной среде**.

  	![Диалоговое окно "Внешние пакеты"](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Выберите **Python 2.7** в качестве базового интерпретатора.

  	![Диалоговое окно "Добавление виртуальной среды"](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.

  	![Команда базы данных синхронизации Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Откроется консоль управления Django.  Следуйте инструкциям на экране для создания пользователя.

    Будет создана база данных sqlite в папке проекта.

  	![Окно консоли управления Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Подтвердите, что приложение работает, нажав <kbd>F5</kbd>.

1.  Щелкните **Войти в систему** в панели навигации сверху.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Введите учетные данные пользователя, который был создан при синхронизации базы данных.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Щелкните **Создать примеры опросов**.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Выберите опрос и проголосуйте.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Создание базы данных SQL

В качестве базы данных мы создадим базу данных SQL Azure.

Выполнив следующие шаги, вы создадите базу данных.

1.  Выполните вход на [портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![Кнопка "Создать"](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Последовательно выберите **СЛУЖБЫ ДАННЫХ**, **БАЗА ДАННЫХ SQL** и **БЫСТРОЕ СОЗДАНИЕ**.

  	![Быстро создать базу данных SQL](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Выберите, чтобы создать новый сервер базы данных SQL.

1.  Выберите регион или территориальную группу, где требуется расположить базу данных. При использовании базы данных в приложении Azure, выберите тот же самый регион, в котором будет развернуто приложение.

## Настройка проекта

В этом разделе мы настроим приложение на использование только что созданной 
базы данных SQL.  Мы узнаем, как получить параметры подключения 
с портала Azure.  Мы также установим дополнительные пакеты Python, необходимые для использования 
баз данных SQL с Django.  После этого мы запустим приложение локально.

1.  На [Портал управления Azure][] щелкните **БАЗЫ ДАННЫХ SQL**, а затем выберите созданную ранее базу данных.

1.  Щелкните **УПРАВЛЕНИЕ**.

  	![Кнопка управления](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Вам будет предложено обновить правила брандмауэра. Щелкните **ДА**.  Это позволит вам подключаться к серверу базы данных со своего компьютера разработчика.

  	![Разрешить подключения](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Щелкните **БАЗЫ ДАННЫХ SQL**, затем **СЕРВЕРЫ**.  Щелкните сервер своей базы данных, затем нажмите **НАСТРОИТЬ**.

1.  На этой странице вы увидите IP-адрес каждой машины, имеющей разрешение на подключение к серверу базы данных.  Вы должны увидеть IP-адрес своей машины.

    Внизу, в разделе **Разрешенные службы**, убедитесь, что службам Azure разрешен доступ к серверу.  Когда приложение запускается на веб-сайте Azure (что мы сделаем в следующем разделе данного учебника), ему разрешается подключаться к базе данных.  Чтобы применить изменение, нажмите кнопку **СОХРАНИТЬ**.

  	![Допустимые службы](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

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

  	![Диалоговое окно "Установка пакета Python"](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Установите пакет `django-pyodbc-azure`, используя **pip**.

  	![Диалоговое окно "Установка пакета Python"](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.  

    В результате будут созданы таблицы для базы данных SQL, созданной в предыдущем разделе.  Следуя подсказкам, создайте пользователя, отличного от пользователя базы данных sqlite, которого мы создали в первом разделе.

  	![Окно консоли управления Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Запустите приложение с помощью клавиши <kbd>F5</kbd>.  Опросы, созданные с помощью **Создать примеры опросов** и отправленных данных голосования, будут сериализованы в базе данных SQL.


## Публикация на веб-сайте Azure

PTVS предоставляет простой способ развертывания веб-приложения на веб-сайт Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

  	![Диалоговое окно "Публикация веб-сайта"](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Щелкните **Создать**, чтобы создать новый сайт.

1.  Укажите **Имя сайта** и **Регион**, после чего нажмите кнопку **Создать**.

  	![Диалоговое окно "Создание сайта в Microsoft Azure"](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Примите значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш браузер автоматически откроет опубликованный сайт.  Вы должны увидеть, что приложение, как и ожидалось, работает с базой данных **SQL**, размещенной в Azure.

    Поздравляем!

  	![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Django и базе данных SQL.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Django][]
- [База данных SQL][]


<!--Link references-->
[Центр по разработке для Python]: /develop/python/
[Облачные службы Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Портал управления Azure]: https://manage.windowsazure.com
[Средства Python для Visual Studio]: http://aka.ms/ptvs
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
[База данных SQL]: /documentation/services/sql-database/

<!--HONumber=52--> 
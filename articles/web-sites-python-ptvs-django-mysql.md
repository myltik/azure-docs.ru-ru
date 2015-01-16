<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django и MySQL для Azure с помощью инструментов Python 2.1 для Visual Studio" description="Узнайте, как использовать инструменты Python для Visual Studio, чтобы создать приложение Django, которое хранит данные в экземпляре базы данных MySQL и может быть развернуто на веб-сайте." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django и MySQL для Azure с помощью инструментов Python 2.1 для Visual Studio 

В этом учебнике мы создадим простое приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия] данного учебника(https://www.youtube.com/watch?v=oKCApIrS0Lo).

Мы узнаем, как использовать службу MySQL в Azure, как настроить приложение для использования MySQL, а также как опубликовать приложение на веб-сайте Azure.

Перейдите в [Центр по разработке для Python][], чтобы узнать больше о разработке веб-сайтов Azure с PTVS при помощи веб-платформ Bottle, Flask и Django, с использованием MongoDB, табличного хранилища Azure, MySQL и служб Базы данных SQL.  Несмотря на то, что эта статья сфокусирована на веб-сайтах Azure, для разработки [облачных служб Azure][] шаги останутся теми же.

+ [Предварительные требования](#prerequisites)
+ [Создание проекта](#create-the-project)
+ [Создание базы данных MySQL](#create-a-mysql-database)
+ [Настройка проекта](#configure-the-project)
+ [Публикация на веб-сайте Azure](#publish-to-an-azure-website)
+ [Дальнейшие действия](#next-steps)

##<a name="prerequisites"></a>Предварительные требования

 - Visual Studio 2012 или 2013
 - [Инструменты Python 2.1 для Visual Studio][]
 - [Инструменты Python 2.1 для Visual Studio Samples VSIX][]
 - [Пакет инструментов SDK для Azure для VS 2013][] или [пакет инструментов SDK для Azure для VS 2012][]
 - [Python 2.7 (32-разрядная версия)][]

> [WACOM.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Вы можете <a href="http://azure.microsoft.com/ru-ru/pricing/member-offers/msdn-benefits-details/">активировать преимущества подписчика MSDN</a> или <a href="http://azure.microsoft.com/ru-ru/pricing/free-trial/">подписаться на бесплатный пробный период</a>.
> 
> Если вы хотите начать работу с веб-сайтами Azure до регистрации учетной записи, перейдите по ссылке <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, где вы сможете бесплатно и быстро создать кратковременный начальный сайт ASP.NET на веб-сайтах Azure. Не требуется вводить данные кредитной карты или брать на себя какие-либо обязательства.

##<a name="create-the-project"></a>Создание проекта

В этом разделе мы создадим проект Visual Studio с помощью шаблона. Мы создадим виртуальную среду и установим необходимые пакеты.  Мы создадим локальную базу данных с помощью sqlite.  После этого мы запустим приложение локально.

1.  В Visual Studio выберите **Файл**, **Создать проект**.

1.  В шаблонах проекта PTVS Samples VSIX в секции **Python** выберите **Примеры**.  Выберите **Веб-проект опросов Django** и нажмите кнопку "ОК", чтобы создать проект.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Нажмите кнопку **Установить в виртуальной среде**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Выберите **Python 2.7** в качестве базового интерпретатора.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Откроется консоль управления Django.  Следуйте инструкциям на экране для создания пользователя.

    Будет создана база данных sqlite в папке проекта.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Подтвердите, что приложение работает, нажав <kbd>F5</kbd>.

1.  Щелкните **Войти в систему** в панели навигации сверху.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Введите учетные данные пользователя, который был создан при синхронизации базы данных.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Щелкните **Создать пример опросов**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Выберите опрос и проголосуйте.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Создание базы данных MySQL

В качестве базы данных мы создадим базу данных ClearDB MySQL с размещением в Azure.

В качестве альтернативного решения вы можете создать свою собственную виртуальную машину в Azure, а затем установить и настроить MySQL самостоятельно.

Выполнив следующие шаги, вы сможете создать бесплатную базу данных.

1.  Войдите на [портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Щелкните **МАГАЗИН**, затем выберите **База данных ClearDB MySQL**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  На вкладке "Имя" введите имя службы базы данных.

1.  Выберите регион или территориальную группу, где требуется расположить службу базы данных. При использовании базы данных в приложении Azure, выберите тот же самый регион, в котором будет развернуто приложение.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Щелкните **КУПИТЬ**.

##<a name="configure-the-project"></a>Настройка проекта

В этой секции мы настроим приложение для использования только что созданной базы данных MySQL.  Мы узнаем, как получить настройки соединения с портала Azure.  Мы также установим дополнительные пакеты Python, необходимые для использования баз данных MySQL с Django.  После этого мы запустим приложение локально.

1.  На [портале управления Azure][] щелкните **НАДСТРОЙКИ**, а затем выберите созданную ранее службу базы данных ClearDB MySQL.

1.  Щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**.  Вы можете воспользоваться кнопкой копирования, чтобы поместить значение **CONNECTIONSTRING** в буфер обмена.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Откройте в Visual Studio файл **settings.py**, который находится в папке ProjectName.  Временно вставьте строку подключения в редактор.  Строка подключения имеет такой формат:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Измените **ЯДРО** базы данных по умолчанию на MySQL и задайте значения для **ИМЕНИ**, **ПОЛЬЗОВАТЕЛЯ**, **ПАРОЛЯ** и **УЗЛА** из строки **CONNECTIONSTRING**.

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


1.  В обозревателе решений под разделом **Среды Python** щелкните правой кнопкой мыши виртуальную среду и выберите **Установка пакета Python**.

1. Установите пакет mysql-python, используя **easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.  

    В результате будут созданы таблицы для базы данных MySQL, созданной в предыдущем разделе.  Следуя подсказкам, создайте пользователя, отличного от пользователя базы данных sqlite, которого мы создали в первом разделе.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Запустите приложение с помощью клавиши <kbd>F5</kbd>.  Опросы, созданные с помощью **Создать пример опросов** и отправленных данных голосования, будут сериализованы в базе данных MySQL.

##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS предоставляет простой способ развертывания веб-приложения на веб-сайт Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Щелкните **Создать**, чтобы создать новый сайт.

1.  Укажите **Имя сайта** и **Регион**, после чего нажмите кнопку **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Примите значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш браузер автоматически откроет опубликованный сайт.  Вы должны увидеть, что приложение, как и ожидалось, работает с базой данных **MySQL**, размещенной в Azure.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Django и MySQL.

- [Документация по инструментам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты облачных служб][]
  - [Удаленная отладка на Microsoft Azure][]
- [Документация по Django][]
- [MySQL][]


<!--Link references-->
[Центр по разработке для Python]: /ru-ru/develop/python/
[Облачные службы Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Портал управления Azure]: https://manage.windowsazure.com
[Инструменты Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Инструменты Python 2.1 для Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Инструменты пакета SDK для Azure для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Инструменты пакета SDK для Azure для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Документация по инструментам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Удаленная отладка на Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты облачных служб]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Документация по Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/

<!--HONumber=35.1-->

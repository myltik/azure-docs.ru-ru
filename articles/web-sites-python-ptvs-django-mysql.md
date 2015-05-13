<properties 
	pageTitle="Django и MySQL для Azure с помощью инструментов Python 2.1 для Visual Studio" 
	description="Узнайте, как использовать инструменты Python для Visual Studio, чтобы создать приложение Django, которое хранит данные в экземпляре базы данных MySQL и может быть развернуто на веб-сайте." 
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




# Django и MySQL для Azure с помощью инструментов Python 2.1 для Visual Studio 

В этом учебнике мы будем использовать [Средства Python для Visual Studio][], чтобы создать простое приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия](https://www.youtube.com/watch?v=oKCApIrS0Lo) данного учебника

Вы узнаете, как использовать службу MySQL, размещенную на платформе Azure, как настроить приложение для работы с MySQL и как опубликовать приложение на веб-сайте Azure.

Перейдите в [Центр по разработке для Python][], чтобы узнать больше о разработке веб-сайтов Azure с PTVS при помощи веб-платформ Bottle, Flask и Django, с использованием MongoDB, табличного хранилища Azure, MySQL и служб Базы данных SQL.  Несмотря на то, что эта статья сфокусирована на веб-сайтах Azure, для разработки [Облачные службы Azure][] шаги останутся теми же.

## Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Средства пакета SDK для Azure для VS 2013][] или [Средства пакета SDK для Azure для VS 2012][]
 - [Python 2.7 (32-разрядная версия)][]

> [AZURE.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Вы можете воспользоваться <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">преимуществами Azure для подписчиков MSDN</a> или <a href="http://azure.microsoft.com/pricing/free-trial/">зарегистрироваться для получения бесплатной пробной версии</a>.
> 
> Если вы хотите ознакомиться с веб-сайтами Azure до создания учетной записи, перейдите по адресу <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, где вы можете быстро создать краткосрочный начальный сайт ASP.NET на веб-сайтах Azure бесплатно. Никаких кредитных карт и обязательств.

## Создание проекта

В этом разделе мы создадим проект Visual Studio с помощью шаблона. Мы создадим виртуальную среду и установим необходимые пакеты.  Мы создадим локальную базу данных с помощью sqlite.  После этого мы запустим приложение локально.

1.  В Visual Studio выберите **Файл**, **Создать проект**.

1.  В шаблонах проекта PTVS Samples VSIX в секции **Python** выберите **Примеры**.  Выберите **Веб-проект опросов Django** и нажмите кнопку "ОК", чтобы создать проект.

  	![Диалоговое окно "Новый проект"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Нажмите кнопку **Установить в виртуальной среде**.

  	![Диалоговое окно "Внешние пакеты"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Выберите **Python 2.7** в качестве базового интерпретатора.

  	![Диалоговое окно "Добавление виртуальной среды"](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.

  	![Команда базы данных синхронизации Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Откроется консоль управления Django.  Следуйте инструкциям на экране для создания пользователя.

    Будет создана база данных sqlite в папке проекта.

  	![Окно консоли управления Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Подтвердите, что приложение работает, нажав <kbd>F5</kbd>.

1.  Щелкните **Войти в систему** в панели навигации сверху.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Введите учетные данные пользователя, который был создан при синхронизации базы данных.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Щелкните **Создать примеры опросов**.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Выберите опрос и проголосуйте.

  	![Веб-браузер](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Создание базы данных MySQL

В качестве базы данных мы создадим базу данных ClearDB MySQL с размещением в Azure.

В качестве альтернативного решения вы можете создать свою собственную виртуальную машину в Azure, а затем установить и настроить MySQL самостоятельно.

Выполнив следующие шаги, вы сможете создать бесплатную базу данных.

1.  Выполните вход на [портал управления Azure](https://manage.windowsazure.com).

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![Кнопка "Создать"](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Щелкните **МАГАЗИН**, затем выберите **База данных ClearDB MySQL**.

  	![Диалоговое окно "Выбор надстройки"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  На вкладке "Имя" введите имя службы базы данных.

1.  Выберите регион или территориальную группу, где требуется расположить службу базы данных. При использовании базы данных в приложении Azure, выберите тот же самый регион, в котором будет развернуто приложение.

  	![Диалоговое окно "Персонализация надстройки"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Щелкните **КУПИТЬ**.

## Настройка проекта

В этой секции мы настроим приложение для использования только что созданной базы данных MySQL.  Мы узнаем, как получить настройки соединения с портала Azure.  Мы также установим дополнительные пакеты Python, необходимые для использования баз данных MySQL с Django.  После этого мы запустим приложение локально.

1.  на [Портал управления Azure][] щелкните **НАДСТРОЙКИ**, а затем выберите созданную ранее службу ClearDB MySQL Database.

1.  Щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ**.  Вы можете воспользоваться кнопкой копирования, чтобы поместить значение **CONNECTIONSTRING** в буфер обмена.

  	![Диалоговое окно "Сведения о подключении"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Откройте в Visual Studio файл **settings.py**, который находится в папке *ProjectName*.  Временно вставьте строку подключения в редакторе.  Строка подключения имеет такой формат:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Измените базу данных по умолчанию **ЯДРО** так, чтобы она использовала MySQL, и задайте значения для параметров **ИМЯ**, **ПОЛЬЗОВАТЕЛЬ**, **ПАРОЛЬ** и **УЗЕЛ** из **СТРОКИ ПОДКЛЮЧЕНИЯ**.

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


1.  В обозревателе решений в разделе **Среды Python** щелкните правой кнопкой мыши виртуальную среду и выберите **Установить пакет Python**.

1. Установите пакет `mysql-python`, используя **easy_install**.

  	![Диалоговое окно "Установка пакета" ](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Щелкните правой кнопкой мыши узел проекта и выберите **Python**, **База данных синхронизации Django**.  

    В результате будут созданы таблицы для базы данных MySQL, созданной в предыдущем разделе.  Следуя подсказкам, создайте пользователя, отличного от пользователя базы данных sqlite, которого мы создали в первом разделе.

  	![Окно консоли управления Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Запустите приложение с помощью клавиши <kbd>F5</kbd>.  Опросы, созданные с помощью **Создать примеры опросов** и отправленных данных голосования, будут сериализованы в базе данных MySQL.

## Публикация на веб-сайте Azure

PTVS предоставляет простой способ развертывания веб-приложения на веб-сайт Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

  	![Диалоговое окно "Публикация веб-сайта"](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Щелкните **Создать**, чтобы создать новый сайт.

1.  Укажите **Имя сайта** и **Регион**, после чего нажмите кнопку **Создать**.

  	![Диалоговое окно "Создание сайта в Microsoft Azure"](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Примите значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш браузер автоматически откроет опубликованный сайт.  Вы должны увидеть, что приложение, как и ожидалось, работает с базой данных **MySQL**, размещенной в Azure.

    Поздравляем!

  	![Веб-браузер](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

## Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Django и MySQL.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Django][]
- [MySQL][]


<!--Link references-->
[Центр по разработке для Python]: /develop/python/
[Облачные службы Azure]: cloud-services-python-ptvs.md

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
[MySQL]: http://www.mysql.com/

<!--HONumber=52-->
<properties linkid="web-sites-python-ptvs-flask-table-storage" title="Flask and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask и хранилище таблиц Azure с использованием средств Python Tools 2.1 для Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in Azure Table Storage and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask и хранилище таблиц Azure с использованием средств Python Tools 2.1 для Visual Studio 

В этом учебнике мы создадим простое приложение для опроса, используя один из образцов шаблонов расширения PTVS (Python Tools для Visual Studio).

Приложение для опросов определяет абстракцию для своего репозитория, так что вы сможете легко переключаться между различными типами репозиториев (In-Memory, хранилище таблиц Azure, MongoDB).

Вы узнаете, как создать учетную запись Azure, настроить приложение на работу с хранилищем таблиц Azure и опубликовать приложение на веб-сайте Azure.

Чтобы ознакомиться с другими статьями, посвященными разработке веб-сайтов Azure с PTVS с помощью веб-платформ Bottle, Flask и Django, а также служб MongoDB, хранилища таблиц Azure, MySQL и SQL Database, см. [Центр разработчика Python][].  Хотя данная статья посвящена веб-сайтам Azure, описываемые действия схожи с процедурой разработки [Облачных служб Azure][].

+ [Предварительные требования](#prerequisites)
+ [Создание проекта](#create-the-project)
+ [Создание учетной записи хранения Azure](#create-an-azure-storage-account)
+ [Настройка проекта](#configure-the-project)
+ [Знакомство с хранилищем таблиц Azure](#explore-the-azure-table-storage)
+ [Публикация на веб-сайте Azure](#publish-to-an-azure-website)
+ [Настройка веб-сайта Azure](#configure-the-azure-website)
+ [Дальнейшие действия](#next-steps)

##<a name="prerequisites"></a>Предварительные требования

 - Visual Studio 2012 или 2013
 - [Средства Python 2.1 для Visual Studio][]
 - [Образцы VSIX средств Python 2.1 для Visual Studio][]
 - [Пакет инструментов Azure SDK для VS 2013][] или [Пакет инструментов Azure SDK для VS 2012][]
 - [Python 2.7, 32-разрядная версия][] или [Python 3.4, разрядная версия][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Создание проекта

В этом разделе мы создадим проект Visual Studio, используя образец шаблона.  Мы создадим виртуальную среду и установим необходимые пакеты.  Затем запустите приложение локально, используя стандартный репозиторий, хранимый в памяти.

1.  В Visual Studio выберите **Файл**, **Новый проект**.

1.  Шаблоны из PTVS Samples VSIX находятся здесь: **Python**, **Примеры**.  Чтобы создать проект, выберите **Веб проект для опросов Flask** и нажмите кнопку "ОК".

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Вам будет предложено установить внешние пакеты.  Выберите **Установить в виртуальной среде**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  В качестве базового интерпретатора выберите **Python 2.7** или **Python 3.4**.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Убедитесь, что приложение запущено, нажав клавишу <kbd>F5</kbd>.  По умолчанию приложение использует репозиторий в памяти, который не требует настройки.  Если веб-сервер остановится, все данные будут утеряны.

1.  Щелкните **Создать примеры опросов**, затем нажмите на опрос и проголосуйте.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

Для использования операций хранения требуется учетная запись Azure. Для создания учетной записи хранения, выполните следующие действия:

1.  Войдите в [Портал управления Azure][].

1.  В нижней части области навигации щелкните **СОЗДАТЬ**.

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Щелкните **СЛУЖБЫ ДАННЫХ**, затем **ХРАНИЛИЩЕ** и **БЫСТРОЕ СОЗДАНИЕ**.

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.    В поле URL-адреса введите имя дочернего домена для использования в URI для    учетной записи хранения.  Записи могут содержать от 3 до 24 строчных букв и цифр. Данное значение становится именем хоста в URI, который указывает на blob-объект, очередь или таблицу для подписки.

1.    Выберите регион или территориальную группу, где требуется расположить    хранилище. Если вы планируете пользоваться хранилищем из вашего приложения Azure, выберите тот же регион, в котором будет развернуто приложение.

1.  Дополнительно можно активировать гео-репликацию.  С помощью гео-репликации хранилище Azure надежно хранит ваши данные в двух местоположениях. В обоих местоположениях хранилище Azure поддерживает несколько исправных реплик ваших данных.

1.  Щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**.

##<a name="configure-the-project"></a>Настройка проекта

В данном разделе рассматривается настройка приложения для использования учетной записи хранения, которую вы только что создали.  Здесь вы научитесь получать параметры подключения от портала Azure  Затем мы запустим приложение локально.

1.  На [Портале управления Azure][] выберите учетную запись хранения, созданную в предыдущем разделе.

1.  Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  В Visual Studio щелкните правой кнопкой мыши по узлу проекта в обозревателе решений и выберите **Свойства**.  Щелкните вкладку **Отладка**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Установите значения переменных среды, которые запрашивает приложение, в **Команде отладки сервера**, **Среда**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Это позволит настроить переменные среды, когда вы **Начнете отладку**.  Если вы хотите задать переменные при **Запуске без отладки**, установите те же значения и в **Команде запуска сервера**.

    Также можно определить переменные среды с помощью панели управления Windows.  Это более удобный вариант, если вы не хотите сохранять учетные данные в исходном коде или файле проекта.  Обратите внимание, что для того, чтобы новые значения среды стали доступны приложению, потребуется перезапустить Visual Studio.

1.  Код, в котором используется репозиторий хранилища таблиц Azure, находится в **models/azuretablestorage.py**.  Для получения дополнительной информации об использовании службы таблиц для Python см. [Документацию].

1.  Запустите приложение, нажав клавишу <kbd>F5</kbd>.  Опросы, которые создаются с помощью команды **Создать примеры опросов** и данные, полученные при голосовании, будут сериализованы в хранилище таблиц Azure.

1.  Зайдите на страницу **Сведения** и убедитесь, что приложение использует репозиторий **Хранилище таблиц Azure**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Знакомство с хранилищем таблиц Azure

С помощью обозревателя сервера в Visual Studio очень просто просматривать и редактировать таблицы хранилища.  В этом разделе рассматривается использование обозревателя сервера для просмотра содержимого таблиц приложения с опросами.

> [WACOM.NOTE] Для этого потребуется установить Microsoft Azure Tools, которые входят в пакет [Azure SDK для .NET][].

1.  Откройте **Обозреватель сервера**.  Разверните меню **Azure**, **Хранилище**, ваша учетная запись, затем **Таблицы**.

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Для просмотра содержимого, а также для добавления или редактирования записей, дважды щелкните в окне документа таблицу **Опросы** или **Выборы**.

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Публикация на веб-сайте Azure

PTVS обеспечивает простое развертывание веб-приложения на веб-сайте Azure.

1.  В **Обозревателе решений** щелкните правой кнопкой мыши по узлу проекта и выберите **Опубликовать**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Щелкните **Веб-сайты Microsoft Azure**.

1.  Для создания нового сайта щелкните **Создать**.

1.  Выберите **Имя сайта** и **Регион**, а затем нажмите **Создать**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Примите все значения по умолчанию и щелкните **Опубликовать**.

1.  Ваш веб-браузер автоматически откроет опубликованный сайт.  Если вы вернетесь на страницу сведений, то увидите, что он использует репозиторий памяти **In-Memory**, а не **Хранилище таблиц Azure**.

    Поскольку переменные среды не заданы для веб-сайта Azure, он использует значения по умолчанию, установленные в **settings.py**.

##<a name="configure-the-azure-website"></a>Настройка веб-сайта Azure

В этом разделе вы научитесь настраивать переменные среды для сайта.

1.  На [Портале управления Azure][] выберите сайт, созданный в предыдущем разделе.

1.  В верхнем меню щелкните **НАСТРОЙКА**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Прокрутите вниз до пункта **Настройки приложения** и задайте значения элементов **REPOSITORY\_NAME**, **STORAGE\_NAME** и **STORAGE\_KEY**, как описано в предыдущем разделе.

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. В нижнем меню щелкните **СОХРАНИТЬ**, затем **ПЕРЕЗАПУСТИТЬ** и **ОБЗОР**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Вы увидите, что приложение работает правильно, используя репозиторий **Хранилище таблиц Azure**.

    Поздравляем!

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Дальнейшие действия

Для получения более подробной информации о средствах Python для Visual Studio, Flask и хранилища таблиц Azure.

- [Документация по средствам Python для Visual Studio][]
  - [Веб-проекты][]
  - [Проекты для Облачной службы][]
  - [Удаленная отладка в Microsoft Azure][]
- [Документация по Flask][]
- [Хранилище Azure][]
- [Пакет SDK Azure для Python][]
- [Использование службы таблиц в Python][]


<!--Link references-->
[Центр разработчиков Python]: /ru-ru/develop/python/
[Облачные службы Azure]: ../cloud-services-python-ptvs/
[документация]: ../storage-python-how-to-use-table-storage/
[Использование службы таблиц в Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Портал управления Azure]: https://manage.windowsazure.com
[Пакет Azure SDK для .NET]: http://azure.microsoft.com/ru-ru/downloads/
[Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Образцы VSIX средств Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Средства SDK Azure SDK для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Средства Azure SDK Tools для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Документация по Flask]: http://flask.pocoo.org/
[Удаленная отладка в Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Веб-проекты]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Проекты для Облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Хранилище Azure]: http://azure.microsoft.com/ru-ru/documentation/services/storage/
[Пакет SDK Azure для Python]: https://github.com/Azure/azure-sdk-for-python

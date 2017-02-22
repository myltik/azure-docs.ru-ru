---
title: "Использование Django и базы данных SQL в Azure с помощью инструментов Python 2.2 для Visual Studio"
description: "Информация о том, как создать веб-приложение Django, которое хранит данные в экземпляре базы данных SQL, с помощью инструментов Python для Visual Studio и развернуть его в веб-приложениях службы приложений Azure."
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: 3a677e64-b5a9-4d43-b9c0-66246368b483
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c5e01ef322f72eb5a704fd2273194315bf1a9f6f


---
# <a name="django-and-sql-database-on-azure-with-python-tools-22-for-visual-studio"></a>Использование Django и базы данных SQL в Azure с помощью инструментов Python 2.2 для Visual Studio
В этом учебнике мы используем [Средства Python для Visual Studio] , чтобы создать простое веб-приложение опросника с помощью шаблонов PTVS. Также доступна [видеоверсия](https://www.youtube.com/watch?v=ZwcoGcIeHF4)этого учебника.

Вы узнаете, как использовать базы данных SQL, размещенные на платформе Azure, как настроить веб-приложение для работы с базой данных SQL, а затем опубликовать его в [веб-приложениях службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

В [центре разработчиков для Python] доступны материалы по разработке веб-приложений службы приложений Azure с использованием PTVS, веб-платформ Bottle, Flask и Django, хранилища таблиц Azure, а также служб базы данных SQL и MySQL. Хотя эта статья ориентирована в первую очередь на службу приложений, при разработке для [облачных служб Azure]используются аналогичные процедуры.

## <a name="prerequisites"></a>Предварительные требования
* Visual Studio 2015
* [Python 2.7 (32-разрядная версия).]
* [Инструменты Python 2.2 для Visual Studio]
* [примеров VSIX для инструментов Python 2.2 для Visual Studio]
* [Инструменты пакета SDK для Azure для Visual Studio 2015]
* Django 1.9 или более поздней версии.

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
>
>

## <a name="create-the-project"></a>Создание проекта
В этом разделе мы создадим проект Visual Studio с помощью шаблона. Мы создадим виртуальную среду и установим необходимые пакеты. Мы создадим локальную базу данных с помощью sqlite. Затем запустим веб-приложение локально.

1. В Visual Studio выберите последовательно **Файл** и **Создать проект**.
2. Чтобы найти шаблоны [примеров VSIX для инструментов Python 2.2 для Visual Studio], в разделе **Python** выберите **Примеры**. Выберите **Веб-проект опросов Django** и нажмите кнопку «ОК», чтобы создать проект.

     ![Диалоговое окно «Новый проект»](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)
3. Вам будет предложено установить внешние пакеты. Выберите вариант **Установить в виртуальной среде**.

     ![Диалоговое окно «Внешние пакеты»](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)
4. Выберите **Python 2.7** в качестве базового интерпретатора.

     ![Диалоговое окно «Добавление виртуальной среды»](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)
5. В **обозревателе решений** щелкните правой кнопкой мыши узел проекта, а затем последовательно выберите **Python** и **Django Migrate** (Миграция Django).  Выберите **Django Create Superuser**(Создать суперпользователя Django).
6. Откроется консоль управления Django, а в папке проекта будет создана база данных sqlite. Следуйте инструкциям на экране для создания пользователя.
7. Убедитесь в том, что приложение работает, нажав клавишу <kbd>F5</kbd>.
8. Щелкните **Войти в систему** на панели навигации сверху.

     ![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)
9. Введите учетные данные пользователя, который был создан при синхронизации базы данных.

     ![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)
10. Щелкните **Создать примеры опросов**.

      ![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)
11. Выберите опрос и проголосуйте.

      ![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-sql-database"></a>Создание базы данных SQL
В качестве базы данных мы создадим базу данных SQL Azure.

Выполнив следующие шаги, вы создадите базу данных.

1. Войдите на [портал Azure].
2. В нижней части области навигации щелкните **Создать**. Выберите **Данные+хранилище** > **База данных SQL**.
3. Настройте новую Базу данных SQL, создав новую группу ресурсов, и выберите соответствующее расположение для нее.
4. После создания Базы данных SQL щелкните **Открыть в Visual Studio** в колонке базы данных.
5. Щелкните **Настроить брандмауэр**.
6. В колонке **Параметры брандмауэра** добавьте правило брандмауэра, указав для параметров **Начальный IP-адрес** и **Конечный IP-адрес** общедоступный IP-адрес компьютера разработки. Щелкните **Сохранить**.

   Это позволит вам подключаться к серверу базы данных со своего компьютера разработчика.
7. В колонке базы данных щелкните **Свойства**, затем щелкните **Показать строки подключения к базам данных**.
8. Поместите значение **ADO.NET** в буфер обмена с помощью кнопки копирования.

## <a name="configure-the-project"></a>Настройка проекта
В этом разделе мы настроим веб-приложение для использования только что созданной базы данных SQL. Мы также установим дополнительные пакеты Python, необходимые для использования баз данных SQL с Django. Затем запустим веб-приложение локально.

1. Откройте в Visual Studio файл **settings.py**из папки *ProjectName* . Временно вставьте строку подключения в редакторе. Строка подключения имеет такой формат:

       Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Измените определение `DATABASES` , чтобы использовать указанные выше значения.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1. В обозревателе решений в разделе **Python Environments** (Среды Python) щелкните правой кнопкой мыши виртуальную среду и выберите **Install Python Package** (Установить пакет Python).
2. Установите пакет `pyodbc` , используя **pip**.

     ![Диалоговое окно «Установка пакета Python»](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)
3. Установите пакет `django-pyodbc-azure` , используя **pip**.

     ![Диалоговое окно «Установка пакета Python»](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)
4. В **обозревателе решений** щелкните правой кнопкой мыши узел проекта, а затем последовательно выберите **Python** и **Django Migrate** (Миграция Django).  Выберите **Django Create Superuser**(Создать суперпользователя Django).

   В результате будут созданы таблицы для базы данных SQL, созданной в предыдущем разделе. Следуя подсказкам, создайте пользователя, отличного от пользователя базы данных sqlite, которого мы создали в первом разделе.
5. Запустите приложение, нажав клавишу `F5`. Опросы, созданные с помощью команды **Создать примеры опросов** и отправленных данных голосования, будут сериализованы в базе данных SQL.

## <a name="publish-the-web-app-to-azure-app-service"></a>Публикация веб-приложения в службе приложений Azure
С помощью пакета SDK для Azure для .NET можно легко развернуть свое веб-приложение в веб-приложениях службы приложений Azure.

1. В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

     ![Диалоговое окно «Публикация веб-сайта»](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)
2. Щелкните **Веб-приложения Microsoft Azure**.
3. Нажмите **Создать** , чтобы создать новое веб-приложение.
4. Заполните перечисленные ниже поля и нажмите кнопку **Создать**.

   * **Имя веб-приложения**
   * **План обслуживания приложения**
   * **Группа ресурсов**
   * **Регион**
   * Для параметра **Сервер базы данных** оставьте значение **Нет базы данных**.
5. Примите значения по умолчанию и щелкните **Опубликовать**.
6. Опубликованное веб-приложение автоматически откроется в вашем браузере. Вы должны увидеть, что приложение, как и ожидалось, работает с базой данных **SQL** , размещенной в Azure.

   Поздравляем!

     ![Веб-браузер](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## <a name="next-steps"></a>Дальнейшие действия
Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Django и базе данных SQL.

* [Документация по средствам Python для Visual Studio]
  * [Веб-проекты]
  * [Проекты для облачной службы]
  * [Удаленная отладка в Microsoft Azure]
* [Документация по Django]
* [База данных SQL]

## <a name="whats-changed"></a>Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

<!--Link references-->
[центре разработчиков для Python]: /develop/python/
[облачных служб Azure]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->
[портал Azure]: https://portal.azure.com
[Средства Python для Visual Studio]: http://aka.ms/ptvs
[Инструменты Python 2.2 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[примеров VSIX для инструментов Python 2.2 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Инструменты пакета SDK для Azure для Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 (32-разрядная версия).]: http://go.microsoft.com/fwlink/?LinkId=517190
[Документация по средствам Python для Visual Studio]: http://aka.ms/ptvsdocs
[Удаленная отладка в Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Веб-проекты]: http://go.microsoft.com/fwlink/?LinkId=624027
[Проекты для облачной службы]: http://go.microsoft.com/fwlink/?LinkId=624028
[Документация по Django]: https://www.djangoproject.com/
[База данных SQL]: /documentation/services/sql-database/



<!--HONumber=Feb17_HO3-->



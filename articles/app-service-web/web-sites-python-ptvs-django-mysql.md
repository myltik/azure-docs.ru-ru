---
title: "Использование Django и MySQL в Azure с помощью инструментов Python 2.2 для Visual Studio"
description: "Информация о том, как создать веб-приложение Django, которое хранит данные в экземпляре базы данных MySQL, с помощью инструментов Python для Visual Studio и развернуть его в веб-приложениях службы приложений Azure."
services: app-service\web
documentationcenter: python
author: huguesv
manager: erikre
editor: 
ms.assetid: c60a50b5-8b5e-4818-a442-16362273dabb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: get-started-article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 1c29cfc0a5b6361a7f526c37d5421ee4be3fe2c1
ms.lasthandoff: 01/20/2017


---
# <a name="django-and-mysql-on-azure-with-python-tools-22-for-visual-studio"></a>Использование Django и MySQL в Azure с помощью инструментов Python 2.2 для Visual Studio
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

В этом руководстве показано, как с помощью [средств Python для Visual Studio](https://www.visualstudio.com/vs/python) можно создать простое веб-приложение опросника с использованием примеров шаблонов PTVS. Вы узнаете, как использовать службу MySQL, размещенную на платформе Azure, как настроить веб-приложение для работы с MySQL и как опубликовать веб-приложение в [веб-приложениях службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

> [!NOTE]
> Доступна также видеоверсия этого руководства:
> 
> [PTVS 2.1: Django app with MySQL][video] (PTVS 2.1: приложение Django с использованием MySQL).
> 
> 

В [центре разработчиков Python] доступны материалы по разработке веб-приложений службы приложений Azure с использованием PTVS, веб-платформ Bottle, Flask и Django, хранилища таблиц Azure, а также служб базы данных SQL и MySQL. Хотя эта статья ориентирована в первую очередь на службу приложений, при разработке для [облачных служб Azure]используются аналогичные процедуры.

## <a name="prerequisites"></a>Предварительные требования
* Visual Studio 2015
* [Python 2.7 (32-разрядная версия)] или [Python 3.4 (32-разрядная версия)]
* [Инструменты Python 2.2 для Visual Studio]
* [примеров VSIX для инструментов Python 2.2 для Visual Studio]
* [Инструменты пакета SDK для Azure для Visual Studio 2015]
* Django 1.9 или более поздней версии.

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Не требуется никаких кредитных карт и обязательств.
> 
> 

## <a name="create-the-project"></a>Создание проекта
В этом разделе мы создадим проект Visual Studio с помощью шаблона. Мы создадим виртуальную среду и установим необходимые пакеты. Мы создадим также локальную базу данных с помощью sqlite. После этого мы запустим приложение локально.

1. В Visual Studio выберите последовательно **Файл** и **Создать проект**.
2. Чтобы найти шаблоны [примеров VSIX для инструментов Python 2.2 для Visual Studio], в разделе **Python** выберите **Примеры**. Выберите **Веб-проект опросов Django** и нажмите кнопку «ОК», чтобы создать проект.
   
    ![Диалоговое окно «Новый проект»](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)
3. Вам будет предложено установить внешние пакеты. Выберите вариант **Установить в виртуальной среде**.
   
    ![Диалоговое окно «Внешние пакеты»](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)
4. Выберите **Python 2.7** или **Python 3.4** в качестве базового интерпретатора.
   
    ![Диалоговое окно «Добавление виртуальной среды»](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)
5. В **обозревателе решений** щелкните правой кнопкой мыши узел проекта, а затем последовательно выберите **Python** и **Django Migrate** (Миграция Django).  Выберите **Django Create Superuser**(Создать суперпользователя Django).
6. Откроется консоль управления Django, а в папке проекта будет создана база данных sqlite. Следуйте инструкциям на экране для создания пользователя.
7. Убедитесь, что приложение работает, нажав клавишу `F5`.
8. Щелкните **Войти в систему** на панели навигации сверху.
   
    ![Панель навигации Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)
9. Введите учетные данные пользователя, который был создан при синхронизации базы данных.
   
    ![Форма входа](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)
10. Щелкните **Создать примеры опросов**.
    
     ![Создать примеры опросов](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)
11. Выберите опрос и проголосуйте.
    
     ![Голосование в примерах опросов](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>Создание базы данных MySQL
В качестве базы данных мы создадим базу данных ClearDB MySQL, размещенную в Azure.

В качестве альтернативного решения вы можете создать свою собственную виртуальную машину в Azure, а затем установить и настроить MySQL самостоятельно.

Выполнив следующие шаги, вы сможете создать бесплатную базу данных.

1. Войдите на [портал Azure].
2. В верхней части области навигации последовательно выберите пункты **Создать**, **Данные+хранилище**, **База данных MySQL**.
3. Настройте новую базу данных MySQL, создав новую группу ресурсов, и выберите соответствующее расположение для нее.
4. После создания базы данных MySQL щелкните **Свойства** в колонке базы данных.
5. Поместите значение **CONNECTION STRING** в буфер обмена с помощью кнопки копирования.

## <a name="configure-the-project"></a>Настройка проекта
В этом разделе мы настроим веб-приложение для использования только что созданной базы данных MySQL. Мы также установим дополнительные пакеты Python, необходимые для использования баз данных MySQL с Django. Затем запустим веб-приложение локально.

1. Откройте в Visual Studio файл **settings.py**из папки *ProjectName* . Временно вставьте строку подключения в редакторе. Строка подключения имеет такой формат:
   
        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>
   
    Измените базу данных по умолчанию **ENGINE** так, чтобы она использовала MySQL, и задайте значения для параметров **NAME**, **USER**, **PASSWORD** и **HOST** из строки **CONNECTIONSTRING**.
   
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
2. В обозревателе решений в разделе **Python Environments** (Среды Python) щелкните правой кнопкой мыши виртуальную среду и выберите **Install Python Package** (Установить пакет Python).
3. Установите пакет `mysqlclient` , используя **pip**.
   
    ![Диалоговое окно «Установка пакета»](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)
4. В **обозревателе решений** щелкните правой кнопкой мыши узел проекта, а затем последовательно выберите **Python** и **Django Migrate** (Миграция Django).  Выберите **Django Create Superuser**(Создать суперпользователя Django).
   
    Появятся таблицы для базы данных MySQL, созданной в предыдущем разделе. Следуя подсказкам, создайте пользователя, который будет отличаться от пользователя базы данных sqlite, которого мы создали в первом разделе.
5. Запустите приложение, нажав клавишу `F5`. Опросы, созданные с помощью команды **Создать примеры опросов** и отправленных данных голосования, будут сериализованы в базе данных MySQL.

## <a name="publish-the-web-app-to-azure-app-service"></a>Публикация веб-приложения в службе приложений Azure
С помощью пакета SDK для Azure для .NET можно легко развернуть веб-приложение в службе приложений Azure.

1. В **обозревателе решений** щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.
   
    ![Диалоговое окно «Публикация веб-сайта»](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)
2. Щелкните **Служба приложений Microsoft Azure**.
3. Нажмите **Создать** , чтобы создать новое веб-приложение.
4. Заполните следующие поля и нажмите кнопку **Создать**.
   
   * **Имя веб-приложения**
   * **План обслуживания приложения**
   * **Группа ресурсов**
   * **Регион**
   * Для параметра **Сервер базы данных** оставьте значение **Нет базы данных**.
5. Примите значения по умолчанию и щелкните **Опубликовать**.
6. Опубликованное веб-приложение автоматически откроется в вашем браузере. Вы должны увидеть, что веб-приложение, как и ожидалось, работает с базой данных **MySQL** , размещенной в Azure.
   
    ![Веб-браузер](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)
   
    Поздравляем! Вы опубликовали в Azure веб-приложение на основе MySQL.

## <a name="next-steps"></a>Дальнейшие действия
Используйте следующие ссылки, чтобы узнать больше об инструментах Python для Visual Studio, Django и MySQL.

* [Документация по средствам Python для Visual Studio]
  * [Веб-проекты]
  * [Проекты для облачной службы]
  * [Удаленная отладка в Microsoft Azure]
* [Документация по Django]
* [MySQL]

Дополнительную информацию можно найти в [Центре разработчика Python](/develop/python/).

<!--Link references-->

[центре разработчиков Python]: /develop/python/
[облачных служб Azure]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->

[портал Azure]: https://portal.azure.com
[Python Tools for Visual Studio]: https://www.visualstudio.com/vs/python/
[Инструменты Python 2.2 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[примеров VSIX для инструментов Python 2.2 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Инструменты пакета SDK для Azure для Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 (32-разрядная версия)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Документация по средствам Python для Visual Studio]: http://aka.ms/ptvsdocs
[Удаленная отладка в Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Веб-проекты]: http://go.microsoft.com/fwlink/?LinkId=624027
[Проекты для облачной службы]: http://go.microsoft.com/fwlink/?LinkId=624028
[Документация по Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo


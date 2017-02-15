---
title: "Создание базы данных MySQL и подключение к ней в Azure"
description: "Из этой статьи вы узнаете, как с помощью портала Azure создать базу данных MySQL и подключиться к ней из веб-приложения PHP в Azure."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5f828f90c73f5fd0356fde4e71c11d4d27ec794


---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Создание базы данных MySQL и подключение к ней в Azure
В этом учебнике объясняется, как создать базу данных MySQL на [портале Azure](https://portal.azure.com) (поставщик — [ClearDB](http://www.cleardb.com/)) и подключиться к ней из веб-приложения PHP, запущенного в [службе приложений Azure](app-service/app-service-value-prop-what-is.md). 

> [!NOTE]
> Кроме того, вы можете создать базу данных MySQL как часть [шаблона приложения Marketplace](app-service-web/app-service-web-create-web-app-from-marketplace.md).
> 
> 

## <a name="create-a-mysql-database-in-azure-portal"></a>Создание базы данных MySQL на портале Azure
Чтобы создать базу данных MySQL на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню слева выберите пункт **Создать** > **Данные + хранилище** > **База данных MySQL**.
   
    ![Создание базы данных MySQL на портале Azure — начало](./media/store-php-create-mysql-database/create-db-1-start.png)
3. В [колонке](azure-portal-overview.md)(*колонка*— это страница портала, которая открывается горизонтально) новой базы данных MySQL настройте базу данных.
   
   * **Имя базы данных**: введите уникальное имя.
   * **Подписка**: выберите подписку, которую нужно использовать.
   * **Тип базы данных**: выберите **Общий** для недорогих или бесплатных категорий или **Выделенный** для получения выделенных ресурсов. 
   * **Группа ресурсов**: добавьте базу данных MySQL в существующую или новую [группу ресурсов](azure-resource-manager/resource-group-overview.md) . Ресурсами, которые находятся в одной группе, легко управлять.
   * **Расположение**: выберите близкое к вам расположение. При добавлении элементов в существующую группу ресурсов вам автоматически назначается расположение этой группы.
   * **Ценовая категория**: щелкните **Ценовая категория**, затем выберите один из вариантов (категория **Меркурий** бесплатная) и щелкните **Выбрать**. 
   * **Юридические условия**: щелкните **Юридические условия**, просмотрите сведения о покупке и нажмите кнопку **Приобрести**.
   * **Закрепление на панели мониторинга**: укажите, хотите ли вы получить к элементу доступ непосредственно на панели мониторинга. Это особенно удобно, если вы еще не знакомы с навигацией по порталу.
     
     На следующем снимке экрана представлен пример того, как вы можете настроить базу данных MySQL.  
     ![Создание базы данных MySQL на портале Azure — настройка](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Настроив все параметры, щелкните **Создать**.
   
    ![Создание базы данных MySQL на портале Azure — создание](./media/store-php-create-mysql-database/create-db-3-create.png)
   
    Отобразится всплывающее уведомление о запуске развертывания.
   
    ![Создание базы данных MySQL на портале Azure — выполнение](./media/store-php-create-mysql-database/create-db-4-started-status.png)
   
    По завершении развертывания отобразится еще одно всплывающее окно. Кроме того, на портале также автоматически откроется колонка базы данных MySQL.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Подключение к базе данных MySQL
Чтобы отобразить сведения о подключении для новой базы данных MySQL, просто щелкните **Свойства** в колонке своего веб-приложения.

![Создание базы данных MySQL на портале Azure — колонка базы данных MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Теперь эти сведения о подключении вы можете использовать в любом веб-приложении. [Здесь](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)доступен пример того, как использовать сведения о подключении в простом приложении PHP.

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Подключение веб-приложения Laravel (из руководства по началу работы с PHP)
Предположим, что вы уже изучили учебник [Создание, настройка и развертывание веб-приложения PHP в Azure](app-service-web/app-service-web-php-get-started.md), а в Azure запущено веб-приложение [Laravel](https://www.laravel.com/). Вы легко можете добавить возможности базы данных в это приложение. Просто сделайте следующее:

> [!NOTE]
> Чтобы выполнить приведенные ниже инструкции, следует предварительно изучить учебник [Создание, настройка и развертывание веб-приложения PHP в Azure](app-service-web/app-service-web-php-get-started.md).
> 
> 

1. Настройте приложение Laravel в локальной среде разработки, чтобы указать на базу данных MySQL. Для этого откройте `.env` в корневом каталоге приложения Laravel и настройте параметры базы данных MySQL.
   
        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>
   
   > [!NOTE]
   > В колонке **Свойства** имя базы данных MySQL может совпадать с именем, отображаемым в поле **Имя базы данных** (а может и не совпадать). Лучше проверить параметр "База данных" в поле **Строка подключения** .    
   > 
   > ![Создание базы данных MySQL на портале Azure — выполнение](./media/store-php-create-mysql-database/connect-db-1-database-name.png)
   > 
   > 
2. Быстро проверить, есть ли у вас доступ к MySQL, можно с помощью [стандартного формирования шаблонов проверки подлинности Laravel](https://laravel.com/docs/5.2/authentication#authentication-quickstart). 
   В терминале командной строки выполните из корневого каталога приложения Laravel такие команды:
   
         php artisan migrate
         php artisan make:auth
   
    Первая команда создает таблицы в Azure на основании предварительно заданных переносов в каталог `database/migrations`. Вторая команда формирует шаблоны основных представлений и маршрутов для регистрации и проверки подлинности пользователя.
3. Теперь запустите сервер разработки.
   
        php artisan serve
4. В браузере перейдите по адресу http://localhost:8000 и зарегистрируйте нового пользователя, как показано ниже.
   
    ![Подключение к базе данных MySQL на портале Azure — регистрация пользователя](./media/store-php-create-mysql-database/connect-db-2-development-server.png)
   
    Следуя подсказкам интерфейса, завершите регистрацию. После этого вы автоматически войдете.
   
    ![Подключение к базе данных MySQL на портале Azure — регистрация пользователя](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)
   
    Вы в процессе разработки приложения на основе базы данных MySQL в Azure.
5. Теперь вам осталось реплицировать параметры `.env` в веб-приложение Azure. Запустите такие команды интерфейса командной строки Azure:
   
        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>
   
    Сведения об этом см. в статье [Настройка веб-приложения Azure](app-service-web/app-service-web-php-get-started.md#configure).
6. Затем сохраните и примените в Azure изменения, сделанные раньше во время запуска `php artisan make:auth`.
   
        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master
7. Найдите веб-приложение Azure.
   
        azure site browse
8. Войдите, используя созданные ранее учетные данные пользователя.
   
    ![Подключение к базе данных MySQL на портале Azure — поиск веб-приложения Azure](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)
   
    После входа должен отобразиться понятный экран, предназначенный для работы после входа.
   
    ![Подключение к базе данных MySQL на портале Azure — выполнен вход](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)
   
    Теперь веб-приложение PHP в Azure имеет доступ к данным базы данных MySQL. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию можно найти в [Центре разработчика PHP](/develop/php/).




<!--HONumber=Nov16_HO3-->



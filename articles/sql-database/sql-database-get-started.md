---
title: "Портал Azure: начало работы с базой данных SQL Azure | Документация Майкрософт"
description: "Узнайте, как создать логический сервер базы данных SQL, правила брандмауэра на уровне сервера и базы данных с помощью портала Azure. Вы также научитесь отправлять запросы к базам данных с помощью SQL Server Management Studio."
keywords: "руководство по базам данных SQL, создание базы данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6fd3c0ebe6d7b7e23550358ca1d93840ae8acaa1
ms.openlocfilehash: 3b9a4fbd7121155e34cf9014ca08a4614457fe11


---
# <a name="tutorial-provision-and-access-an-azure-sql-database-using-the-azure-portal-and-sql-server-management-studio"></a>Руководство по подготовке базы данных SQL Azure и доступу к ней с помощью портала Azure и SQL Server Management Studio

Из этого руководства вы узнаете, как с помощью портала Azure выполнять следующие операции.

* создадим группу ресурсов Azure;
* создадим логический сервер SQL Azure;
* просмотрим свойства логического сервера SQL Azure;
* создадим правило брандмауэра на уровне сервера;
* создадим пример отдельной базы данных Adventure Works LT;
* просмотрим свойства примера базы данных Adventure Works LT в Azure.

В этом руководстве мы также будем использовать последнюю версию SQL Server Management Studio. Этот продукт нам понадобится для:

* подключения к логическому серверу и его базе данных master;
* отправки запросов к базе данных master;
* подключения к примеру базы данных;
* отправки запросов к примеру базы данных.

После завершения работы с этим руководством у вас будет пример базы данных и пустая база данных, подключенные к логическому серверу и работающие в группе ресурсов Azure. У вас также будет правило брандмауэра на уровне сервера, которое будет разрешать субъектам уровня сервера входить на сервер из указанного IP-адреса (или диапазона IP-адресов). 

**Оценка времени**. Для работы с этим руководством потребуется около 30 минут (при условии, что предварительные требования уже выполнены).

> [!TIP]
> Во время работы с руководством по началу работы эти же задачи можно выполнять с помощью [C#](sql-database-get-started-csharp.md) или [PowerShell](sql-database-get-started-powershell.md).
>

## <a name="prerequisites"></a>Предварительные требования

* Вам понадобится учетная запись Azure. Вы можете [создать бесплатную учетную запись Azure](/pricing/free-trial/?WT.mc_id=A261C142F) или [активировать преимущества для подписчиков Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* У вас должна быть возможность подключиться к порталу Azure с помощью учетной записи, которой назначена роль владельца или участника подписки. Дополнительные сведения об управлении доступом на основе ролей (RBAC) см. в статье [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md).

> [!NOTE]
> Это руководство поможет вам освоить содержание следующих статей: [Логические серверы базы данных SQL Azure](sql-database-server-overview.md), [Общие сведения о базе данных SQL Azure](sql-database-overview.md) и [Обзор правил брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).
>  


### <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Вход на портал Azure с помощью учетной записи Azure
Используя [существующую подписку](https://account.windowsazure.com/Home/Index), выполните следующие действия, чтобы подключиться к порталу Azure.

1. Откройте любой браузер и подключитесь к [порталу Azure](https://portal.azure.com/).
2. Выполните вход на [портал Azure](https://portal.azure.com/).
3. На странице **входа** введите учетные данные своей подписки.
   
   ![Вход](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Создание логического сервера SQL Server на портале Azure

1. Щелкните **Создать**, введите **sql server** и нажмите клавишу **ВВОД**.

    ![Логический сервер SQL Server](./media/sql-database-get-started/logical-sql-server.png)
2. Щелкните **SQL Server (логический сервер)**.
   
    ![Создание логического сервера SQL Server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Щелкните **Создать**, чтобы открыть колонку создания логического сервера SQL Server.

    ![Новый логический сервер SQL Server](./media/sql-database-get-started/new-logical-sql-server.png)
3. В поле "Имя сервера" введите допустимое имя для нового логического сервера. Зеленый флажок указывает, что выбрано допустимое имя.
    
    ![Имя нового сервера](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Полным именем нового сервера будет <имя_вашего_сервера>.database.windows.net.
    >
    
4. В текстовом поле "Имя входа администратора сервера" укажите имя пользователя, которое будет использоваться сервером при проверке подлинности SQL Server. Это имя называется именем субъекта сервера. Зеленый флажок указывает, что выбрано допустимое имя.
    
    ![Учетные данные администратора SQL](./media/sql-database-get-started/sql-admin-login.png)
5. В полях **Пароль** и **Подтверждение пароля** укажите пароль для входа в учетную запись субъекта сервера. Зеленый флажок указывает, что выбран допустимый пароль.
    
    ![Пароль администратора SQL](./media/sql-database-get-started/sql-admin-password.png)
6. Выберите подписку, в которой у вас есть разрешение на создание объектов.

    ![Подписка](./media/sql-database-get-started/subscription.png)
7. Над полем "Группа ресурсов" установите переключатель **Создать новую**, а затем в текстовом поле введите допустимое имя новой группы ресурсов. Также можно использовать существующую группу, если она уже создана. Зеленый флажок указывает, что выбрано допустимое имя.

    ![Создание группы ресурсов](./media/sql-database-get-started/new-resource-group.png)

8. В списке **Расположение** выберите центр обработки данных, соответствующий вашему расположению, например "Восточная Австралия".
    
    ![Расположение сервера](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > Параметр **Разрешить службам Azure доступ к серверу** невозможно изменить в этой колонке. Его можно изменить в колонке брандмауэра сервера. Дополнительные сведения см. в статье [Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure](sql-database-control-access-sql-authentication-get-started.md).
    >
    
9. Щелкните **Создать**.

    ![кнопка "Создать"](./media/sql-database-get-started/create.png)

## <a name="view-the-logical-sql-server-properties-in-the-azure-portal"></a>Просмотр свойств логического сервера SQL Server на портале Azure

1. На портале Azure щелкните **Больше служб**.

    ![Пункт "Больше служб"](./media/sql-database-get-started/more-services.png)
2. В поле фильтра введите **SQL**, а затем щелкните значок звезды напротив пункта "Серверы SQL". Так вы добавите серверы SQL Server в список избранного в Azure. 

    ![Добавление в избранное](./media/sql-database-get-started/favorite.png)
3. В колонке по умолчанию щелкните **Серверы SQL**, чтобы открыть список серверов SQL Server в вашей подписке Azure. 

    ![Создание нового сервера SQL Server](./media/sql-database-get-started/new-sql-server.png)

4. Щелкните свой новый сервер SQL Server, чтобы просмотреть его свойства на портале Azure. Подробное описание параметров из этой колонки приведено в последующих руководствах.

    ![Колонка сервера SQL Server](./media/sql-database-get-started/sql-server-blade.png)
5. В разделе "Параметры" щелкните **Свойства**, чтобы просмотреть различные свойства логического сервера SQL Server.

    ![Свойства сервера SQL Server](./media/sql-database-get-started/sql-server-properties.png)
6. Скопируйте полное имя сервера в буфер обмена. Оно нам вскоре понадобится.

    ![Полное имя сервера SQL Server](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Создание правила брандмауэра на уровне сервера с помощью портала Azure

1. В колонке сервера SQL Server в разделе "Параметры" щелкните **Брандмауэр**, чтобы открыть колонку брандмауэра для SQL Server.

    ![Брандмауэр SQL Server](./media/sql-database-get-started/sql-server-firewall.png)

2. Просмотрите IP-адрес клиента и убедитесь, что это именно ваш IP-адрес в Интернете. Для этого в любом браузере введите поисковый запрос "мой IP-адрес". Иногда адреса могут не совпадать по тем или иным причинам.

    ![Ваш IP-адрес](./media/sql-database-get-started/your-ip-address.png)

3. Для удобства предположим, что IP-адреса совпадают. Далее на панели инструментов нажмите кнопку **Добавить IP-адрес клиента**.

    ![Кнопка "Добавить IP-адрес клиента"](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Брандмауэр базы данных SQL на сервере можно открыть для одного IP-адреса или целого диапазона IP-адресов. Открыв брандмауэр, вы дадите возможность администраторам и пользователям SQL входить в любую базу данных на сервере, для которой у них есть действительные учетные данные.
    >

4. На панели инструментов нажмите кнопку **Сохранить**, чтобы сохранить это правило брандмауэра на уровне сервера, а затем нажмите кнопку **ОК**.

    ![Кнопка "Добавить IP-адрес клиента"](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Подключение к SQL Server с помощью SQL Server Management Studio (SSMS)

1. Сведения о том, как скачать и установить последнюю версию SSMS, см. в статье [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Когда новая версия становится доступной для скачивания, последняя версия SSMS выдает соответствующий запрос. Это позволяет всегда пользоваться обновленным ПО.

2. После установки введите в поле поиска Windows текст **Microsoft SQL Server Management Studio** и нажмите клавишу **ВВОД**, чтобы запустить приложение.

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. В диалоговом окне подключения к серверу введите данные, необходимые для подключения к серверу SQL Server с использованием проверки подлинности SQL Server.

    ![Подключение к серверу](./media/sql-database-get-started/connect-to-server.png)
4. Щелкните **Подключить**.

    ![Подключение к серверу установлено](./media/sql-database-get-started/connected-to-server.png)
5. В обозревателе объектов последовательно разверните **Базы данных**, **Системные базы данных** и **master**, чтобы просмотреть объекты в базе данных master.

    ![База данных master](./media/sql-database-get-started/master-database.png)
6. Щелкните правой кнопкой мыши **master** и выберите **Создать запрос**.

    ![Запрос к базе данных master](./media/sql-database-get-started/query-master-database.png)

8. В окне запроса введите следующее:

   ```select * from sys.objects```

9.  На панели инструментов нажмите кнопку **Выполнить**, чтобы получить список всех системных объектов в базе данных master.

    ![Запрос системных объектов из базы данных master](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Сведения о системе безопасности SQL см. в статье [Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure](sql-database-control-access-sql-authentication-get-started.md)
    >

## <a name="create-new-database-in-the-azure-portal-using-adventure-works-lt-sample"></a>Создание базы данных на портале Azure с использованием примера Adventure Works LT

1. На портале Azure в колонке по умолчанию щелкните **Базы данных SQL**.

    ![базы данных sql](./media/sql-database-get-started/new-sql-database.png)
2. В колонке баз данных SQL щелкните **Добавить**.

    ![Добавление базы данных SQL](./media/sql-database-get-started/add-sql-database.png)
3. В колонке базы данных SQL ознакомьтесь с автоматически подставленной информацией.

    ![Колонка базы данных SQL](./media/sql-database-get-started/sql-database-blade.png)
4. Укажите допустимое имя базы данных.

    ![Имя базы данных SQL](./media/sql-database-get-started/sql-database-name.png)
5. В разделе "Выбрать источник" щелкните **Пример**, а затем в списке "Выберите пример" щелкните **AdventureWorksLT [V12]**.
   
    ![Adventure Works LT](./media/sql-database-get-started/adventureworkslt.png)
6. В разделе "Сервер" укажите учетные данные администратора.

    ![Учетные данные для сервера](./media/sql-database-get-started/server-credentials.png)

    > [!NOTE]
    > Базу данных можно добавить на сервер как отдельную базу данных (по умолчанию) или в составе пула эластичных БД. Дополнительные сведения о пулах эластичных БД см. в статье [Что такое пул эластичных БД Azure](sql-database-elastic-pool.md).
    >

7. В разделе "Ценовая категория" выберите уровень **Базовый**. При необходимости ценовую категорию можно будет изменить позже, но для учебных целей мы рекомендуем использовать категорию с наименьшей стоимостью.

    ![Ценовая категория](./media/sql-database-get-started/pricing-tier.png)
8. Щелкните **Создать**.

    ![кнопка "Создать"](./media/sql-database-get-started/create.png)

## <a name="view-database-properties-in-the-azure-portal"></a>Просмотр свойств базы данных на портале Azure

1. В колонке "Базы данных SQL" щелкните имя новой базы данных, чтобы просмотреть ее свойства на портале Azure. Подробное описание параметров из этой колонки приведено в последующих руководствах. 

    ![Колонка нового примера базы данных](./media/sql-database-get-started/new-sample-db-blade.png)
2. Щелкните **Свойства**, чтобы просмотреть дополнительные сведения о базе данных.

    ![Свойства нового примера базы данных](./media/sql-database-get-started/new-sample-db-properties.png)

3. Щелкните **Показать строки подключения к базам данных**.

    ![Строки подключения к новому примеру базы данных](./media/sql-database-get-started/new-sample-db-connection-strings.png)
4. Щелкните **Обзор**, а затем в области "Основное" щелкните имя сервера.
    
    ![Область "Основное" нового примера базы данных](./media/sql-database-get-started/new-sample-db-essentials-pane.png)
5. В области "Основное" для вашего сервера просмотрите сведения о только что добавленной базе данных.

    ![Новый пример базы данных в области "Основное"](./media/sql-database-get-started/new-sample-db-server-essentials-pane.png)

## <a name="connect-and-query-sample-database-using-sql-server-management-studio"></a>Подключение к примеру базы данных и отправка к ней запросов с помощью SQL Server Management Studio

1. Переключитесь в SQL Server Management Studio и в обозревателе объектов щелкните **Базы данных**. Затем на панели инструментов нажмите кнопку **Обновить**, чтобы просмотреть свой пример базы данных.

    ![Новый пример базы данных в SSMS](./media/sql-database-get-started/new-sample-db-ssms.png)
2. В обозревателе объектов разверните новую базу данных, чтобы просмотреть ее объекты.

    ![Объекты новой базы данных в SSMS](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Щелкните правой кнопкой мыши пример базы данных и выберите пункт **Создать запрос**.

    ![Запрос к новому примеру базы данных в SSMS](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. В окне запроса введите следующее:

   ```select * from sys.objects```
   
9.  На панели инструментов нажмите кнопку **Выполнить**, чтобы получить список всех системных объектов в примере базы данных.

    ![Запрос на получение системных объектов из нового примера базы данных в SSMS](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-new-blank-database-using-sql-server-management-studio"></a>Создание пустой базы данных с помощью SQL Server Management Studio

1. В обозревателе объектов щелкните правой кнопкой мыши элемент **Базы данных** и выберите пункт **Новая база данных**.

    ![Создание пустой базы данных в SSMS](./media/sql-database-get-started/new-blank-database-ssms.png)

    > [!NOTE]
    > SSMS может создать для вас скрипт Transact-SQL для создания базы данных.
    >

2. В диалоговом окне создания базы данных введите в соответствующем текстовом поле имя базы данных. 

    ![Ввод имени пустой базы данных в SSMS](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. В диалоговом окне создания базы данных щелкните **Параметры**, а затем измените выпуск на **Базовый**.

    ![Параметры пустой базы данных в SSMS](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Просмотрите в этом окне, какие еще параметры можно изменить для базы данных SQL Azure. Дополнительные сведения об этих параметрах см. в статье [CREATE DATABASE (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Нажмите кнопку **ОК**, чтобы создать пустую базу данных.
5. По завершении обновите в обозревателе объектов узел "Базы данных" и просмотрите только что созданную пустую базу данных. 

    ![Новая пустая база данных в обозревателе объектов](./media/sql-database-get-started/new-blank-database-object-explorer.png)

> [!TIP]
> Чтобы сэкономить деньги во время обучения, удалите неиспользуемые базы данных. Базы данных выпуска "Базовый" можно восстановить в течение семи дней. Ни в коем случае не удаляйте сервер. Сделав это, вы не сможете восстановить ни сам север, ни его базы данных.
>


## <a name="next-steps"></a>Дальнейшие действия
Завершив работу с этим руководством, изучите дополнительные материалы, в которых продолжается начатая в этом руководстве тема. 

- Основные сведения об аутентификации SQL Server см. в статье [Руководство по базам данных SQL: аутентификация, доступ и правила брандмауэра уровня базы данных в SQL Server](sql-database-control-access-sql-authentication-get-started.md).
- Основные сведения об аутентификации Azure Active Directory см. в статье [Руководство по базам данных SQL: доступ с аутентификацией Azure AD и правила брандмауэра уровня базы данных](sql-database-control-access-aad-authentication-get-started.md).
* Если вы хотите поработать с запросами к примеру базы данных на портале Azure, см. страницу [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/) (Предварительная версия: интерактивные запросы к базам данных SQL).
* Если вы знаете Excel, узнайте, как [подключиться к базе данных SQL в Azure с помощью Excel](sql-database-connect-excel.md).
* Если вы готовы написать свой собственный код, выберите язык программирования в [библиотеках подключений для базы данных SQL и SQL Server](sql-database-libraries.md).
* Если вы хотите перенести локальные базы данных SQL Server в облако Azure, см. статью [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
* Чтобы загрузить данные в новую таблицу из CSV-файла с помощью программы командной строки BCP, см. сведения в статье [Загрузка данных из CSV-файла в хранилище данных SQL Azure (неструктурированные файлы)](sql-database-load-from-csv-with-bcp.md).
* Сведения о том, как создавать таблицы и другие объекты, см. в разделе "Создание таблицы" в статье [Создание таблицы (Учебник)](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Дополнительные ресурсы

- Технический обзор см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).
- Сведения о ценах см. на [странице с ценами на базы данных SQL Azure](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Feb17_HO1-->



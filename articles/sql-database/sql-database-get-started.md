---
title: "Краткое руководство. Начало работы с базой данных SQL Azure | Документация Майкрософт"
description: "Вы узнаете, как создать логический сервер базы данных SQL, правило брандмауэра на уровне сервера и базы данных на портале Azure, а также как использовать SQL Server Management Studio с помощью базы данных SQL Azure."
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
ms.date: 02/04/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6453cca9f876e6c363fbed463263c0f9684a3e70
ms.openlocfilehash: b838974de06ecbc751254064e2310df51c450086


---
# <a name="quick-start-tutorial-your-first-azure-sql-database"></a>Краткое руководство. Начало работы с базой данных SQL Azure

В этом кратком руководстве раскрываются следующие темы:

* [Создание нового логического сервера](sql-database-get-started.md#create-a-new-logical-sql-server) 
* [Просмотр свойств логического сервера](sql-database-get-started.md#view-the-logical-server-properties) 
* [Создание правила брандмауэра на уровне сервера](sql-database-get-started.md#create-a-server-level-firewall-rule) 
* [Подключение к серверу с помощью SSMS](sql-database-get-started.md#connect-to-the-server-with-ssms) 
* [Создание базы данных с примерами данных](sql-database-get-started.md#create-a-database-with-sample-data) 
* [Просмотр свойств базы данных](sql-database-get-started.md#view-the-database-properties) 
* [Запрос базы данных на портале Azure](sql-database-get-started.md#query-the-database-in-the-azure-portal) 
* [Подключение к базе данных и выполнение запросов с помощью SSMS](sql-database-get-started.md#connect-and-query-the-database-with-ssms) 
* [Создание пустой базы данных с помощью SSMS](sql-database-get-started.md#create-a-blank-database-with-ssms) 
* [Устранение неполадок подключения](sql-database-get-started.md#troubleshoot-connectivity) 
* [Удаление базы данных](sql-database-get-started.md#delete-a-single-database) 


В этом кратком руководстве по началу работы вы создадите пример базы данных и пустую базу данных, работающую в группе ресурсов Azure и подключенную к логическому серверу. Вы также создадите два правила брандмауэра на уровне сервера, которые позволят субъекту уровня сервера входить на сервер из двух указанных IP-адресов. Наконец, вы узнаете, как запросить базу данных на портале Azure, а также как подключиться и сделать запрос с помощью SQL Server Management Studio. 

**Оценка времени.** Для работы с этим руководством потребуется около 30 минут (при условии, что предварительные требования уже выполнены).

> [!TIP]
> Эти же задачи можно выполнять с помощью [C#](sql-database-get-started-csharp.md) или [PowerShell](sql-database-get-started-powershell.md).
>

## <a name="prerequisites"></a>Предварительные требования

* Вам понадобится учетная запись Azure. Вы можете [создать бесплатную учетную запись Azure](/pricing/free-trial/?WT.mc_id=A261C142F) или [активировать преимущества для подписчиков Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* У вас должна быть возможность подключиться к порталу Azure с помощью учетной записи, которой назначена роль владельца или участника подписки. Дополнительные сведения об управлении доступом на основе ролей (RBAC) см. в статье [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md).

> [!NOTE]
> Это краткое руководство поможет вам освоить содержание следующих статей: [Логические серверы базы данных SQL Azure](sql-database-server-overview.md), [Общие сведения о базе данных SQL Azure](sql-database-overview.md) и [Обзор правил брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).
>  


### <a name="sign-in-to-the-azure-portal-with-your-azure-account"></a>Вход на портал Azure с помощью учетной записи Azure
Чтобы подключиться к порталу Azure с помощью [учетной записи Azure](https://account.windowsazure.com/Home/Index), сделайте следующее:

1. Откройте любой браузер и подключитесь к [порталу Azure](https://portal.azure.com/).
2. Выполните вход на [портал Azure](https://portal.azure.com/).
3. На странице **входа** введите учетные данные своей подписки.
   
   ![Вход](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Создание нового логического сервера SQL

Чтобы создать новый логический сервер с помощью портала Azure в любом регионе, сделайте следующее:

1. Щелкните **Создать**, введите **sql server** и нажмите клавишу **ВВОД**.

    ![Логический сервер SQL Server](./media/sql-database-get-started/logical-sql-server.png)
2. Щелкните **SQL Server (логический сервер)**.
   
    ![Создание логического сервера SQL Server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Щелкните **Создать**, чтобы открыть колонку создания логического сервера SQL Server.

    ![Новый логический сервер SQL Server](./media/sql-database-get-started/new-logical-sql-server.png)
3. В поле "Имя сервера" введите допустимое имя для нового логического сервера. Зеленый флажок указывает, что выбрано допустимое имя.
    
    ![Имя нового сервера](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Полное имя для нового сервера будет выглядеть так: <имя_вашего_сервера>.database.windows.net.
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

## <a name="view-the-logical-server-properties"></a>Просмотр свойств логического сервера

Выполните приведенные ниже действия, чтобы просмотреть свойства сервера с помощью портала Azure. Это имя вам потребуется для подключения к этому серверу в последующей процедуре. 

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

## <a name="create-a-server-level-firewall-rule"></a>создадим правило брандмауэра на уровне сервера;

Приведенные здесь действия помогут вам создать новое правило брандмауэра на уровне сервера с помощью портала Azure, чтобы вы могли подключиться к серверу с помощью SQL Server Management Studio в следующей процедуре.

1. В колонке сервера SQL Server в разделе "Параметры" щелкните **Брандмауэр**, чтобы открыть колонку брандмауэра для SQL Server.

    ![Брандмауэр SQL Server](./media/sql-database-get-started/sql-server-firewall.png)

2. Нажмите кнопку **Добавить IP-адрес клиента** на панели инструментов.

    ![Кнопка "Добавить IP-адрес клиента"](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Брандмауэр базы данных SQL на сервере можно открыть для одного IP-адреса или целого диапазона IP-адресов. Открыв брандмауэр, вы дадите возможность администраторам и пользователям SQL входить в любую базу данных на сервере, для которой у них есть действительные учетные данные.
    >

4. На панели инструментов нажмите кнопку **Сохранить**, чтобы сохранить это правило брандмауэра на уровне сервера, а затем нажмите кнопку **ОК**.

    ![Кнопка "Добавить IP-адрес клиента"](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Подключение к серверу с помощью SSMS

Чтобы подключиться к логическому серверу SQL с помощью SQL Server Management Studio, сделайте следующее:

1. Сведения о том, как скачать и установить последнюю версию SSMS, см. в статье [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Когда новая версия становится доступной для скачивания, последняя версия SSMS выдает соответствующий запрос. Это позволяет всегда пользоваться обновленным ПО.

2. После установки введите в поле поиска Windows текст **Microsoft SQL Server Management Studio** и нажмите клавишу **ВВОД**, чтобы запустить приложение.

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. В диалоговом окне "Подключение к серверу" введите необходимые данные, чтобы подключиться к серверу SQL Server, используя проверку подлинности SQL Server.

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

## <a name="create-a-database-with-sample-data"></a>Создание базы данных с примерами данных

Приведенные здесь действия помогут вам создать базу данных с примерами данных, используя портал Azure. Эта база данных будет подключена к логическому серверу, созданному ранее. Если уровень служб "Базовый" недоступен в регионе, в котором создан сервер, удалите сервер и создайте его повторно в другом регионе. Инструкции по удалению приведены в последнем разделе данного руководства.

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

## <a name="view-the-database-properties"></a>Просмотр свойств базы данных

Чтобы запросить базу данных с помощью портала Azure, сделайте следующее:

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

## <a name="query-the-database-in-the-azure-portal"></a>Запрос базы данных на портале Azure

Приведенные здесь действия помогут вам запросить базу данных с помощью редактора запросов на портале Azure. Запрос отображает объекты в базе данных.

1. В колонке "Базы данных SQL" щелкните **Tools** (Инструменты) на панели инструментов.

    ![средства](./media/sql-database-get-started/tools.png)
2. В колонке Tools (Инструменты) щелкните **Редактор запросов (предварительная версия)**.

    ![редактор запросов](./media/sql-database-get-started/query-editor.png)
3. Установите флажок, чтобы подтвердить, что редактор запросов является компонентом предварительной версии, а затем нажмите кнопку **ОК**.
4. В колонке **Редактор запросов** щелкните **Вход**.

    ![колонка "Редактор запросов"](./media/sql-database-get-started/query-editor-blade.png)
5. Проверьте значения в полях "Тип авторизации" и "Вход", а затем введите пароль для этого имени входа. 

    ![имя входа редактора запросов](./media/sql-database-get-started/query-editor-login.png)
6. Нажмите кнопку **ОК**, чтобы попытаться войти.
7. Если появится окно ошибки входа с сообщением о том, что клиент не может войти, так как для используемого им IP-адреса отсутствует правило брандмауэра, скопируйте IP-адрес клиента в окне ошибки и в колонке сервера SQL Server для этой базы данных создайте правило брандмауэра на уровне сервера.

    ![ошибка редактора запросов](./media/sql-database-get-started/query-editor-error.png)
8. Повторите предыдущие 6 действий, чтобы войти в базу данных.
9. После выполнения проверки подлинности в окне запросов введите следующий запрос:

   ```select * from sys.objects```

    ![запрос редактора запросов](./media/sql-database-get-started/query-editor-query.png)
10.  Щелкните **Выполнить**.
11. Просмотрите результаты запроса в области **Результаты**.

    ![результаты редактора запросов](./media/sql-database-get-started/query-editor-results.png)

## <a name="connect-and-query-the-database-with-ssms"></a>Подключение к базе данных и выполнение запросов с помощью SSMS

Выполните следующие действия, чтобы подключиться к базе данных с помощью SQL Server Management Studio, а затем запросить примеры данных, чтобы просмотреть объекты в базе данных.

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

## <a name="create-a-blank-database-with-ssms"></a>Создание пустой базы данных с помощью SSMS

Чтобы создать новую базу данных в логическом сервере с помощью SQL Server Management Studio, сделайте следующее:

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

## <a name="troubleshoot-connectivity"></a>Устранение неполадок подключения

> [!IMPORTANT]
> Сведения об устранении проблем с подключением см. в статье [Устранение неполадок подключения к базе данных SQL Azure](sql-database-troubleshoot-common-connection-issues.md).
> 

## <a name="delete-a-single-database"></a>Удаление отдельной базы данных

Чтобы удалить отдельную базу данных с помощью портала Azure, сделайте следующее:

1. На портале Azure в колонке вашей базы данных SQL щелкните **Удалить**.

    ![кнопка удаления базы данных](./media/sql-database-get-started/delete-database.png)
2. Нажмите кнопку **Да**, чтобы подтвердить безвозвратное удаление этой базы данных.

    ![подтверждение удаления базы данных](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> В течение периода хранения базы данных ее можно восстановить, используя автоматические резервные копии, создаваемые службой. Базы данных выпуска "Базовый" можно восстановить в течение семи дней. Ни в коем случае не удаляйте сервер. Сделав это, вы не сможете восстановить ни сам север, ни его базы данных. Дополнительные сведения о резервном копировании базы данных см. в статье [Подробнее о резервном копировании базы данных SQL](sql-database-automated-backups.md), а сведения о восстановлении базы данных из резервных копий см. в статье [Восстановление базы данных SQL Azure с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md). Сведения о восстановлении удаленной базы данных см. в статье [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-portal.md).
>


## <a name="next-steps"></a>Дальнейшие действия
Завершив работу с этим руководством, изучите дополнительные материалы, в которых продолжается начатая в этом руководстве тема. 

- Основные сведения об аутентификации SQL Server см. в статье [Руководство по базам данных SQL: аутентификация, доступ и правила брандмауэра уровня базы данных в SQL Server](sql-database-control-access-sql-authentication-get-started.md).
- Основные сведения об аутентификации Azure Active Directory см. в статье [Руководство по базам данных SQL: доступ с аутентификацией Azure AD и правила брандмауэра уровня базы данных](sql-database-control-access-aad-authentication-get-started.md).
* Если вы хотите поработать с запросами к примеру базы данных на портале Azure, см. страницу [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/) (Предварительная версия: интерактивные запросы к базам данных SQL).
* Если вы знаете Excel, узнайте, как [подключиться к базе данных SQL в Azure с помощью Excel](sql-database-connect-excel.md).
* Если вы готовы написать свой собственный код, выберите язык программирования в [библиотеках подключений для базы данных SQL и SQL Server](sql-database-libraries.md).
* Если вы хотите перенести локальные базы данных SQL Server в облако Azure, см. статью [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
* Чтобы загрузить данные в новую таблицу из CSV-файла с помощью программы командной строки BCP, см. сведения в статье [Загрузка данных из CSV-файла в базу данных SQL Azure (неструктурированные файлы)](sql-database-load-from-csv-with-bcp.md).
* Сведения о том, как создавать таблицы и другие объекты, см. в разделе "Создание таблицы" в статье [Создание таблицы (Учебник)](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Дополнительные ресурсы

- Технический обзор см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).
- Сведения о ценах см. на [странице с ценами на базы данных SQL Azure](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Feb17_HO2-->



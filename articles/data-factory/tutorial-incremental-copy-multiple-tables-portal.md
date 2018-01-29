---
title: "Добавочное копирование нескольких таблиц с помощью фабрики данных Azure | Документация Майкрософт"
description: "В этом руководстве вы создадите конвейер фабрики данных Azure, который пошагово копирует разностные данные из нескольких таблиц в локальной базе данных SQL Server в базу данных SQL Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/20/2018
ms.author: jingwang
ms.openlocfilehash: c79bce401b0f1d67d7955f4c97a5dfac5008be0d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Добавочная загрузка данных из нескольких таблиц в SQL Server в базу данных SQL Azure
В этом руководстве вы создадите фабрику данных Azure с конвейером, который загружает разностные данные из нескольких таблиц локальной базы данных SQL Server в базу данных SQL Azure.    

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * подготовите исходное и конечное хранилища данных;
> * Создадите фабрику данных.
> * Создайте локальную среду выполнения интеграции.
> * Установка среды выполнения интеграции. 
> * создание связанных служб. 
> * Создание наборов данных источника, приемника и предела.
> * создадите и запустите конвейер, а также начнете его мониторинг;
> * Просмотрите результаты.
> * Добавление или обновление данных в исходных таблицах.
> * Повторный запуск конвейера и выполнение его мониторинга.
> * Просмотр окончательных результатов.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных Azure, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Обзор
Ниже приведены важные действия для создания этого решения. 

1. **Выберите столбец для предела.**
    
    Выберите один столбец из каждой таблицы в исходном хранилище данных, который можно использовать для идентификации новых или обновленных записей при каждом запуске. Как правило, данные в этом выбранном столбце (например, последнее_время_изменения или идентификатор) продолжают увеличиваться по мере создания или обновления строк. В качестве предела используется максимальное значение в этом столбце.

2. **Подготовьте хранилище данных для хранения значений предела.**   
    
    В этом руководстве вы сохраните значение предела в базе данных SQL.

3. **Создайте конвейер, следуя инструкциям ниже**. 
    
    a. Создайте действие ForEach, которое выполняет итерацию по списку имен исходной таблицы. Этот список передается в конвейер в качестве параметра. В каждой исходной таблице этот параметр вызывает следующие действия для загрузки разностных данных для этой таблицы.

    Б. Создайте два действия поиска. Используйте первое действие поиска для получения последнего значения предела, а второе для получения нового значения предела. Эти значения передаются в действие копирования.

    c. Создайте действие копирования, копирующее строки из исходного хранилища данных со значениями столбцов предела, которые выше значений старого предела и меньше значений нового. Затем оно копирует разностные данные из исходного хранилища данных в хранилище BLOB-объектов Azure в качестве нового файла.

    d. Создайте действие хранимой процедуры, которое обновляет значение предела для конвейера при последующем выполнении. 

    Ниже приведена общая схема решения. 

    ![Пошаговая загрузка данных](media\tutorial-incremental-copy-multiple-tables-portal\high-level-solution-diagram.png)


Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям
* **SQL Server.** В этом руководстве используйте локальную базу данных SQL Server в качестве исходного хранилища данных. 
* **База данных SQL Azure**. Базу данных SQL используйте в качестве принимающего хранилища данных. Если у вас нет базы данных SQL, создайте ее, следуя указаниям в статье [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md). 

### <a name="create-source-tables-in-your-sql-server-database"></a>Создание исходных таблиц в базе данных SQL Server

1. Откройте SQL Server Management Studio и подключитесь к локальной базе данных SQL Server.

2. В **обозревателе сервера** щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.

3. Выполните следующую команду SQL в базе данных, чтобы создать таблицы с именами `customer_table` и `project_table`.

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Создание целевых таблиц в базе данных SQL Azure
1. Откройте SQL Server Management Studio и подключитесь к базе данных SQL Azure.

2. В **обозревателе сервера** щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.

3. Выполните следующую команду SQL для базы данных SQL, чтобы создать таблицы с именами `customer_table` и `project_table`.  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-sql-database-to-store-the-high-watermark-value"></a>Создание дополнительной таблицы в базе данных SQL для хранения значения верхнего предела
1. Выполните указанную ниже команду SQL для базы данных SQL, чтобы создать таблицу с именем `watermarktable` для хранения значения предела. 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Вставьте исходные значения предела для обеих исходных таблиц в таблицу значений предела.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-sql-database"></a>Создание хранимой процедуры в базе данных SQL 

Выполните указанную ниже команду, чтобы создать хранимую процедуру в базе данных SQL. Эта хранимая процедура обновляет значение предела после каждого запуска конвейера. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures"></a>Создание типов данных и дополнительные хранимые процедуры
Выполните следующий запрос для создания двух хранимых процедур и двух типов данных в базе данных SQL. Они используются для объединения данных из исходных в целевые таблицы.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
2. На странице **Новая фабрика данных** введите **ADFMultiIncCopyTutorialDF** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. При возникновении указанной ниже ошибки измените имя фабрики данных (например, на ваше_имя_ADFMultiIncCopyTutorialDF) и попробуйте создать фабрику данных снова. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
3. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий.
     
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
        Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
4. Выберите **V2 (Preview)** (V2 (предварительная версия)) для **версии**.
5. Укажите **расположение** фабрики данных. В раскрывающемся списке отображаются только поддерживаемые расположения. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.
6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.      
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
   ![Домашняя страница фабрики данных](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
10. Щелкните элемент **Author & Monitor** (Создание и мониторинг), чтобы запустить на отдельной вкладке пользовательский интерфейс фабрики данных Azure.
11. На странице "Начало работы" пользовательского интерфейса фабрики данных Azure щелкните **Create pipeline** (Создание конвейера) или перейдите на вкладку **Изменить**. 

   ![Страница "Начало работы"](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Создание и настройка локальной среды выполнения интеграции
При перемещении данных из хранилища данных в частной сети (локальной) в хранилище данных Azure установите в своей локальной среде локальную среду выполнения интеграции (IR). В локальной среде выполнения интеграции данные перемещаются между частной сетью и Azure. 

1. Щелкните вкладку **Подключения** в нижней части левой панели, а затем переключитесь на вкладку **Integration Runtimes** (Среды выполнения интеграции) в окне **подключений**. 

   ![Вкладка "Подключения"](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
2. На вкладке **Integration Runtimes** (Среды выполнения интеграции) щелкните **+ Создать**. 

   ![Кнопка создания среды выполнения интеграции](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
3. В окне **Integration Runtime Setup** (Настройка среды выполнения интеграции) выберите вариант **Perform data movement and dispatch activities to external computes** (Выполнить перемещение данных и передать действия на внешние вычислительные ресурсы), затем нажмите кнопку **Далее**. 

   ![Выбор типа среды выполнения интеграции](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
4. Выберите **Частная сеть**, а затем нажмите кнопку **Далее**. 

   ![Выбор частной сети](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
5. Введите **MySelfHostedIR** для параметра **Name** (Имя) и щелкните **Next** (Далее). 

   ![Имя локальной среды выполнения интеграции](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
10. Щелкните элемент **Click here to launch the express setup for this computer** (Щелкните здесь, чтобы запустить экспресс-установку для этого компьютера) в разделе **Option 1: Express setup** (Вариант 1. Экспресс-установка). 

   ![Ссылка экспресс-установки](./media/tutorial-incremental-copy-multiple-tables-portal/click-exress-setup.png)
11. В окне **Экспресс-установка Integration Runtime (Self-hosted)** щелкните **Закрыть**. 

   ![Настройка среды выполнения интеграции — успешное завершение](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
12. В окне веб-браузера **Integration Runtime Setup** (Настройка среды выполнения интеграции) нажмите кнопку **Готово**. 

   ![Настройка среды выполнения интеграции — завершение](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
17. Проверьте, чтобы в списке сред выполнения интеграции использовалась среда **MySelfHostedIR**.

       ![Список сред выполнения интеграции](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Создание связанных служб
Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом разделе вы создадите связанные службы для локальной базы данных SQL Server и базы данных SQL. 

### <a name="create-the-sql-server-linked-service"></a>Создание связанной службы SQL Server
На этом шаге вы свяжете локальную базу данных SQL Server с фабрикой данных.

1. В окне **подключений** перейдите из вкладки **сред выполнения интеграции** на вкладку **Связанные службы**, а затем щелкните **+ Создать**.

    ![Кнопка создания связанной службы](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. В окне **New Linked Service** (Новая связанная служба) выберите **SQL Server**, а затем нажмите кнопку **Продолжить**. 

    ![Выбор SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
3. В окне **New Linked Service** (Новая связанная служба) выполните следующие действия:

    1. Введите **SqlServerLinkedService** в поле **Имя**. 
    2. Выберите **MySelfHostedIR** для параметра **Connect via integration runtime** (Подключиться через среду выполнения интеграции). Это **важный** шаг. Среда выполнения интеграции по умолчанию не может подключиться к локальному хранилищу данных. Используйте ранее созданную локальную среду выполнения интеграции. 
    3. В поле **Имя сервера** введите имя компьютера, на котором размещена база данных SQL Server.
    4. В поле **Имя базы данных** введите имя базы данных на сервере SQL Server, где содержатся исходные данные. Для работы с этим руководством вы ранее создали таблицу и вставили данные в эту базу данных. 
    5. В поле **Тип проверки подлинности** выберите **соответствующий тип**, который будет использоваться для подключения к базе данных. 
    6. В поле **Имя пользователя** введите имя пользователя, у которого есть доступ к базе данных SQL Server. Если в имени учетной записи пользователя или имени сервера необходимо использовать символ косой черты (`\`), добавьте escape-символ (`\`). Например, `mydomain\\myuser`.
    7. В поле **Пароль** введите **пароль** для этого пользователя. 
    8. Чтобы проверить, может ли фабрика данных подключиться к базе данных SQL Server, нажмите кнопку **Проверить соединение**. Устраните все ошибки для успешного подключения. 
    9. Чтобы сохранить связанную службу, щелкните **Сохранить**.

        ![Связанная служба SQL Server — параметры](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Создание связанной службы базы данных SQL Azure
На последнем шаге вы создадите связанную службу, которая свяжет исходную базу данных SQL Server с фабрикой данных. На этом шаге вы свяжете базу данных-приемник или целевую базу данных SQL Azure с фабрикой данных. 

1. В окне **подключений** перейдите из вкладки **сред выполнения интеграции** на вкладку **Связанные службы**, а затем щелкните **+ Создать**.

    ![Кнопка создания связанной службы](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. В окне **New Linked Service** (Новая связанная служба) выберите **Azure SQL Database** (База данных SQL Microsoft Azure) и щелкните **Continue** (Продолжить). 
3. В окне **New Linked Service** (Новая связанная служба) выполните следующие действия:

    1. Введите **AzureSqlDatabaseLinkedService** в поле **Имя**. 
    3. Для поля **Имя сервера** выберите в раскрывающемся списке имя сервера SQL Azure. 
    4. Для поля **Имя базы данных** выберите базу данных SQL Azure, в которой в рамках этого руководства были созданы таблицы customer_table и project_table. 
    6. В поле **Имя пользователя** введите имя пользователя, у которого есть доступ к базе данных SQL Azure. 
    7. В поле **Пароль** введите **пароль** для этого пользователя. 
    8. Чтобы проверить, может ли фабрика данных подключиться к базе данных SQL Server, нажмите кнопку **Проверить соединение**. Устраните все ошибки для успешного подключения. 
    9. Чтобы сохранить связанную службу, щелкните **Сохранить**.

        ![Параметры связанной службы SQL Azure](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
10. Вы должны увидеть в списке две связанные службы. 
   
    ![Две связанные службы](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Создание наборов данных
На этом шаге вы создадите наборы данных для представления источника данных, назначение данных и место для хранения предела.

### <a name="create-a-source-dataset"></a>Создание исходного набора данных

1. На левой панели нажмите кнопку **+ (плюс)** и выберите элемент **Набор данных**.

   ![Меню "Новый набор данных"](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. В окне **Новый набор данных** выберите **SQL Server**, а затем нажмите кнопку **Готово**. 

   ![Выбор SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
3. Для настройки набора данных в веб-браузере откроется новая вкладка. Также этот набор данных появится в представлении TreeView. На вкладке **Общие** окна свойств ниже введите **SourceDataset** в поле **Имя**. 

   ![Исходный набор данных — имя](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
4. Щелкните вкладку **Подключение** в окне "Свойства", а затем выберите **SqlServerLinkedService** для **связанной службы**. Поле таблицы ниже оставьте пустым. Действие копирования в конвейере использует SQL-запрос для загрузки данных, вместо того чтобы загружать всю таблицу.

   ![Раздел исходного набора данных, вкладка "Подключение"](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Создание набора данных приемника
1. На левой панели нажмите кнопку **+ (плюс)** и выберите элемент **Набор данных**.

   ![Меню "Новый набор данных"](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. В окне **Новый набор данных** выберите **База данных SQL Microsoft Azure** и щелкните **Готово**. 

   ![Выбор Базы данных SQL Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. Для настройки набора данных в веб-браузере откроется новая вкладка. Также этот набор данных появится в представлении TreeView. На вкладке **Общие** окна свойств ниже введите **SinkDataset** (Набор данных приемника) в поле **Имя**.

   ![Вкладка "Общие", имя набора данных приемника](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
4. Щелкните вкладку **Подключение** в окне "Свойства", а затем выберите **AzureSqlLinkedService** для **связанной службы**. 

   ![Вкладка "Подключение" целевого набора данных](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
5. Щелкните вкладку **Параметры** в окне "Свойства" и выполните следующее: 

    1. Нажмите кнопку **+ Создать** в разделе **создания и обновления параметров**. 
    2. Введите **SinkTableName** в поле **Имя**, а для параметра **Тип** выберите значение **Строка**. В этом наборе данных **имя целевой таблицы** используется как параметр. Параметр SinkTableName динамически задается конвейером в среде выполнения. Действие ForEach в конвейере выполняет итерацию по списку имен таблиц и передает имя таблицы для этого набора данных в каждой итерации.
    3. В разделе **Parameterized properties** (Параметризованные свойства) введите `@{dataset().SinkTableName}` в качестве значения для свойства **tableName**. Используйте значение, передаваемое параметру **SinkTableName** для инициализации свойства **tableName** набора данных. 

       ![Свойства целевого набора данных](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)

### <a name="create-a-dataset-for-a-watermark"></a>Создание набора данных для предела
На этом шаге вы создадите набор данных для хранения значения верхнего предела. 

1. На левой панели нажмите кнопку **+ (плюс)** и выберите элемент **Набор данных**.

   ![Меню "Новый набор данных"](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. В окне **Новый набор данных** выберите **База данных SQL Microsoft Azure** и щелкните **Готово**. 

   ![Выбор Базы данных SQL Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. На вкладке **Общие** окна свойств ниже введите **WatermarkDataset** в поле **Имя**.
4. Перейдите на вкладку **Подключения** и выполните следующие действия: 

    1. Выберите **AzureSqlDatabaseLinkedService** в списке **Связанная служба**.
    2. Выберите **[dbo].[watermarktable]** в списке **Таблица**.

       ![Вкладка "Подключение" набора данных для нижнего предела](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Создание конвейера
Этот конвейер принимает список имен таблиц в качестве параметра. Действие ForEach выполняет итерацию по списку имен таблиц, а затем выполняет следующие операции. 

1. Использует действие поиска, чтобы получить старое значение предела (начальное значение или значение, используемое в последней итерации).

2. Использует действие поиска, чтобы получить новое значение предела (максимальное значение в столбце предела в исходной таблице).

3. Использует действие копирования, чтобы скопировать данные между двумя значениями пределов из исходной в целевую базу данных.

4. Использует действие хранимой процедуры, чтобы обновить старое значение предела для использования на первом шаге следующей итерации. 

### <a name="create-the-pipeline"></a>Создание конвейера

1. На левой панели нажмите кнопку **+ (плюс)** и выберите пункт **Конвейер**.

    ![Меню создания конвейера](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
2. На вкладке **Общие** в окне **Свойства** введите значение **IncrementalCopyPipeline** в поле **Имя**. 

    ![Имя конвейера](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
3. В окне **Свойства** сделайте следующее: 

    1. Щелкните **+ Создать**. 
    2. Введите **tableList** в качестве **имени** параметра. 
    3. Для параметра **типа** выберите значение **Объект**.

    ![Параметры конвейера](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
4. Перетащите элемент **ForEach** с панели элементов **Действия** в область конструктора конвейера. На вкладке **Общие** окна **Свойства** введите значение **IterateSQLTables**. 

    ![Имя действия ForEach](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
5. Перейдите на вкладку **Настройки** в окне **Свойства** и введите `@pipeline().parameters.tableList` для параметра **Элементы**. Действие ForEach выполняет итерацию по списку таблиц, а затем выполняет операцию добавочного копирования. 

    ![Настройки действия ForEach](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
6. Выберите в конвейере действие **ForEach**. Нажмите кнопку **редактирования (значок карандаша)**.

    ![Кнопка редактирования действия ForEach](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
7. Перетащите действие **Поиск** с панели элементов **Действия** и введите **LookupOldWaterMarkActivity** в поле **Имя**.

    ![Имя первого действия поиска](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
8. Перейдите на вкладку **Настройки** в окне **Свойства** и сделайте следующее: 

    1. Выберите **WatermarkDataset** в поле **Source Dataset** (Исходный набор данных).
    2. Выберите **Запрос** в списке **Use Query** (Пользовательский запрос). 
    3. Введите следующий запрос SQL в поле **Запрос**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Настройки первого действия поиска](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
9. Перетащите действие **Поиск** с панели элементов **Действия** и введите **LookupNewWaterMarkActivity** в поле **Имя**.
        
    ![Имя второго действия поиска](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
10. Переключитесь на вкладку **Параметры** .

    1. Выберите **SourceDataset** в поле **Source Dataset** (Исходный набор данных). 
    2. Выберите **Запрос** в списке **Use Query** (Пользовательский запрос).
    3. Введите следующий запрос SQL в поле **Запрос**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Настройки второго действия поиска](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
11. Перетащите действие **Копирование** с панели элементов **Действия** и введите **IncrementalCopyActivity** в поле **Имя**. 

    ![Действие копирования — имя](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
12. Поочередно подключите действия **поиска** к действию **копирования**. Для этого нужно перетащить **зеленые** квадраты возле полей с действиями **поиска** к действию **копирования**. Когда цвет границы для действия копирования изменится на **синий**, отпустите кнопку мыши.

    ![Подключение действий поиска к действию копирования](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
13. Выберите действие **копирования** в конвейере. Перейдите на вкладку **Источник** в окне **Свойства**. 

    1. Выберите **SourceDataset** в поле **Source Dataset** (Исходный набор данных). 
    2. Выберите **Запрос** в списке **Use Query** (Пользовательский запрос). 
    3. Введите следующий запрос SQL в поле **Запрос**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Действие копирования — настройки источника](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
14. Перейдите на вкладку **Приемник** и выберите **SinkDataset** в поле **Sink Dataset** (Целевой набор данных). 
        
    ![Действие копирования — настройки приемника](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
15. Перейдите на вкладку **Параметры** и сделайте следующее:

    1. В поле свойства **Sink Stored Procedure Name** (Имя хранимой процедуры приемника) введите `@{item().StoredProcedureNameForMergeOperation}`.
    2. В поле свойства **Sink Table Type** (Тип таблицы приемника) введите `@{item().TableType}`.
    3. В разделе **Набор данных приемника** для параметра **SinkTableName** введите `@{item().TABLE_NAME}`.

        ![Параметры действия копирования](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
16. Перетащите действие **Хранимая процедура** с панели элементов **Действия** в область конструктора конвейера. Подключите действие **копирования** к действию **Хранимая процедура**. 

    ![Параметры действия копирования](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
17. Выберите в конвейере действие **Хранимая процедура** и введите **StoredProceduretoWriteWatermarkActivity** в поле **Имя** на вкладке **Общие** окна **Свойства**. 

    ![Действие хранимой процедуры — имя](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
18. Перейдите на вкладку **Учетная запись SQL** и выберите **AzureSqlDatabaseLinkedService** в списке **Связанная служба**.

    ![Действие хранимой процедуры — учетная запись SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
19. Перейдите на вкладку **Хранимая процедура** и выполните здесь следующие действия:

    1. В поле **имени хранимой процедуры** введите `sp_write_watermark`. 
    2. Нажмите кнопку **+ Создать**, чтобы добавить параметры ниже. 

        | ИМЯ | type | Значение | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Строка | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Действие хранимой процедуры — параметры хранимой процедуры](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
20. Нажмите кнопку **Опубликовать** в области слева. С помощью этого действия выполнится публикация созданных сущностей в службу фабрики данных. 

    ![Кнопка "Опубликовать"](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
21. Дождитесь сообщения **Successfully published** (Публикация выполнена). Чтобы просмотреть уведомления, щелкните ссылку **Показать уведомления**. Чтобы закрыть окно уведомлений, щелкните значок **X**.

    ![Отображение уведомлений](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Запуск конвейера

1. Щелкните **Триггер** на панели инструментов конвейера, а затем — **Trigger Now** (Активировать сейчас).     

    ![Trigger Now (Активировать сейчас)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
2. В окне **запуска конвейера** для параметра **tableList** введите значение ниже, а затем нажмите кнопку **Готово**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

    ![Аргументы окна запуска конвейера](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Мониторинг конвейера

1. Перейдите на вкладку **Мониторинг** слева. Вы увидите выполнение конвейера, активированное**вручную**. Нажмите кнопку **Обновить**, чтобы обновить этот список. Ссылки в столбце **Действия** дают возможность просмотреть запуски действий, связанные с этим запуском конвейера, а также повторно запустить конвейер. 

    ![Запуски конвейера](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Щелкните ссылку **View Activity Runs** (Просмотр запусков действий) в столбце **Действия**. Вы увидите все запуски действия, связанные с выбранным запуском конвейера. 

    ![Выполнение действия](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Просмотр результатов
В SQL Server Management Studio выполните следующие запросы в целевой базе данных SQL. Так вы проверите, что данные скопированы из исходных таблиц в целевые. 

**Запрос** 
```sql
select * from customer_table
```

**Выходные данные**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Запрос**

```sql
select * from project_table
```

**Выходные данные**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Запрос**

```sql
select * from watermarktable
```

**Выходные данные**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Обратите внимание, что значения предела для обеих таблиц обновились. 

## <a name="add-more-data-to-the-source-tables"></a>Добавление данных в исходные таблицы

Выполните следующий запрос в исходной базе данных SQL Server, чтобы обновить имеющуюся строку в таблице customer_table. Вставьте новую строку в таблицу project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Повторный запуск конвейера
1. В окне веб-браузера перейдите на вкладку **редактирования** в области слева. 
2. Щелкните **Триггер** на панели инструментов конвейера, а затем — **Trigger Now** (Активировать сейчас).   

    ![Trigger Now (Активировать сейчас)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. В окне **запуска конвейера** для параметра **tableList** введите значение ниже, а затем нажмите кнопку **Готово**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline"></a>Мониторинг конвейера

1. Перейдите на вкладку **Мониторинг** слева. Вы увидите выполнение конвейера, активированное**вручную**. Нажмите кнопку **Обновить**, чтобы обновить этот список. Ссылки в столбце **Действия** дают возможность просмотреть запуски действий, связанные с этим запуском конвейера, а также повторно запустить конвейер. 

    ![Запуски конвейера](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Щелкните ссылку **View Activity Runs** (Просмотр запусков действий) в столбце **Действия**. Вы увидите все запуски действия, связанные с выбранным запуском конвейера. 

    ![Выполнение действия](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Просмотр окончательных результатов
В SQL Server Management Studio выполните следующие запросы в целевой базе данных SQL Azure. Так вы проверите, что обновленные и новые данные скопированы из исходных таблиц в целевые. 

**Запрос** 
```sql
select * from customer_table
```

**Выходные данные**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Обратите внимание на новые значения в столбцах **Name** и **LastModifytime** для строки под номером 3 в столбце **PersonID**. 

**Запрос**

```sql
select * from project_table
```

**Выходные данные**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Обратите внимание, что запись **NewProject** добавлена в таблицу project_table. 

**Запрос**

```sql
select * from watermarktable
```

**Выходные данные**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Обратите внимание, что значения предела для обеих таблиц обновились.
     
## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы выполнили следующие шаги: 

> [!div class="checklist"]
> * подготовите исходное и конечное хранилища данных;
> * Создадите фабрику данных.
> * Создание локальной среды выполнения интеграции (IR).
> * Установка среды выполнения интеграции.
> * создание связанных служб. 
> * Создание наборов данных источника, приемника и предела.
> * создадите и запустите конвейер, а также начнете его мониторинг;
> * Просмотрите результаты.
> * Добавление или обновление данных в исходных таблицах.
> * Повторный запуск конвейера и выполнение его мониторинга.
> * Просмотр окончательных результатов.

Перейдите к следующему руководству, чтобы узнать о преобразовании данных с помощью кластера Spark в Azure:

> [!div class="nextstepaction"]
>[Добавочная загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure с использованием сведений об отслеживании изменений](tutorial-incremental-copy-change-tracking-feature-portal.md)



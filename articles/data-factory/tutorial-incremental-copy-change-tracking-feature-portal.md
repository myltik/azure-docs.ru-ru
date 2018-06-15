---
title: Добавочное копирование данных с помощью решения "Отслеживание изменений" | Документация Майкрософт
description: 'В этом руководстве вы создадите конвейер фабрики данных Azure, который пошагово копирует разностные данные из нескольких таблиц в локальной базе данных SQL Server в базу данных SQL Azure. '
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/12/2018
ms.author: jingwang
ms.openlocfilehash: 891dad1a481c966e6ea1771f3e7c7850fa429352
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189874"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Добавочная загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure с использованием сведений об отслеживания изменений 
Из этого руководстве вы узнаете, как создать фабрику данных Azure с конвейером, который копирует разностные данные на основе сведений об **отслеживании изменений** в базе данных-источнике SQL Azure в хранилище BLOB-объектов Azure.  

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * подготовите исходное хранилище данных;
> * создадите фабрику данных;
> * создадите связанные службы; 
> * создадите источник, приемник и наборы данных отслеживания изменений;
> * создадите, запустите и начнете мониторинг конвейера полного копирования;
> * добавите или обновите данные в исходной таблице;
> * создаете, запустите и начнете мониторинг конвейера добавочного копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Обзор
В решениях для интеграции данных после начальной загрузки данных широко используется добавочная загрузка. В некоторых случаях измененные за определенный период времени данные в исходном хранилище данных можно легко разделить на такие срезы, как LastModifyTime или CreationTime. В некоторых случаях определить разностные данные, полученные в ходе последней обработки данных, нет возможности. Для этого используется технология "Отслеживание изменений", поддерживаемая такими хранилищами данных, как База данных Azure SQL и SQL Server.  В этом руководстве описано, как использовать фабрику данных Azure версии 2 для работы с технологией "Отслеживание изменений" (SQL) для добавочной загрузки разностных данных из базы данных SQL Azure в хранилище BLOB-объектов Azure.  Дополнительные сведения о технологии "Отслеживание изменений" (SQL) см. в статье [Об отслеживании изменений (SQL Server)](/sql/relational-databases/track-changes/about-change-tracking-sql-server). 

## <a name="end-to-end-workflow"></a>Комплексный рабочий процесс
Ниже описан стандартный рабочий процесс по добавочной загрузке данных с помощью технологии "Отслеживание изменений".

> [!NOTE]
> И База данных SQL Azure, и SQL Server поддерживают технологию "Отслеживание изменений". В этом руководстве в качестве исходного хранилища данных используется База данных Azure SQL. Вы также можете использовать локальную среду SQL Server. 

1. **Начальная загрузка данных журнала** (выполняется однократно):
    1. Включите технологию "Отслеживание изменений" в исходной базе данных Azure SQL.
    2. Получите начальное значение SYS_CHANGE_VERSION в базе данных Azure SQL в качестве базовых показателей для записи измененных данных.
    3. Загрузите все данные из базы данных SQL Azure в хранилище BLOB-объектов Azure. 
2. **Добавочная загрузка разностных данных по расписанию** (выполняется периодически после начальной загрузки данных):
    1. Получите старое и новое значения SYS_CHANGE_VERSION.
    3. Загрузите разностные данные, присоединив первичные ключи измененных строк (для двух значений SYS_CHANGE_VERSION) из таблицы **sys.change_tracking_tables**, с данными в **исходной таблице**, а затем переместите разностные данные в целевое хранилище.
    4. Обновите SYS_CHANGE_VERSION для следующей загрузки разностных данных.

## <a name="high-level-solution"></a>Общее решение
В этом руководстве описано, как создать два конвейера, которые выполняют следующие две операции:  

1. **Начальная загрузка** — вы создадите конвейер с действием копирования, которое копирует все данные из исходного хранилища данных (база данных Azure SQL) в целевое хранилище данных (хранилище BLOB-объектов Azure).

    ![Полная загрузка данных](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Добавочная загрузка** — вы создадите конвейер со следующими действиями для периодического выполнения: 
    1. Создайте **два действия поиска**, чтобы получить старое и новое значения SYS_CHANGE_VERSION из базы данных SQL Azure и передать их в действие копирования.
    2. Создайте **одно действие копирования**, чтобы скопировать вставленные, обновленные или удаленные данные, связанные с двумя значениями SYS_CHANGE_VERSION, из базы данных SQL Azure в хранилище BLOB-объектов.
    3. Создайте **одно действие хранимой процедуры**, чтобы обновить значение SYS_CHANGE_VERSION для запуска следующего конвейера.

    ![Схема последовательности добавочной загрузки](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям
* **База данных SQL Azure**. Используйте базу данных как **исходное** хранилище данных. Если у вас нет базы данных SQL Azure, вы можете создать ее, выполнив шаги из статьи [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md).
* **Учетная запись хранения Azure.** В этом руководстве в качестве **приемника** будет использоваться хранилище BLOB-объектов. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). Создайте контейнер с именем **adftutorial**. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Создание таблицы источника данных в базе данных SQL Azure
1. Запустите **SQL Server Management Studio** и подключитесь к Azure SQL Server. 
2. В **обозревателе сервера** щелкните правой кнопкой мыши **базу данных** и выберите **Создать запрос**.
3. Выполните указанную ниже команду SQL для базы данных SQL Azure, чтобы создать таблицу с именем `data_source_table` в качестве хранилища источника данных.  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Включите функцию **Отслеживание изменений** для базы данных и исходной таблицы (data_source_table), выполнив следующий SQL-запрос: 

    > [!NOTE]
    > - Замените &lt;имя базы данных&gt; именем базы данных Azure SQL, которая содержит таблицу data_source_table. 
    > - Измененные данные в текущем примере хранятся в течение двух дней. Если загружать измененные данные каждые три дня или реже, некоторые измененные данные не будут включены.  В таком случае вам нужно увеличить значение CHANGE_RETENTION. В противном случае нужно следить за тем, чтобы период для загрузки измененных данных не превышал два дня. Дополнительные сведения см. в разделе [Включение отслеживания изменений для базы данных](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database).
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Создайте таблицу и сохраните стандартное значение ChangeTracking_version, выполнив следующий запрос: 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > Если после включения отслеживания изменений для базы данных SQL данные не меняются, значение ChangeTracking_version будет равно 0.
6. Выполните следующий запрос, чтобы создать хранимую процедуру в базе данных SQL Azure. Конвейер вызывает эту хранимую процедуру для обновления значения ChangeTracking_version в таблице, созданной на предыдущем шаге. 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Чтобы установить модули Azure PowerShell, выполните инструкции из статьи [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Создать фабрику данных

1. Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных.
1. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory-menu.png)
2. На странице **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. При возникновении указанной ниже ошибки измените имя фабрики данных (например, на ваше_имя_ADFTutorialDataFactory) и попробуйте создать фабрику данных снова. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".
  
       `Data factory name “ADFTutorialDataFactory” is not available`
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

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
   ![Домашняя страница фабрики данных](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Щелкните плитку **Создание и мониторинг**, чтобы открыть на отдельной вкладке пользовательский интерфейс фабрики данных Azure.
11. На странице **начала работы** откройте вкладку **Изменить** на панели слева, как показано на следующем рисунке: 

    ![Кнопка создания конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Создание связанных служб
Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом разделе вы создадите связанные службы для учетной записи хранения Azure и базы данных SQL Azure. 

### <a name="create-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure
На этом шаге вы свяжете учетную запись хранения Azure с фабрикой данных.

1. Нажмите кнопку **Подключения**, а затем **+ Создать**.

   ![Кнопка создания подключения](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. В окне **New Linked Service** (Новая связанная служба) выберите **хранилище BLOB-объектов Azure** и щелкните **Continue** (Продолжить). 

   ![Выбор хранилища BLOB-объектов Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. В окне **New Linked Service** (Новая связанная служба) выполните следующие действия: 

    1. Введите **AzureStorageLinkedService** в поле **имени**. 
    2. Выберите учетную запись хранения в списке **Storage account name** (Имя учетной записи хранения). 
    3. Выберите команду **Сохранить**. 
    
   ![Параметры учетной записи хранения Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Создание связанной службы базы данных SQL Azure
На этом шаге вы свяжете базу данных SQL Azure с фабрикой данных.

1. Нажмите кнопку **Подключения**, а затем **+ Создать**.
2. В окне **New Linked Service** (Новая связанная служба) выберите **Azure SQL Database** (База данных SQL Microsoft Azure) и щелкните **Continue** (Продолжить). 
3. В окне **New Linked Service** (Новая связанная служба) выполните следующие действия: 

    1. Введите **AzureSqlDatabaseLinkedService** в поле **имени**. 
    2. Выберите нужный сервер Azure SQL в поле **Server name** (Имя сервера).
    4. Выберите базу данных Azure SQL в поле **Database name** (Имя базы данных). 
    5. Введите имя пользователя в поле **User name** (Имя пользователя). 
    6. Введите пароль для этого пользователя в поле **Password** (Пароль). 
    7. Нажмите кнопку **Test connection** (Проверить подключение), чтобы проверить подключение.
    8. Нажмите кнопку **Save** (Сохранить), чтобы сохранить связанную службу. 
    
       ![Настройки связанной службы базы данных SQL Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Создание наборов данных
На этом шаге вы создадите наборы данных, которые представляют данные источника и приемника, а также передадите в хранилище значение SYS_CHANGE_VERSION.

### <a name="create-a-dataset-to-represent-source-data"></a>Создание набора данных для представления исходных данных 
На этом шаге вы создадите набор данных для представления исходных данных. 

1. В представлении в виде дерева щелкните значок **+ (плюс)** и выберите вариант **Набор данных**. 

   ![Меню "Новый набор данных"](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Выберите **База данных SQL Azure** и щелкните **Готово**. 

   ![Тип исходного набора данных — база данных SQL Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Вы увидите новую вкладку для настройки набора данных. Также этот набор данных появится в представлении в виде дерева. В окне **Свойства** введите имя **SourceDataset** для нового набора данных.

   ![Имя исходного набора данных](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Перейдите на вкладку **Подключения** и выполните следующие действия: 
    
    1. Выберите **AzureSqlDatabaseLinkedService** в списке **Связанная служба**. 
    2. Выберите **[dbo].[ data_source_table]** в списке **Таблица**. 

   ![Подключение к источнику](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Создайте набор данных, который будет представлять данные для копирования в целевое хранилище данных. 
На этом шаге вы создадите набор данных для представления данных, которые копируются из исходного хранилища данных. Для работы с этим руководством нужен контейнер adftutorial, созданный в хранилище BLOB-объектов Azure. Создайте контейнер (если его еще нет) или присвойте ему имя имеющегося контейнера. В этом руководстве имя файла выходных данных создается динамически с помощью выражения `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

1. В представлении в виде дерева щелкните значок **+ (плюс)** и выберите вариант **Набор данных**. 

   ![Меню "Новый набор данных"](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Выберите **Хранилище BLOB-объектов** и щелкните **Готово**. 

   ![Тип целевого набора данных — хранилище BLOB-объектов Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Вы увидите новую вкладку для настройки набора данных. Также этот набор данных появится в представлении в виде дерева. В окне **Свойства** введите имя **SinkDataset** для нового набора данных.

   ![Целевой набор данных — имя](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Перейдите на вкладку **Подключения** в окне свойств и выполните здесь следующие действия.

    1. Выберите **AzureStorageLinkedService** в списке **Связанная служба**.
    2. Введите значение **adftutorial/incchgtracking** в качестве имени **папки** для параметра **filePath** (Путь к файлу).
    3. Введите значение **@CONCAT('Incremental-', pipeline().RunId, '.txt')** в качестве имени **файла** для параметра **filePath** (Путь к файлу).  

       ![Целевой набор данных — подключение](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Создание набора данных, который будет представлять данные отслеживания изменений 
На этом шаге вы создадите набор данных для хранения значения ChangeTracking_version.  Таблица table_store_ChangeTracking_version входит в список необходимых компонентов.

1. В представлении в виде дерева щелкните значок **+ (плюс)** и выберите вариант **Набор данных**. 
2. Выберите **База данных SQL Azure** и щелкните **Готово**. 
3. Вы увидите новую вкладку для настройки набора данных. Также этот набор данных появится в представлении в виде дерева. В окне **Свойства** введите имя **ChangeTrackingDataset** для нового набора данных.
4. Перейдите на вкладку **Подключения** и выполните здесь следующие действия. 
    
    1. Выберите **AzureSqlDatabaseLinkedService** в списке **Связанная служба**. 
    2. Выберите **[dbo]. [table_store_ChangeTracking_version]** в списке **Таблица**. 

## <a name="create-a-pipeline-for-the-full-copy"></a>Создание конвейера для полного копирования данных
На этом этапе вы создадите конвейер с действием копирования, которое копирует все данные из исходного хранилища данных (база данных Azure SQL) в целевое хранилище данных (хранилище BLOB-объектов Azure).

1. Щелкните значок **+ (плюс)** на панели слева и выберите **Конвейер**. 

    ![Меню создания конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. Вы увидите новую вкладку для настройки конвейера. Также этот конвейер появится в отображении дерева. В окне **Свойства** укажите имя **FullCopyPipeline** для нового конвейера.

    ![Меню создания конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. На панели элементов **Действия** разверните узел **Поток данных** и перетащите действие **Копирование** в область конструктора конвейера, затем присвойте этому действию имя **FullCopyActivity**. 

    ![Действие полного копирования — имя](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Перейдите на вкладку **Источник** и выберите **SourceDataset** в поле **Source Dataset** (Исходный набор данных). 

    ![Действие копирования — источник](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Перейдите на вкладку **Приемник** и выберите **SinkDataset** в поле **Sink Dataset** (Целевой набор данных). 

    ![Действие копирования — приемник](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. Чтобы проверить определение конвейера, щелкните **Проверка** на панели инструментов. Убедитесь, что проверка завершается без ошибок. Закройте **отчет о проверке конвейера**, щелкнув **>>**. 

    ![Проверка конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Чтобы опубликовать сущности (связанные службы, наборы данных и конвейеры), щелкните **Опубликовать**. Подождите, пока публикация успешно завершится. 

    ![Кнопка "Опубликовать"](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Дождитесь сообщения **Successfully published** (Публикация выполнена). 

    ![Публикация выполнена](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Вы можете просмотреть уведомления, нажав слева кнопку **Показать уведомления**. Чтобы закрыть окно уведомлений, щелкните значок **X**.

    ![Показать уведомления](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Запуск конвейера полного копирования
Щелкните **Триггер** на панели инструментов конвейера, а затем **Trigger Now** (Активировать сейчас). 

![Меню Trigger Now (Активировать сейчас)](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Мониторинг конвейера полного копирования

1. Щелкните вкладку **Мониторинг** слева. В открывшемся списке вы увидите запуск конвейера и его текущее состояние. Чтобы обновить этот список, щелкните **Обновить**. Ссылки в столбце действий позволяют просмотреть выполнения действий, связанных с этим запуском конвейера, и (или) повторно запустить конвейер. 

    ![Запуски конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. Чтобы просмотреть сведения о выполнении действий, связанных с этим запуском конвейера, щелкните ссылку **View Activity Runs** (Просмотр сведений о выполнении действий) в столбце **Действия**. В этом конвейере определено только одно действие, поэтому в списке вы увидите только одну запись. Чтобы вернуться к представлению запусков конвейера, щелкните ссылку **Pipelines** (Конвейеры) в верхней части окна. 

    ![Выполнение действия](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Просмотр результатов
Вы увидите файл с именем `incremental-<GUID>.txt` в папке `incchgtracking` контейнера `adftutorial`. 

![Выходной файл, полученный в результате полного копирования](media\tutorial-incremental-copy-change-tracking-feature-portal\full-copy-output-file.png)

Файл должен включать данные из базы данных Azure SQL:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Добавление данных в исходную таблицу

Выполните следующий запрос к базе данных Azure SQL, чтобы добавить и обновить строку. 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>Создание конвейера для копирования разностных данных
В этом шаге вы создадите конвейер со следующими действиями для периодического выполнения: **Действия поиска** получают старое и новое значения SYS_CHANGE_VERSION из базы данных SQL Azure и передают их в действие копирования. **Действие копирования** копирует вставленные, обновленные или удаленные данные, связанные с двумя значениями SYS_CHANGE_VERSION, из базы данных SQL Azure в хранилище BLOB-объектов. **Действие хранимой процедуры** обновляет значение SYS_CHANGE_VERSION для запуска следующего конвейера.

1. В пользовательском интерфейсе фабрики данных переключитесь на вкладку **Правка**. Щелкните значок **+ (плюс)** в левой панели и выберите **Конвейер**. 

    ![Меню создания конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. Вы увидите новую вкладку для настройки конвейера. Также этот конвейер появится в отображении дерева. В окне **Свойства** укажите имя **IncrementalCopyPipeline** для нового конвейера.

    ![Имя конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. Разверните элемент **Общие** на панели **Действия** и перетащите действие **Поиск** в область конструктора конвейера. Задайте для этого действия имя **LookupLastChangeTrackingVersionActivity**. Это действие возвращает версию отслеживания изменений, использованную для последней операции копирования, которая хранится в таблице **table_store_ChangeTracking_version**.

    ![Действие поиска — имя](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. Перейдите на вкладку **Настройки** в окне **Свойства** и выберите **ChangeTrackingDataset** в поле **Source Dataset** (Исходный набор данных). 

    ![Действие поиска — настройки](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Перетащите элемент **Поиск** с панели элементов **Действия** в область конструктора конвейера. Задайте для этого действия имя **LookupCurrentChangeTrackingVersionActivity**. Это действие возвращает текущую версию отслеживания изменений.

    ![Действие поиска — имя](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. Перейдите на вкладку **Настройки** в окне **Свойства** и выполните здесь следующие действия.

    1. Выберите **SourceDataset** в поле **Source Dataset** (Исходный набор данных).
    2. Выберите **Запрос** в списке **Use Query** (Пользовательский запрос). 
    3. Введите следующий запрос SQL в поле **Запрос**. 

        ```sql
        SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
        ```

    ![Действие поиска — настройки](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. На панели элементов **Действия** разверните узел **Поток данных** и перетащите действие **Копирование** в область конструктора конвейера. Присвойте этому действию имя **IncrementalCopyActivity**. Это действие копирует в целевое хранилище данных все данные, изменившиеся за период между предыдущей и текущей версиями отслеживания изменений. 

    ![Действие копирования — имя](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. Перейдите на вкладку **Источник** в окне **Свойства** и выполните здесь следующие действия.

    1. Выберите **SourceDataset** в поле **Source Dataset** (Исходный набор данных). 
    2. Выберите **Запрос** в списке **Use Query** (Пользовательский запрос). 
    3. Введите следующий запрос SQL в поле **Запрос**. 

        ```sql
        select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
        ```
    
    ![Действие копирования — настройки источника](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Перейдите на вкладку **Приемник** и выберите **SinkDataset** в поле **Sink Dataset** (Целевой набор данных). 

    ![Действие копирования — настройки приемника](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. Поочередно **соедините оба действия поиска с действием копирования**. Перетащите **зеленую кнопку**, присоединенную к действию **Поиск**, на блок действия **Копирование**. 

    ![Соединение действий поиска и копирования](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Перетащите действие **Хранимая процедура** с панели элементов **Действия** в область конструктора конвейера. Присвойте новому действию имя **StoredProceduretoUpdateChangeTrackingActivity**. Это действие обновляет версию отслеживания изменений в таблице **table_store_ChangeTracking_version**.

    ![Действие хранимой процедуры — имя](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Перейдите на вкладку *Учетная запись SQL* и выберите **AzureSqlDatabaseLinkedService** в списке **Связанная служба**. 

    ![Действие хранимой процедуры — учетная запись SQL](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Перейдите на вкладку **Хранимая процедура** и выполните здесь следующие действия: 

    1. Укажите **Update_ChangeTracking_Version** в качестве **имени хранимой процедуры**.  
    2. Выберите **Параметр импорта**. 
    3. В разделе **Параметры хранимой процедуры** укажите следующие значения параметров: 

        | ИМЯ | type | Значение | 
        | ---- | ---- | ----- | 
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} | 
        | TableName | Строка | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} | 
    
        ![Действие хранимой процедуры — параметры](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Соедините действие копирования с действием хранимой процедуры**. Перетащите **зеленую кнопку**, присоединенную к действию "Копирование", на блок действия "Хранимая процедура". 

    ![Соединение действий копирования и хранимой процедуры](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Нажмите кнопку **Проверить** на панели инструментов. Убедитесь, что проверка завершается без ошибок. Закройте **окно отчета о проверке конвейера**, щелкнув **>>**. 

    ![Кнопка проверки](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16.  Опубликуйте сущности (связанные службы, наборы данных и конвейеры) в службе фабрики данных, нажав кнопку **Опубликовать все**. Дождитесь сообщения **Публикация успешно выполнена**. 

        ![Кнопка "Опубликовать"](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Запуск конвейера добавочного копирования
1. Щелкните **Триггер** на панели инструментов конвейера, а затем **Trigger Now** (Активировать сейчас). 

    ![Меню Trigger Now (Активировать сейчас)](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. На странице **Запуск конвейера** нажмите кнопку **Готово**.

### <a name="monitor-the-incremental-copy-pipeline"></a>Мониторинг конвейера добавочного копирования
1. Щелкните вкладку **Мониторинг** слева. В открывшемся списке вы увидите запуск конвейера и его текущее состояние. Чтобы обновить этот список, щелкните **Обновить**. Ссылки в столбце **Действия** позволяют просмотреть запуски действий, связанные с этим запуском конвейера, и (или) повторно запустить конвейер. 

    ![Запуски конвейера](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. Чтобы просмотреть сведения о выполнении действий, связанных с этим запуском конвейера, щелкните ссылку **View Activity Runs** (Просмотр сведений о выполнении действий) в столбце **Действия**. В этом конвейере определено только одно действие, поэтому в списке вы увидите только одну запись. Чтобы вернуться к представлению запусков конвейера, щелкните ссылку **Pipelines** (Конвейеры) в верхней части окна. 

    ![Выполнение действия](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Просмотр результатов
Вы увидите второй файл в папке `incchgtracking` контейнера `adftutorial`. 

![Выходной файл, полученный в результате добавочного копирования](media\tutorial-incremental-copy-change-tracking-feature-portal\incremental-copy-output-file.png)

Файл должен включать только разностные данные из базы данных Azure SQL. Запись с `U` — это обновленная строка в базе данных, а `I` — добавленная строка. 

```
1,update,10,2,U
6,new,50,1,I
```
Первые три столбца — это измененные данные из таблицы data_source_table. Последние два столбцы — это метаданные из таблицы sys.change_tracking_tables. Четвертый столбец — это версия SYS_CHANGE_VERSION для каждой изменившейся строки. Пятый столбец — это операция U = update, I = insert.  Дополнительные сведения об отслеживании изменений см. в описании функции [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql). 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>Дополнительная информация
Перейдите к следующему руководству, чтобы узнать о преобразовании данных с помощью кластера Spark в Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using Spark activity in Azure Data Factory](tutorial-transform-data-spark-portal.md) (Преобразование данных в облаке с помощью действий Spark в фабрике данных Azure)




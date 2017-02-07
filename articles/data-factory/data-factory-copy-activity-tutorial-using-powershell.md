---
title: "Руководство по созданию конвейера для переноса данных с помощью Azure PowerShell | Документация Майкрософт"
description: "В этом руководстве вы создадите конвейер фабрики данных Azure с действием копирования с помощью Azure PowerShell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/06/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b16e7e221853f5d44248430cef2296301a09d497
ms.openlocfilehash: 9ff8fec2daf9fc69377891b85b0a81e9e2a54536


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Руководство по созданию конвейера фабрики данных для переноса данных с помощью Azure PowerShell
> [!div class="op_single_selector"]
> * [Обзор и предварительные требования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)
> * [Портал Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Шаблон Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [ИНТЕРФЕЙС REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
>
>

В этом руководстве описывается создание и отслеживание экземпляра фабрики данных Azure с помощью командлетов Azure PowerShell. Конвейер в фабрике данных, который вы создадите в ходе изучения этого руководства, копирует данные из двоичного объекта Azure в Базу данных SQL Azure с помощью действия копирования.

Действие копирования перемещает данные в фабрике данных. Это действие выполняется с помощью глобально доступной службы, обеспечивающей безопасное, надежное и масштабируемое копирование данных между разными хранилищами. Дополнительные сведения о действии копирования см. в статье [Перемещение данных с помощью действия копирования](data-factory-data-movement-activities.md).   

> [!IMPORTANT]
> Прежде чем начать работу с этим руководством, изучите [обзорную статью](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) и выполните все предварительные требования.
>
> В этой статье рассматриваются не все командлеты фабрики данных. Полную документацию по этим командлетам см. в [справочнике](https://msdn.microsoft.com/library/dn820234.aspx).
>
>

## <a name="prerequisites"></a>Предварительные требования
Помимо выполнения предварительных требований, перечисленных в разделе "Обзор руководства", необходимо установить Azure PowerShell. Следуйте инструкциям по [установке и настройке Azure PowerShell](../powershell-install-configure.md).

## <a name="in-this-tutorial"></a>В этом учебнике рассматриваются следующие темы:
В таблице ниже перечислены шаги, которые выполняются в рамках этого руководства.

| Шаг | Описание |
| --- | --- |
| [Создание фабрики данных Azure](#create-data-factory) |На этом шаге создается фабрика данных Azure с именем **ADFTutorialDataFactoryPSH**. |
| [Создание связанных служб](#create-linked-services) |На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. Связанные службы StorageLinkedService и AzureSqlLinkedService связывают с фабрикой данных ADFTutorialDataFactoryPSH службу хранилища Azure и Базу данных SQL Azure соответственно. |
| [Создать входные и выходные наборы данных](#create-datasets) |На этом этапе определяются два набора данных (**EmpTableFromBlob** и **EmpSQLTable**). Эти наборы данных используются в качестве входных и выходных таблиц для **действия копирования** в конвейере ADFTutorialPipeline, который будет создан на следующем этапе. |
| [Создание и запуск конвейера](#create-pipeline) |На этом шаге создается конвейер с именем **ADFTutorialPipeline** в фабрике данных ADFTutorialDataFactoryPSH. Конвейер использует действие копирования для создания копии данных из большого двоичного объекта Azure в выходной таблице базы данных Azure. |
| [Контроль наборов данных и конвейера](#monitor-pipeline) |На этом шаге выполняется мониторинг наборов данных и конвейера с помощью Azure PowerShell. |

## <a name="create-a-data-factory"></a>Создание фабрики данных
На этом этапе с помощью Azure PowerShell создается фабрика данных Azure с именем **ADFTutorialDataFactoryPSH**.

1. Запустите **PowerShell**. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

   а. Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.

           Login-AzureRmAccount   
   b. Чтобы просмотреть все подписки для этой учетной записи, выполните следующую команду:

           Get-AzureRmSubscription
   c. Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените **&lt;NameOfAzureSubscription**&gt; именем своей подписки Azure.

           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup** , выполнив следующую команду:

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем **ADFTutorialResourceGroup**. Если вы используете другую группу ресурсов, укажите ее вместо ADFTutorialResourceGroup.
3. Выполните командлет **New-AzureRmDataFactory**, чтобы создать фабрику данных с именем **ADFTutorialDataFactoryPSH**.  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. При возникновении следующей ошибки измените имя (например, на ваше_имя_ADFTutorialDataFactoryPSH). Используйте это имя вместо ADFTutorialFactoryPSH при выполнении шагов в этом учебнике. Ознакомьтесь с [правилами именования](data-factory-naming-rules.md) артефактов фабрики данных.

        Data factory name “ADFTutorialDataFactoryPSH” is not available
* Чтобы создавать экземпляры фабрики данных, вы должны быть участником или администратором подписки Azure.
* В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.
* Может появиться сообщение об ошибке **Подписка не зарегистрирована для использования пространства имен "Microsoft.DataFactory"**. В этом случае выполните одно из следующих действий и повторите попытку публикации.

  * Чтобы зарегистрировать поставщик фабрики данных Azure, выполните следующую команду в Azure PowerShell:

          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

      Чтобы убедиться, что поставщик фабрики данных зарегистрирован, выполните следующую команду:

          Get-AzureRmResourceProvider
  * Войдите на [портал Azure](https://portal.azure.com), используя свою подписку Azure. Откройте колонку фабрики данных или создайте фабрику данных на портале Azure. Поставщик будет зарегистрирован автоматически.

## <a name="create-linked-services"></a>Создание связанных служб
Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. Хранилищем данных может выступать служба хранилища Azure, База данных SQL Azure или локальная база данных SQL Server, где хранятся входные или выходные данные для конвейера фабрики данных. Служба вычислений — это служба, которая обрабатывает входные данные и создает выходные данные.

На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. Связанные службы StorageLinkedService и AzureSqlLinkedService связывают с фабрикой данных **ADFTutorialDataFactoryPSH** учетную запись хранения Azure и Базу данных SQL Azure соответственно. Далее в этом руководстве вы создадите конвейер, который копирует данные из контейнера больших двоичных объектов в StorageLinkedService в таблицу SQL в AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Создание связанной службы для учетной записи хранения Azure
1. Создайте в папке **C:\ADFGetStartedPSH** файл JSON с именем **StorageLinkedService.json** со следующим содержимым. (Если папки ADFGetStartedPSH нет, создайте ее.)

         {
               "name": "StorageLinkedService",
               "properties": {
                 "type": "AzureStorage",
                 "typeProperties": {
                       "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                 }
               }
         }

   Замените значения **accountname** и **accountkey** на имя вашей учетной записи хранения Azure и ее ключ.
2. В **Azure PowerShell** перейдите в папку **ADFGetStartedPSH**.
3. Чтобы создать связанную службу, можно использовать командлет **New-AzureRmDataFactoryLinkedService** . В этом командлете, как и в других командлетах фабрики данных, которые используются в этом руководстве, нужно передавать значения для параметров **ResourceGroupName** и **DataFactoryName**. Кроме того, можно использовать командлет **Get-AzureRmDataFactory**, чтобы получить объект DataFactory и передать его без необходимости ввода параметров ResourceGroupName и DataFactoryName при каждом запуске командлета. Чтобы назначить выходные данные командлета **Get-AzureRmDataFactory** переменной **$df**, выполните следующую команду:

    ```   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. Теперь выполните командлет **New-AzureRmDataFactoryLinkedService**, чтобы создать связанную службу **StorageLinkedService**.

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    Если вы не выполнили командлет **Get-AzureRmDataFactory** и назначили выходные данные переменной **$df**, то потребуется указать значения параметров ResourceGroupName и DataFactoryName следующим образом.   

    ```
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

Если вы закроете Azure PowerShell посреди обучения, при следующем запуске Azure PowerShell придется выполнить командлет Get-AzureRmDataFactory, чтобы завершить прохождение руководства.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Создание связанной службы для Базы данных SQL Azure
1. Создайте файл JSON с именем AzureSqlLinkedService.json со следующим содержимым.

         {
             "name": "AzureSqlLinkedService",
             "properties": {
                 "type": "AzureSqlDatabase",
                 "typeProperties": {
                       "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                 }
               }
         }

   Вместо **servername**, **databasename**, **username@servername** и **password** укажите имя своего сервера Azure SQL Server, имя базы данных, имя учетной записи пользователя и пароль.
2. Чтобы создать связанную службу, выполните следующую команду:

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   Убедитесь, что для сервера Базы данных SQL включена настройка **Разрешить доступ к службам Azure**. Чтобы проверить и при необходимости включить этот параметр, выполните следующие действия.

   1. Щелкните раздел **Обзор** слева и выберите пункт **Серверы SQL**.
   2. Выберите свой сервер и в колонке **SQL Server** щелкните пункт **Параметры**.
   3. В колонке **Параметры** щелкните пункт **Брандмауэр**.
   4. В колонке **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.
   5. Щелкните раздел **Активно** слева, чтобы переключиться в колонку **Фабрика данных**, которую вы использовали ранее.

## <a name="create-datasets"></a>Создание наборов данных
На предыдущем шаге вы создали службы, связывающие учетную запись хранения Azure и Базу данных SQL Azure с фабрикой данных. Теперь мы создадим наборы данных, представляющие входные и выходные данные для действия копирования в конвейере, который будет создан на следующем шаге.

Таблица представляет собой прямоугольный набор данных. Сейчас это единственный поддерживаемый тип набора данных. Входная таблица в этом руководстве ссылается на контейнер больших двоичных объектов в службе хранилища Azure. Выходная таблица ссылается на таблицу SQL в Базе данных SQL Azure.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Подготовка хранилища BLOB-объектов Azure и Базы данных SQL Azure для изучения руководства
Пропустите этот шаг, если вы изучили руководство [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Чтобы подготовить хранилище BLOB-объектов и Базу данных SQL для этого руководства, выполните следующие шаги.

1. Создайте контейнер больших двоичных объектов с именем **adftutorial** в хранилище BLOB-объектов, на которое указывает служба **StorageLinkedService**.
2. Создайте и отправьте текстовый файл с именем **emp.txt** в качестве большого двоичного объекта в контейнер **adftutorial**.
3. Создайте таблицу с именем **emp** в Базе данных SQL, на которую указывает служба **AzureSqlLinkedService**.

4. Запустите Блокнот, вставьте следующий текст и сохраните его с именем **emp.txt** в папке **C:\ADFGetStartedPSH** на жестком диске.

        John, Doe
        Jane, Doe
5. При помощи таких средств, как [обозреватель службы хранилища Azure](https://azurestorageexplorer.codeplex.com/), создайте контейнер **adftutorial** и отправьте в него файл **emp.txt**.

    ![Обозреватель хранилищ Azure](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. Используйте следующий скрипт SQL, чтобы создать таблицу **emp** в Базе данных SQL.  

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    Если на вашем компьютере установлена версия SQL Server 2014, следуйте инструкциям, указанным в статье [Управление базой данных SQL Azure с помощью SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md), чтобы подключиться к серверу Базы данных SQL и запустить скрипт SQL.

    Если клиенту не разрешен доступ к серверу Базы данных SQL, следует настроить брандмауэр для сервера Базы данных SQL, чтобы разрешить доступ с вашего компьютера (IP-адреса). Инструкции см. [в этой статье](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-an-input-dataset"></a>Создание входного набора данных
Таблица представляет собой прямоугольный набор данных, организованных в виде схемы. На этом шаге создается таблица с именем **EmpBlobTable**. Таблица указывает на контейнер больших двоичных объектов в службе хранилища Azure, представленной связанной службой **StorageLinkedService**. Этот контейнер (**adftutorial**) содержит входные данные в файле **emp.txt**.

1. Создайте файл JSON с именем **EmpBlobTable.json** в папке **C:\ADFGetStartedPSH** со следующим содержимым:

         {
           "name": "EmpTableFromBlob",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureBlob",
             "linkedServiceName": "StorageLinkedService",
             "typeProperties": {
               "fileName": "emp.txt",
               "folderPath": "adftutorial/",
               "format": {
                 "type": "TextFormat",
                 "columnDelimiter": ","
               }
             },
             "external": true,
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }

   Обратите внимание на следующие моменты.

   * Для параметра **type** набора данных задано значение **AzureBlob**.
   * Для **linkedServiceName** задано значение **StorageLinkedService**.
   * В качестве значения **folderPath** установлен контейнер **adftutorial**.
   * Для параметра **fileName** задано значение **emp.txt**. Если имя BLOB-объекта не указано, входными данными считаются данные из всех BLOB-объектов в контейнере.  
   * Для параметра **type** формата установлено значение **TextFormat**.
   * В текстовом файле содержатся два поля, **FirstName** и **LastName**, которые разделены запятой (**columnDelimiter**).    
   * Параметр **availability** имеет значение **hourly** (параметру **frequency** присваивается значение **hour**, а параметру **interval** — значение **1**). Следовательно, служба фабрики данных будет искать входные данные в корневом каталоге контейнера больших двоичных объектов (**adftutorial**) каждый час.

   Если параметр **fileName** для **входной таблицы** не задан, все файлы и BLOB-объекты из входной папки (**folderPath**) считаются входными данными. Если параметр fileName указать в формате JSON, то только указанный файл или BLOB-объект будут рассматриваться как входные данные.

   Если не указать **fileName** для **выходной таблицы**, то созданные в **folderPath** файлы получают имена в следующем формате: Data.<Guid\>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Чтобы динамически установить параметры **folderPath** и **fileName** на основе времени **SliceStart**, используйте свойство **partitionedBy**. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а в fileName используется время (часы) из SliceStart. Например, если срез выполняется для временной отметки 2016-10-20T08:00:00, folderName получает значение wikidatagateway/wikisampledataout/2016/10/20, а fileName — 08.csv.

         "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
         "fileName": "{Hour}.csv",
         "partitionedBy":
         [
             { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
             { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
             { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
             { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
         ],

   Дополнительные сведения о свойствах JSON см. в [справочнике по скриптам JSON](data-factory-data-movement-activities.md).
2. Выполните следующую команду для создания набора данных фабрики данных.

    ```  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>Создание выходного набора данных
На этом этапе вы создадите выходной набор данных с именем **EmpSQLTable**. Этот набор данных указывает на таблицу SQL (**emp**) в базе данных SQL Azure (представлена значением **AzureSqlLinkedService**). Конвейер копирует данные из входного большого двоичного объекта в таблицу **emp** .

1. Создайте файл JSON с именем **EmpSQLTable.json** в папке **C:\ADFGetStartedPSH** со следующим содержимым:

         {
           "name": "EmpSQLTable",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureSqlTable",
             "linkedServiceName": "AzureSqlLinkedService",
             "typeProperties": {
               "tableName": "emp"
             },
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }

   Обратите внимание на следующие моменты.

   * Для параметра **type** набора данных задано значение **AzureSqlTable**.
   * Для **linkedServiceName** задано значение **AzureSqlLinkedService**.
   * **tablename** имеет значение **emp**.
   * В таблице emp в базе данных есть три столбца: **ID**, **FirstName** и **LastName**. ID — это столбец для идентификаторов, поэтому здесь вам нужно указать только значения **FirstName** и **LastName**.
   * Параметр **availability** имеет значение **hourly** (параметру **frequency** присваивается значение **hour**, а параметру **interval** — значение **1**). Служба фабрики данных каждый час создает срез выходных данных в таблице **emp** в базе данных SQL Azure.
2. Выполните следующую команду для создания набора данных фабрики данных.

    ```   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>Создание конвейера
На этом шаге создается конвейер с **действием копирования**. Конвейер использует **EmpTableFromBlob** в качестве входных данных и **EmpSQLTable** в качестве выходных данных.

1. Создайте файл JSON с именем **ADFTutorialPipeline.json** в папке **C:\ADFGetStartedPSH** со следующим содержимым:

          {
           "name": "ADFTutorialPipeline",
           "properties": {
             "description": "Copy data from a blob to Azure SQL table",
             "activities": [
               {
                 "name": "CopyFromBlobToSQL",
                 "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                 "type": "Copy",
                 "inputs": [
                   {
                     "name": "EmpTableFromBlob"
                   }
                 ],
                 "outputs": [
                   {
                     "name": "EmpSQLTable"
                   }
                 ],
                 "typeProperties": {
                   "source": {
                     "type": "BlobSource"
                   },
                   "sink": {
                     "type": "SqlSink"
                   }
                 },
                 "Policy": {
                   "concurrency": 1,
                   "executionPriorityOrder": "NewestFirst",
                   "style": "StartOfInterval",
                   "retry": 0,
                   "timeout": "01:00:00"
                 }
               }
             ],
             "start": "2016-08-09T00:00:00Z",
             "end": "2016-08-10T00:00:00Z",
             "isPaused": false
           }
         }

   Обратите внимание на следующие моменты.

   * В разделе действий доступно только одно действие, параметр **type** которого имеет значение **Copy**.
   * Для входных данных действия задано значение **EmpTableFromBlob**, а для выходных данных — **EmpSQLTable**.
   * В разделе **transformation** в качестве типа источника указано значение **BlobSource**, а в качестве типа приемника — **SqlSink**.

   Замените значение свойства **start** текущей датой, а значение свойства **end** — датой следующего дня. Даты начала и окончания должны быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2016-10-14T16:32:41Z. В этом руководстве мы используем время **end**, однако его указывать не обязательно.

   Если не указать значение свойства **end**, оно вычисляется по формуле "**время начала + 48 часов**". Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9/9/9999** в качестве значения свойства **end**.

   В этом примере будет 24 среза данных, так как каждый час создается новый срез.

   Дополнительные сведения о свойствах JSON см. в [справочнике по скриптам JSON](data-factory-data-movement-activities.md).
2. Выполните следующую команду для создания таблицы фабрики данных.

    ```   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

Поздравляем! Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер. Вы также создали расписание для конвейера.

## <a name="monitor-the-pipeline"></a>Мониторинг конвейера
На этом шаге Azure PowerShell будет использоваться для мониторинга процессов в фабрике данных Azure.

1. Выполните командлет **Get-AzureRmDataFactory** и назначьте выходные данные переменной $df.

    ```  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. Выполните командлет **Get-AzureRmDataFactorySlice** для получения сведений обо всех срезах в таблице **EmpSQLTable**, которая является выходной таблицей конвейера.  

    ```   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Замените год, месяц и дату в параметре **StartDateTime** на текущие значения. Это значение должно соответствовать значению параметра **Start** в JSON-файле конвейера.

   Вы увидите 24 среза — по одному для каждого часа с 00:00 текущего дня до 00:00 следующего дня.

   **Пример выходных данных**

    ```   
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. Выполните командлет **Get-AzureRmDataFactoryRun**, чтобы получить сведения о действиях, выполняемых для **конкретного** среза. Измените значение параметра **StartDateTime** в соответствии со значением параметра **Start** для среза из выходных данных. Значение **StartDateTime** должно быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601).

    ```  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Вы должны увидеть результат, аналогичный приведенному ниже.

    ```   
     Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
     ResourceGroupName   : ADFTutorialResourceGroup
     DataFactoryName     : ADFTutorialDataFactoryPSH
     TableName           : EmpSQLTable
     ProcessingStartTime : 8/9/2016 11:03:28 PM
     ProcessingEndTime   : 8/9/2016 11:04:36 PM
     PercentComplete     : 100
     DataSliceStart      : 8/9/2016 10:00:00 PM
     DataSliceEnd        : 8/9/2016 11:00:00 PM
     Status              : Succeeded
     Timestamp           : 8/9/2016 11:03:28 PM
     RetryAttempt        : 0
     Properties          : {}
     ErrorMessage        :
     ActivityName        : CopyFromBlobToSQL
     PipelineName        : ADFTutorialPipeline
     Type                : Copy
    ```

Полную документацию по командлетам фабрики данных см. в [этом справочнике][cmdlet-reference].

## <a name="summary"></a>Сводка
В этом учебнике вы создали фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Вы использовали PowerShell для создания фабрики данных, связанных служб, наборов данных и конвейера. Вот обобщенные действия, которые вы выполнили в этом руководстве:  

1. Создание **фабрики данных Azure**.
2. Создание **связанных служб**.

   а. **Служба хранилища Azure** — создание связанной службы для связи с учетной записью хранения Azure, которая содержит входные данные.     
   b. **SQL Azure** — создание связанной службы для связи с Базой данных SQL Azure, которая содержит выходные данные.
3. Создание **наборов данных** , которые описывают входные и выходные данные для конвейеров.
4. Создание **конвейера** с **BlobSource** в качестве источника и **SqlSink** в качестве приемника с помощью **действия копирования**.

## <a name="see-also"></a>Дополнительные материалы
| Раздел | Описание |
|:--- |:--- |
| [Действия перемещения данных](data-factory-data-movement-activities.md) |В этой статье содержится информация о действии копирования, которое использовалось в этом руководстве. |
| [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) |Здесь объясняются аспекты планирования и исполнения в модели приложений фабрики данных Azure. |
| [Конвейеры](data-factory-create-pipelines.md) |Эта статья содержит сведения о конвейерах и действиях в фабрике данных Azure. |
| [datasets](data-factory-create-datasets.md) |Эта статья поможет вам понять, что такое наборы данных в фабрике данных Azure. |
| [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью нового приложения по мониторингу и управлению](data-factory-monitor-manage-app.md) |В этой статье описывается мониторинг и отладка конвейеров, а также управление ими с помощью приложения мониторинга и управления. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Jan17_HO5-->



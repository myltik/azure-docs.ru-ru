---
title: "Копирование локальных данных в облако с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из локального хранилища данных в облако Azure с помощью локальной среды выполнения интеграции в фабрике данных Azure."
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 24a4255a23f0b9b9da5d8c3cefeefb8fe250f2f1
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-copy-data-between-on-premises-and-cloud"></a>Руководство. Копирование данных между локальной средой и облаком
В этом руководстве будет использоваться Azure PowerShell для создания конвейера фабрики данных, который копирует данные из локальной базы данных SQL Server в хранилище BLOB-объектов Azure. Вы создадите и будете использовать локальную среду выполнения интеграции (IR) фабрики данных Azure, которая обеспечивает интеграцию локальных и облачных хранилищ данных.  Сведения об использовании других средств или пакетов SDK для создания фабрики данных см. в [этом кратком руководстве](quickstart-create-data-factory-dot-net.md).

Эта статья не содержит подробный обзор службы фабрики данных. Общие сведения о службе фабрики данных Azure см. в статье [Введение в фабрику данных Azure](introduction.md). 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создадите локальную среду выполнения интеграции.
> * Создадите SQL Server и связанные службы Azure. 
> * Создадите SQL Server и наборы данных больших двоичных объектов Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 или 2016. 
В этом руководстве используйте локальную базу данных SQL Server в качестве **исходного** хранилища данных. Создайте таблицу с именем **emp** в базе данных SQL Server и вставьте в нее несколько примеров записей.

1. Запустите **SQL Server Management Studio**. Если вы используете SQL Server 2016, может потребоваться отдельно установить SQL Server Management Studio из [центра загрузки](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Подключитесь к SQL Server с помощью учетных данных. 
3. Создайте пример базы данных. В представлении в виде дерева щелкните правой кнопкой мыши элемент **Базы данных** и выберите пункт **Новая база данных**. В диалоговом окне **Новая база данных** введите **имя** базы данных и нажмите кнопку **ОК**. 
4. Запустите следующий скрипт запроса к базе данных, который создаст таблицу **emp**. В представлении в виде дерева щелкните правой кнопкой мыши созданную **базу данных** и выберите пункт **Новый запрос**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Выполните следующие команды в базе данных, которые вставят примеры данных в таблицу:

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Учетная запись хранения Azure
В этом руководстве используйте учетную запись хранения Azure общего назначения (хранилища BLOB-объектов) в качестве **целевого (принимающего)** хранилища данных. Если у вас нет учетной записи хранения Azure общего назначения, см. инструкции по [созданию учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account).

#### <a name="get-storage-account-name-and-account-key"></a>Получение имени и ключа учетной записи хранения
В этом кратком руководстве вы будете использовать имя и ключ своей учетной записи хранения Azure. Далее описана процедура получения имени и ключа учетной записи хранения. 

1. Откройте браузер и перейдите на [портал Azure](https://portal.azure.com). Войдите, используя имя пользователя и пароль Azure. 
2. Щелкните **Больше служб >** в меню слева, отфильтруйте содержимое по ключевому слову **хранение**, затем выберите пункт **Учетные записи хранения**.

    ![Поиск учетной записи хранения](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. В списке учетных записей хранения найдите с помощью фильтра свою учетную запись хранения (при необходимости), а затем выберите **эту учетную запись**. 
4. На странице **учетной записи хранения** выберите в меню параметр **Ключи доступа**.

    ![Получение имени и ключа учетной записи хранения](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Скопируйте значения полей **Имя учетной записи хранения** и **key1** в буфер обмена. Вставьте их в Блокнот или другой редактор и сохраните файл. В этом руководстве будет использоваться имя и ключ учетной записи хранения. 

#### <a name="create-the-adftutorial-container"></a>Создание контейнера adftutorial 
В этом разделе вы создадите контейнер больших двоичных объектов с именем adftutorial в хранилище BLOB-объектов Azure. 

1. Установите [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), если его нет на вашем компьютере. 
2. Запустите **обозреватель службы хранилища Microsoft Azure** на компьютере.   
3. В окне **подключения к службе хранилища Azure** выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения) и нажмите кнопку **Next** (Далее). Если окно **подключения к службе хранилища Azure** не отображается, щелкните правой кнопкой мыши элемент **Storage Accounts** (Учетные записи хранения) в представлении в виде дерева, а затем выберите пункт **Connect to Azure storage** (Подключиться к хранилищу Azure). 

    ![Подключение к службе хранилища Azure](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. В окне **Присоединение с помощью имени и ключа** вставьте **имя учетной записи** и **ключ учетной записи**, сохраненные на предыдущем шаге. Затем щелкните **Далее**. 
5. В окне **Connection Summary** (Сводка по подключению) нажмите кнопку **Connect** (Подключиться).
6. Убедитесь, что ваша учетная запись хранения отображается в представлении в виде дерева в разделе **(Local and Attached)** -> **Storage Accounts** ((Локальные и присоединенные) > Учетные записи хранения). 
7. Разверните узел **Контейнеры больших двоичных объектов** и убедитесь, что контейнер больших двоичных объектов с именем **adftutorial** не существует. Если он уже существует, пропустите следующие шаги по созданию контейнера. 
8. Щелкните правой кнопкой мыши элемент **Blob Containers** (Контейнеры больших двоичных объектов) и выберите **Create Blob Container** (Создать контейнер BLOB-объектов).

    ![Создание контейнера больших двоичных объектов](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Введите **adftutorial** в качестве имени и нажмите клавишу **ВВОД**. 
10. Убедитесь, что контейнер **adftutorial** выбран в представлении в виде дерева. Фабрика данных автоматически создает выходную папку в этом контейнере, поэтому ее не нужно создавать. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Установка Azure PowerShell
Установите последнюю версию среды Azure PowerShell, если ее еще нет на компьютере. 

1. В веб-браузере перейдите на страницу [скачивания пакетов Azure SDK](https://azure.microsoft.com/downloads/). 
2. Нажмите кнопку **Установка Windows** в разделе **Программы командной строки** -> **PowerShell**. 
3. Чтобы установить Azure PowerShell, запустите файл **MSI**. 

Подробные инструкции см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Вход в Azure PowerShell
Запустите **PowerShell** на компьютере. Не закрывайте Azure PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

1. Выполните следующую команду и введите имя пользователя Azure и пароль, которые используются для входа на портал Azure.
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Если у вас несколько подписок, выполните следующую команду, чтобы просмотреть все подписки для этой учетной записи:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените значение **SubscriptionId** на идентификатор подписки Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Создать фабрику данных

1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Определите переменную для имени фабрики данных, которую в дальнейшем можно будет использовать в командах PowerShell. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Определите переменную для расположения фабрики данных: 

    ```powershell
    $location = "East US"
    ```
4. Чтобы создать группу ресурсов Azure, выполните следующую команду: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$resourceGroupName` другое значение и повторите попытку. Если вы хотите совместно использовать группу ресурсов с другими пользователями, перейдите к следующему шагу.  
5. Чтобы создать фабрику данных, выполните командлет **Set-AzureRmDataFactoryV2**. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Чтобы создавать экземпляры фабрики данных, вы должны быть **участником** или **администратором** подписки Azure.
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

## <a name="create-a-self-hosted-ir"></a>Создание локальной среды IR

В этом разделе вы создадите локальную среду выполнения интеграции и свяжите ее с локальным узлом (компьютером).

1. Создайте переменную для имени среды выполнения интеграции. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Создайте локальную среду выполнения интеграции. Используйте уникальное имя в случае, если существует другая среда выполнения интеграции с тем же именем.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Пример выходных данных:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Выполните следующую команду, чтобы получить состояние созданной среды выполнения интеграции.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Пример выходных данных:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Выполните следующую команду, чтобы получить ключи проверки подлинности для регистрации локальной среды выполнения интеграции в службе фабрики данных в облаке. Скопируйте один из ключей для регистрации локальной среды выполнения интеграции.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Пример выходных данных:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Установка среды выполнения интеграции
1. [Скачайте](https://www.microsoft.com/download/details.aspx?id=39717) локальную среду выполнения интеграции на локальный компьютер и запустите установку. 
2. В окне **приветствия мастера установки Microsoft Integration Runtime** нажмите кнопку **Далее**.  
3. На странице **Лицензионное соглашение** примите условия использования и лицензионное соглашение и нажмите кнопку **Далее**. 
4. На странице **Конечная папка** нажмите кнопку **Далее**. 
5. На странице **Все готово для установки Microsoft Integration Runtime** нажмите кнопку **Установить**. 
6. Если появится предупреждающее сообщение о том, что для компьютера, который не используется, настроен переход в спящий режим или режим гибернации, нажмите кнопку **ОК**. 
7. На странице **Мастер установки Microsoft Integration Runtime завершит работу** нажмите кнопку **Готово**.
8. На странице **Регистрация Integration Runtime (Self-hosted)** вставьте ключ, созданный в предыдущем разделе, и нажмите кнопку **Зарегистрировать**. 

   ![Регистрация среды выполнения интеграции](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Когда локальная среда выполнения интеграции будет успешно зарегистрирована, вы увидите следующее сообщение:

   ![Успешно зарегистрирована](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. На странице **Новый узел Integration Runtime (Self-hosted)** нажмите кнопку **Далее**. 

    ![Страница "Новый узел Integration Runtime"](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. На странице **Коммуникационный канал интрасети** нажмите кнопку **Пропустить**. Вы можете выбрать сертификацию TLS/SSL для защиты внутриузловой передачи данных в среде выполнения интеграции с несколькими узлами. 

    ![Страница "Коммуникационный канал интрасети"](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. На странице **Регистрация Integration Runtime (Self-hosted)** нажмите кнопку **Запустить диспетчер конфигурации**. 
6. Когда узел будет подключен к облачной службе, отобразится следующая страница:

   ![Узел подключен](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Создание связанных служб

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Создание связанных служб хранилища Azure (место назначения и приемник)

1. Создайте JSON-файл с именем **AzureStorageLinkedService.json** в папке **C:\ADFv2Tutorial** и добавьте в него приведенное ниже содержимое. Если папка ADFv2Tutorial отсутствует, создайте ее.  

    > [!IMPORTANT]
    > Перед сохранением файла замените значения &lt;accountname&gt; и &lt;accountkey&gt; на имя вашей учетной записи хранения Azure и ее ключ.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```
2. В **Azure PowerShell** перейдите в папку **ADFv2Tutorial**.

   Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу: **AzureStorageLinkedService**. Командлеты, используемые в этом руководстве, принимают значения параметров **ResourceGroupName** и **DataFactoryName**. Кроме того, вы можете передать объект **DataFactory**, возвращенный командлетом Set-AzureRmDataFactoryV2, не вводя параметры ResourceGroupName и DataFactoryName при каждом запуске командлета.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Пример выходных данных:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Создание и шифрование связанной службы SQL Server (источник)

1. Создайте JSON-файл с именем **SqlServerLinkedService.json** в папке **C:\ADFv2Tutorial** с указанным ниже содержимым. Замените **&lt;servername>**, **&lt;databasename>**, **&lt;username>** и **&lt;password>** собственными именами сервера, базы данных, пользователя и пароля SQL Server, прежде чем сохранить файл. 

    > [!IMPORTANT]
    > Замените **&lt;integration** **runtime** **name>** именем своей среды выполнения интеграции.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Чтобы зашифровать конфиденциальные данные, содержащиеся в полезных данных JSON в локальной среде выполнения интеграции, выполните командлет **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** и передайте указанные выше полезные данные JSON. Шифрование гарантирует, что учетные данные будут зашифрованы с помощью программного интерфейса защиты данных (API защиты данных). Защищенные данные хранятся на узле локальной среды выполнения интеграции (на локальном компьютере). Полезные выходные данные можно перенаправить в другой JSON-файл (в этом случае encryptedLinkedService.json), содержащий зашифрованные учетные данные.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Выполните следующую команду с помощью JSON-файла из предыдущего шага для создания **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Создание наборов данных
На этом этапе вы создадите наборы входных и выходных данных, которые представляют собой входные и выходные данные для операции копирования (из локальной базы данных SQL Server в хранилище BLOB-объектов Azure).

### <a name="create-a-dataset-for-source-sql-database"></a>Создание набора данных для исходной базы данных SQL

1. Создайте JSON-файл с именем **SqlServerDataset.json** в папке **C:\ADFv2Tutorial** со следующим содержимым:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Чтобы создать набор данных **SqlServerDataset**, выполните командлет **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Пример выходных данных:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Создание набора данных для хранилища BLOB-объектов Azure (приемника)

1. Создайте JSON-файл с именем **AzureBlobDataset.json** в папке **C:\ADFv2Tutorial** со следующим содержимым:

    > [!IMPORTANT]
    > В этом образце кода предполагается, что у вас есть контейнер с именем **adftutorial** в хранилище BLOB-объектов Azure.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Чтобы создать набор данных **AzureBlobDataset**, выполните командлет **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Пример выходных данных:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Создание конвейеров

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Создание конвейера SqlServerToBlobPipeline

1. Создайте JSON-файл с именем **SqlServerToBlobPipeline.json** в папке **C:\ADFv2Tutorial** со следующим содержимым:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Чтобы создать конвейер **SQLServerToBlobPipeline**, выполните командлет **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Пример выходных данных:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Запуск и мониторинг выполнения конвейера

1. Запустите выполнение конвейера SQLServerToBlobPipeline и запишите идентификатор выполнения конвейера для будущего мониторинга.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Выполните указанный ниже скрипт, чтобы постоянно проверять состояние выполнения конвейера **SQLServerToBlobPipeline** и вывести конечный результат.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Вот результат примера выполнения:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Вы можете получить идентификатор выполнения конвейера **SQLServerToBlobPipeline** и просмотреть подробный результат выполнения действия, как показано ниже.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Вот результат примера выполнения:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Проверка выходных данных
Конвейер автоматически создает выходную папку с именем `fromonprem` в контейнере больших двоичных объектов `adftutorial`. Убедитесь, что вы видите в выходной папке файл **dbo.emp.txt**. Используйте [обозреватель хранилищ Azure](https://azure.microsoft.com/features/storage-explorer/), чтобы проверить, созданы ли выходные данные. 

## <a name="next-steps"></a>Дальнейшие действия
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создадите локальную среду выполнения интеграции.
> * Создадите SQL Server и связанные службы Azure. 
> * Создадите SQL Server и наборы данных больших двоичных объектов Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

См. список хранилищ данных, [поддерживаемых фабрикой данных Azure](copy-activity-overview.md#supported-data-stores-and-formats).

Перейдите к следующему руководству, чтобы узнать о копировании данных в пакетном режиме из источника в место назначения:

> [!div class="nextstepaction"]
>[Копирование нескольких таблиц в пакетном режиме с помощью фабрики данных Azure](tutorial-bulk-copy.md)

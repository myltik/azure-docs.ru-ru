---
title: "Копирование данных из базы данных SQL Server в хранилище BLOB-объектов с помощью фабрики данных Azure | Документация Майкрософт"
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ba47f3e3f331929b884f27f49bf6e484ff363765
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Руководство. Копирование данных из локальной среды SQL Server в хранилище BLOB-объектов Azure
В этом руководстве будет использоваться Azure PowerShell для создания конвейера фабрики данных, который копирует данные из локальной базы данных SQL Server в хранилище BLOB-объектов Azure. Вы создадите и будете использовать локальную среду выполнения интеграции, которая перемещает данные между локальным и облачным хранилищами данных. 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Эта статья не содержит подробный обзор службы фабрики данных. Общие сведения о службе фабрики данных Azure см. в статье [Введение в фабрику данных Azure](introduction.md). 

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создайте локальную среду выполнения интеграции.
> * Создадите SQL Server и связанные службы Azure. 
> * Создадите SQL Server и наборы данных больших двоичных объектов Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

## <a name="prerequisites"></a>Предварительные требования
### <a name="azure-subscription"></a>Подписка Azure.
Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

### <a name="azure-roles"></a>Роли Azure
Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure. На портале Azure щелкните **имя пользователя** в правом верхнем углу и выберите **Разрешения**, чтобы просмотреть существующие разрешения в подписке. Если у вас есть доступ к нескольким подпискам, выберите соответствующую подписку. Примеры инструкций по назначению пользователю роли см. в статье [о добавлении ролей](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-201420162017"></a>SQL Server 2014, 2016 или 2017
В этом руководстве используйте локальную базу данных SQL Server в качестве **исходного** хранилища данных. Конвейер фабрики данных, созданный в рамках этого руководства, копирует данные из этой локальной базы данных SQL Server (источника) в хранилище BLOB-объектов Azure (приемник). Создайте таблицу с именем **emp** в базе данных SQL Server и вставьте в нее несколько примеров записей. 

1. Запустите **SQL Server Management Studio** на своем компьютере. Если на нем не установлен пакет SQL Server Management Studio, установите его из [центра загрузки](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Подключитесь к SQL Server с помощью учетных данных. 
3. Создайте пример базы данных. В представлении в виде дерева щелкните правой кнопкой мыши элемент **Базы данных** и выберите пункт **Новая база данных**. В диалоговом окне **Новая база данных** введите **имя** базы данных и нажмите кнопку **ОК**. 
4. Запустите приведенный ниже скрипт запроса к базе данных, который позволяет создать таблицу **emp** и вставить в нее примеры данных. В представлении в виде дерева щелкните правой кнопкой мыши созданную **базу данных** и выберите пункт **Новый запрос**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Учетная запись хранения Azure
В этом руководстве используйте учетную запись хранения Azure общего назначения (хранилища BLOB-объектов) в качестве **целевого (принимающего)** хранилища данных. Если у вас нет учетной записи хранения Azure общего назначения, см. инструкции по [созданию учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). Конвейер фабрики данных, созданный в рамках этого руководства, копирует данные из локальной базы данных SQL Server (источника) в это хранилище BLOB-объектов Azure (приемник). 

#### <a name="get-storage-account-name-and-account-key"></a>Получение имени и ключа учетной записи хранения
В этом руководстве вы будете использовать имя и ключ своей учетной записи хранения Azure. Далее описана процедура получения имени и ключа учетной записи хранения. 

1. Откройте браузер и перейдите на [портал Azure](https://portal.azure.com). Войдите, используя имя пользователя и пароль Azure. 
2. Щелкните **Больше служб >** в меню слева, отфильтруйте содержимое по ключевому слову **хранение**, затем выберите пункт **Учетные записи хранения**.

    ![Поиск учетной записи хранения](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. В списке учетных записей хранения найдите с помощью фильтра свою учетную запись хранения (при необходимости), а затем выберите **эту учетную запись**. 
4. На странице **учетной записи хранения** выберите в меню параметр **Ключи доступа**.

    ![Получение имени и ключа учетной записи хранения](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Скопируйте значения полей **Имя учетной записи хранения** и **key1** в буфер обмена. Вставьте их в Блокнот или другой редактор и сохраните файл. В этом руководстве будет использоваться имя и ключ учетной записи хранения. 

#### <a name="create-the-adftutorial-container"></a>Создание контейнера adftutorial 
В этом разделе вы создадите контейнер больших двоичных объектов с именем **adftutorial** в хранилище BLOB-объектов Azure. 

1. На странице **Учетная запись хранения** перейдите на вкладку **Обзор** и щелкните **BLOB-объекты**. 

    ![Выбор параметра "BLOB-объекты"](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. На странице **Служба BLOB-объектов** щелкните **+ Контейнер** на панели инструментов. 

    ![Кнопка добавления контейнера](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. В диалоговом окне **Создание контейнера** введите **adftutorial** в поле имени и нажмите кнопку **ОК**. 

    ![Ввод имени контейнера](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. Щелкните **adftutorial** в списке контейнеров.  

    ![Выбор контейнера](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. Не закрывайте страницу **контейнера** **adftutorial**. Она понадобится для проверки выходных данных в конце этого руководства. Фабрика данных автоматически создает выходную папку в этом контейнере, поэтому ее не нужно создавать.

    ![Страница контейнера](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Установка PowerShell
Установите последнюю версию среды PowerShell, если ее еще нет на компьютере. 

1. В веб-браузере перейдите на страницу [скачивания пакетов Azure SDK](https://azure.microsoft.com/downloads/). 
2. Нажмите кнопку **Установка Windows** в разделе **Программы командной строки** -> **PowerShell**. 
3. Чтобы установить Azure PowerShell, запустите файл **MSI**. 

Подробные инструкции см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Вход в PowerShell

1. Запустите **PowerShell** на компьютере. Не закрывайте окно PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

    ![Запуск PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. Выполните следующую команду и введите имя пользователя Azure и пароль, которые используются для входа на портал Azure.
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Если у вас несколько подписок, выполните следующую команду, чтобы просмотреть все подписки для этой учетной записи:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку. Замените значение **SubscriptionId** на идентификатор подписки Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Создать фабрику данных

1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. Например, `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Чтобы создать группу ресурсов Azure, выполните следующую команду: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$resourceGroupName` другое значение и еще раз выполните команду.
3. Определите переменную для имени фабрики данных, которую в дальнейшем можно будет использовать в командах PowerShell. Имена должны начинаться с буквы или цифры и могут содержать только буквы, цифры и дефисы (-).

    > [!IMPORTANT]
    >  Измените имя фабрики данных, чтобы оно было глобально уникальным. Например, укажите ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. Определите переменную для расположения фабрики данных: 

    ```powershell
    $location = "East US"
    ```  
5. Чтобы создать фабрику данных, выполните командлет **Set-AzureRmDataFactoryV2**. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure.
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

## <a name="create-a-self-hosted-ir"></a>Создание локальной среды IR

В этом разделе вы создадите локальную среду выполнения интеграции и свяжете ее с локальным компьютером, на котором находится база данных SQL Server. Локальная среда выполнения интеграции — это компонент, который копирует данные из SQL Server на компьютере в хранилище BLOB-объектов Azure. 

1. Создайте переменную для имени среды выполнения интеграции. Используйте уникальное имя и запишите его. Оно будет использоваться далее в этом руководстве. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Создайте локальную среду выполнения интеграции. 

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

3. Выполните следующую команду, чтобы получить **ключи проверки подлинности** для регистрации локальной среды выполнения интеграции в службе фабрики данных в облаке. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Пример выходных данных:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. Скопируйте один из ключей (без двойных кавычек) для регистрации локальной среды выполнения интеграции, которую вы установите на компьютер на следующем шаге.  

## <a name="install-integration-runtime"></a>Установка среды выполнения интеграции
1. [Скачайте](https://www.microsoft.com/download/details.aspx?id=39717) локальную среду выполнения интеграции на локальный компьютер под управлением Windows и запустите установку. 
2. В окне **приветствия мастера установки Microsoft Integration Runtime** нажмите кнопку **Далее**.  
3. На странице **Лицензионное соглашение** примите условия использования и лицензионное соглашение и нажмите кнопку **Далее**. 
4. На странице **Конечная папка** нажмите кнопку **Далее**. 
5. На странице **Все готово для установки Microsoft Integration Runtime** нажмите кнопку **Установить**. 
6. Если появится предупреждающее сообщение о том, что для компьютера, который не используется, настроен переход в спящий режим или режим гибернации, нажмите кнопку **ОК**. 
7. Если появится окно **Электропитание**, закройте его и перейдите в окно установки. 
8. На странице **Мастер установки Microsoft Integration Runtime завершит работу** нажмите кнопку **Готово**.
9. На странице **Регистрация Integration Runtime (Self-hosted)** вставьте ключ, созданный в предыдущем разделе, и нажмите кнопку **Зарегистрировать**. 

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
7. Теперь проверьте возможность подключения к базе данных SQL Server.

    ![Вкладка «Диагностика»](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - В окне **Configuration Manager** перейдите на вкладку **Диагностика**.
    - В поле **Тип источника данных** выберите **SqlServer**.
    - Введите имя **сервера**.
    - Введите имя **базы данных**. 
    - Выберите режим **проверки подлинности**. 
    - Введите имя **пользователя**. 
    - Введите **пароль** для этого имени пользователя.
    - Нажмите кнопку **проверки**, чтобы убедиться, что эта среда выполнения интеграции может подключаться к серверу SQL Server. Если подключение установлено успешно, появится зеленый флажок. В противном случае появится сообщение об ошибке. Исправьте ошибки и проверьте, может ли среда выполнения интеграции подключаться к SQL Server.
    - Запишите эти значения (тип аутентификации, сервер, база данных, имя пользователя, пароль). Они будут использоваться далее в этом руководстве. 
    
      
## <a name="create-linked-services"></a>Создание связанных служб
Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом руководстве вы свяжете учетную запись хранения Azure и локальную базу данных SQL Server с хранилищем данных. Связанные службы содержат сведения о подключении, используемые службой фабрики данных для подключения к ним в среде выполнения. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Создание связанных служб хранилища Azure (место назначения и приемник)
На этом шаге вы свяжете учетную запись хранения Azure с фабрикой данных.

1. Создайте JSON-файл с именем **AzureStorageLinkedService.json** в папке **C:\ADFv2Tutorial** и добавьте в него приведенное ниже содержимое. Если папка ADFv2Tutorial отсутствует, создайте ее.  

    > [!IMPORTANT]
    > Перед сохранением файла замените значения &lt;accountname&gt; и &lt;accountkey&gt; на имя вашей **учетной записи хранения Azure** и ее ключ. Вы записали их ранее в рамках [предварительных требований](#get-storage-account-name-and-account-key).

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

    Если вы используете Блокнот, в диалоговом окне **Сохранить как** в поле **Тип файла** выберите пункт **Все файлы**. Иначе к файлу может добавиться расширение `.txt`. Например, `AzureStorageLinkedService.json.txt`. Если вы создали файл в проводнике до того, как открыли его в Блокноте, расширение `.txt` может не отображаться, так как по умолчанию установлен параметр **Скрывать расширения для зарегистрированных типов файлов**. Удалите расширение `.txt`, прежде чем перейти к следующему шагу. 
2. В **Azure PowerShell** перейдите в папку **C:\ADFv2Tutorial**.

   Выполните командлет **Set-AzureRmDataFactoryV2LinkedService**, чтобы создать связанную службу: **AzureStorageLinkedService**. 

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

    Если отображается сообщение об ошибке "Файл не найден", выполните команду `dir`, чтобы удостовериться, что файл существует. Если имя файла имеет расширение `.txt` (например, AzureStorageLinkedService.json.txt), удалите расширение и выполните команду PowerShell еще раз. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Создание и шифрование связанной службы SQL Server (источник)
На этом шаге вы свяжете локальную базу данных SQL Server с фабрикой данных.

1. Создайте в папке **C:\ADFv2Tutorial** JSON-файл с именем **SqlServerLinkedService.json** с указанным ниже содержимым. Select the right section based on the **authentication** you use to connect to SQL Server.  

    > [!IMPORTANT]
    > Выберите правильный раздел в зависимости от типа **проверки подлинности**, который используется для подключения к SQL Server.

    **Если используется проверка подлинности SQL (sa), скопируйте следующее определение JSON:**

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
    **Если используется проверка подлинности Windows, скопируйте следующее определение JSON:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
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
    > [!IMPORTANT]
    > - Выберите правильный раздел в зависимости от типа **проверки подлинности**, который используется для подключения к SQL Server.
    > - Замените **&lt;integration** **runtime** **name>** именем своей среды выполнения интеграции.
    > - Перед сохранением файла замените **&lt;servername>**, **&lt;databasename>**, **&lt;username>** и **&lt;password>** значениями для своей базы данных SQL Server.
    > - Если в имени учетной записи пользователя или имени сервера необходимо использовать символ косой черты (`\`), добавьте escape-символ (`\`). Например, `mydomain\\myuser`. 

2. Для шифрования конфиденциальных данных (имени пользователя, пароля и т. д.) выполните командлет **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**. Шифрование гарантирует, что учетные данные будут зашифрованы с помощью программного интерфейса защиты данных (API защиты данных). Зашифрованные данные хранятся на узле локальной среды выполнения интеграции (на локальном компьютере). Полезные выходные данные можно перенаправить в другой JSON-файл (в этом случае encryptedLinkedService.json), содержащий зашифрованные учетные данные.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Выполните следующую команду, которая создает **EncryptedSqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Создание наборов данных
На этом этапе вы создадите наборы входных и выходных данных, которые представляют собой входные и выходные данные для операции копирования (из локальной базы данных SQL Server в хранилище BLOB-объектов Azure).

### <a name="create-a-dataset-for-source-sql-database"></a>Создание набора данных для исходной базы данных SQL
На этом шаге вы определите набор данных, представляющий данные в базе данных SQL Server. Это набор данных типа **SqlServerTable**. Он ссылается на **связанную службу SQL Server**, созданную на предыдущем шаге. Связанная служба содержит **сведения о подключении**, используемые службой фабрики данных для подключения к SQL Server в среде выполнения. Этот набор данных указывает **таблицу SQL** в базе данных, содержащий эти данные. В нашем руководстве это таблица `emp`, содержащая исходные данные. 

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
На этом шаге определите набор данных, которые необходимо копировать в хранилище BLOB-объектов Azure. Это набор данных типа **AzureBlob**. Он ссылается на **связанную службу хранилища Azure**, созданную ранее в рамках этого руководства. Связанная служба содержит **сведения о подключении**, используемые фабрикой данных для подключения к учетной записи хранения Azure в среде выполнения. В этом **наборе данных** указана **папка** в хранилище Azure, в которую копируются данные из базы данных SQL Server. В этом руководстве используется папка `adftutorial/fromonprem`, где`adftutorial` — это контейнер больших двоичных объектов, а `fromonprem` — собственно папка. 

1. Создайте JSON-файл с именем **AzureBlobDataset.json** в папке **C:\ADFv2Tutorial** со следующим содержимым:

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

## <a name="create-a-pipeline"></a>Создание конвейера
В рамках этого руководства вы создадите конвейер с действием копирования. Действие копирования использует **SqlServerDataset** в качестве входного набора данных, а **AzureBlobDataset** — в качестве выходного набора. Для типа источника задано значение **SqlSource**, а для типа приемника — значение **BlobSink**.

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


## <a name="create-a-pipeline-run"></a>Создание конвейера
Запустите выполнение конвейера SQLServerToBlobPipeline и запишите идентификатор выполнения конвейера для будущего мониторинга.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

1. Выполните указанный ниже скрипт, чтобы постоянно проверять состояние выполнения конвейера **SQLServerToBlobPipeline** и выводить конечный результат. Скопируйте приведенный ниже скрипт в окно PowerShell и нажмите клавишу ВВОД.

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

3. Вы можете получить идентификатор выполнения конвейера **SQLServerToBlobPipeline** и просмотреть подробный результат выполнения действия с помощью следующей команды: 

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
Конвейер автоматически создает выходную папку с именем `fromonprem` в контейнере больших двоичных объектов `adftutorial`. Убедитесь, что вы видите в выходной папке файл **dbo.emp.txt**. 

1. На странице контейнера **adftutorial** на портале Azure нажмите кнопку **Обновить**, чтобы появилась папка output.

    ![Папка output создана](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. В списке папок щелкните `fromonprem`. 
3. Убедитесь, что отображается файл `dbo.emp.txt`.

    ![Выходной файл](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Дальнейшие действия
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создайте локальную среду выполнения интеграции.
> * Создадите SQL Server и связанные службы Azure. 
> * Создадите SQL Server и наборы данных больших двоичных объектов Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

См. список хранилищ данных, [поддерживаемых фабрикой данных Azure](copy-activity-overview.md#supported-data-stores-and-formats).

Перейдите к следующему руководству, чтобы узнать о копировании данных в пакетном режиме из источника в место назначения:

> [!div class="nextstepaction"]
>[Копирование нескольких таблиц в пакетном режиме с помощью фабрики данных Azure](tutorial-bulk-copy.md)

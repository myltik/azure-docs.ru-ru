---
title: Копирование данных из базы данных SQL Server в хранилище BLOB-объектов с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из локального хранилища данных в облако Azure с помощью локальной среды выполнения интеграции в фабрике данных Azure.
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
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 5d362d8167cdfb772c70b02cc57bb49d3c2eb01d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31594381"
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Руководство. Копирование данных из локальной базы данных SQL Server в хранилище BLOB-объектов Azure
В этом руководстве для создания конвейера фабрики данных, который копирует данные из локальной базы данных SQL Server в хранилище BLOB-объектов Azure, будет использоваться Azure PowerShell. Вы создадите и будете использовать локальную среду выполнения интеграции, которая перемещает данные между локальным и облачным хранилищами данных. 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных Azure, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Эта статья не содержит подробный обзор службы фабрики данных. Дополнительную информацию см. в статье [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](introduction.md). 

Вот какие шаги выполняются в этом учебнике:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создайте локальную среду выполнения интеграции.
> * Создадите SQL Server и связанные службы Azure. 
> * Создадите SQL Server и наборы данных больших двоичных объектов Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

## <a name="prerequisites"></a>предварительным требованиям
### <a name="azure-subscription"></a>Подписка Azure.
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

### <a name="azure-roles"></a>Роли Azure
Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль *участника*, *владельца* либо *администратора* подписки Azure. 

Чтобы просмотреть имеющиеся разрешения в подписке, на портале Azure выберите "Имя пользователя" в правом верхнем углу и выберите **Разрешения**. Если у вас есть доступ к нескольким подпискам, выберите соответствующую подписку. Примеры инструкций по назначению пользователю роли см. в статье [о добавлении ролей](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 и 2017
В этом руководстве используйте локальную базу данных SQL Server в качестве *исходного* хранилища данных. Конвейер фабрики данных, созданный в рамках этого руководства, копирует данные из этой локальной базы данных SQL Server (источника) в хранилище BLOB-объектов Azure (приемник). Затем создайте таблицу с именем **emp** в базе данных SQL Server и вставьте в нее несколько примеров записей. 

1. Запустите среду SQL Server Management Studio. Если она еще не установлена на вашем компьютере, скачайте ее со страницы [Скачивание SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Подключитесь к экземпляру SQL Server с помощью учетных данных. 

3. Создайте пример базы данных. В представлении в виде дерева щелкните правой кнопкой мыши элемент **Базы данных** и выберите пункт **Новая база данных**. 
 
4. В окне **Новая база данных** введите имя базы данных и нажмите кнопку **ОК**. 

5. Чтобы создать таблицу **emp** и вставить в нее примеры данных, запустите приведенный ниже скрипт запроса к базе данных:

   ```
       INSERT INTO emp VALUES ('John', 'Doe')
       INSERT INTO emp VALUES ('Jane', 'Doe')
       GO
   ```

6. В представлении в виде дерева щелкните правой кнопкой мыши созданную базу данных и выберите пункт **Новый запрос**.

### <a name="azure-storage-account"></a>Учетная запись хранения Azure
В этом руководстве используйте учетную запись хранения Azure общего назначения (хранилища BLOB-объектов Azure) в качестве целевого (принимающего) хранилища данных. Если у вас нет учетной записи хранения Azure общего назначения, см. инструкции по [созданию учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account). Конвейер фабрики данных, созданный в рамках этого руководства, копирует данные из локальной базы данных SQL Server (источника) в это хранилище BLOB-объектов Azure (приемник). 

#### <a name="get-storage-account-name-and-account-key"></a>Получение имени и ключа учетной записи хранения
В этом руководстве вы будете использовать имя и ключ своей учетной записи хранения Azure. Выполнив следующие действия, получите имя и ключ учетной записи хранения: 

1. Войдите на [портал Azure](https://portal.azure.com), используя имя пользователя и пароль Azure. 

2. В левой области выберите **Больше служб**, отфильтруйте содержимое по ключевому слову **хранение**, а затем выберите **Учетные записи хранения**.

    ![Поиск учетной записи хранения](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. В списке учетных записей хранения найдите с помощью фильтра свою учетную запись хранения (при необходимости), а затем выберите эту учетную запись. 

4. В окне **Учетная запись хранения** выберите параметр **Ключи доступа**.

    ![Получение имени и ключа учетной записи хранения](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Скопируйте значения полей **Имя учетной записи хранения** и **key1**. Затем вставьте их в Блокнот или другой редактор для дальнейшего использования в руководстве. 

#### <a name="create-the-adftutorial-container"></a>Создание контейнера adftutorial 
В этом разделе вы создадите контейнер больших двоичных объектов с именем **adftutorial** в хранилище BLOB-объектов Azure. 

1. В окне **Учетная запись хранения** перейдите на вкладку **Обзор** и выберите **Blobs** (Большие двоичные объекты). 

    ![Выбор параметра "BLOB-объекты"](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. В окне **Служба BLOB-объектов** выберите **Контейнер**. 

    ![Кнопка добавления контейнера](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. В окне **Новый контейнер** в поле **Имя** введите **adftutorial**, а затем нажмите кнопку **ОК**. 

    ![Ввод имени контейнера](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. В списке контейнеров выберите **adftutorial**.  

    ![Выбор контейнера](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Не закрывайте окно **контейнера** **adftutorial**. Она понадобится для проверки выходных данных в конце этого руководства. Фабрика данных автоматически создает выходную папку в этом контейнере, поэтому ее не нужно создавать.

    ![Окно контейнера](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Установите Azure PowerShell
Установите последнюю версию среды Azure PowerShell, если она не установлена на компьютере. 

1. Перейдите на страницу [скачивания пакетов Azure SDK](https://azure.microsoft.com/downloads/). 

2. В разделе **Программы командной строки** в подразделе **PowerShell** выберите **Установка Windows**. 

3. Чтобы установить Azure PowerShell, запустите файл MSI. 

Подробные инструкции см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Вход в PowerShell

1. Запустите PowerShell на компьютере. Не закрывайте окно, выполняя инструкции, описанные в этом руководстве. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

    ![Запуск PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)

2. Выполните следующую команду и введите имя пользователя Azure и пароль, которые используются для входа на портал Azure.
       
    ```powershell
    Connect-AzureRmAccount
    ```        

3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку. Замените значение **SubscriptionId** на идентификатор подписки Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) (заключенное в двойные кавычки, например `"adfrg"`), а затем выполните команду. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

2. Чтобы создать группу ресурсов Azure, выполните следующую команду: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$resourceGroupName` другое значение и еще раз выполните команду.

3. Определите переменную для имени фабрики данных, которую в дальнейшем можно будет использовать в командах PowerShell. Имя должно начинаться с буквы или цифры и может содержать только буквы, цифры и дефисы (-).

    > [!IMPORTANT]
    >  Измените имя фабрики данных, чтобы оно было глобально уникальным. Например, укажите ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

4. Определите переменную для расположения фабрики данных: 

    ```powershell
    $location = "East US"
    ```  

5. Чтобы создать фабрику данных, выполните командлет: `Set-AzureRmDataFactoryV2` 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * Имя фабрики данных должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль *участника*, *владельца* либо *администратора* подписки Azure.
> * Сейчас с помощью фабрики данных версии 2 можно создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычислительные ресурсы (Azure HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Создание локальной среды выполнения интеграции

В этом разделе вы создадите локальную среду выполнения интеграции и свяжете ее с локальным компьютером, на котором находится база данных SQL Server. Локальная среда выполнения интеграции — это компонент, который копирует данные из базы данных SQL Server на компьютере в хранилище BLOB-объектов Azure. 

1. Создайте переменную для имени среды выполнения интеграции. Используйте уникальное имя и запишите его. Оно будет использоваться далее в этом руководстве. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

2. Создайте локальную среду выполнения интеграции. 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 
    Пример выходных данных:

    ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       : selfhosted IR description
    ```

3. Чтобы получить состояние созданной среды выполнения интеграции, выполните следующую команду:

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

4. Чтобы получить *ключи проверки подлинности* для регистрации локальной среды выполнения интеграции в службе фабрики данных в облаке, выполните следующую команду. Скопируйте один из ключей (без кавычек) для регистрации локальной среды выполнения интеграции, которую вы установите на компьютер на следующем шаге. 

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

## <a name="install-the-integration-runtime"></a>Установка среды выполнения интеграции
1. Скачайте [среду выполнения интеграции фабрики данных Azure](https://www.microsoft.com/download/details.aspx?id=39717) на локальный компьютер под управлением Windows и запустите установку. 

2. В окне **приветствия мастера установки Microsoft Integration Runtime** нажмите кнопку **Далее**.  

3. В окне **Лицензионное соглашение** примите условия использования и лицензионное соглашение и нажмите кнопку **Далее**. 

4. В окне **Конечная папка** нажмите кнопку **Далее**. 

5. В окне **Ready to install Microsoft Integration Runtime** (Все готово для установки Microsoft Integration Runtime) нажмите кнопку **Установить**. 

6. Если появится предупреждающее сообщение о том, что для компьютера, который не используется, настроен переход в спящий режим или режим гибернации, нажмите кнопку **ОК**. 

7. Если откроется окно **Электропитание**, закройте его и перейдите в окно установки. 

8. На странице **Completed the Microsoft Integration Runtime Setup** (Мастер установки Microsoft Integration Runtime завершит работу) нажмите кнопку **Готово**.

9. В окне **Регистрация среды выполнения интеграции (с локальным размещением)** вставьте ключ, созданный в предыдущем разделе, и нажмите кнопку **Зарегистрировать**. 

    ![Регистрация среды выполнения интеграции](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

    Когда локальная среда выполнения интеграции будет успешно зарегистрирована, вы увидите следующее сообщение: 

    ![Успешно зарегистрирована](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

10. В окне **Новый узел среды выполнения интеграции (с локальным размещением)** нажмите кнопку **Далее**. 

    ![Окно "Новый узел среды выполнения интеграции"](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

11. В окне **Коммуникационный канал интрасети** нажмите кнопку **Пропустить**.  
    Вы можете выбрать сертификацию TLS/SSL для защиты внутриузловой передачи данных в среде выполнения интеграции с несколькими узлами.

    ![Окно "Коммуникационный канал интрасети"](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)

12. В окне **Регистрация среды выполнения интеграции (с локальным размещением)** выберите **Запустить диспетчер конфигурации**. 

13. Когда узел будет подключен к облачной службе, вы увидите следующее сообщение:

    ![Узел подключен](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

14. Проверьте возможность подключения к базе данных SQL Server, сделав следующее:

    ![Вкладка «Диагностика»](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    a. В окне **Configuration Manager** перейдите на вкладку **Диагностика**.

    Б. В поле **Тип источника данных** выберите **SqlServer**.

    c. Введите имя сервера.

    d. Введите имя базы данных. 

    д. Выберите режим проверки подлинности. 

    f. Введите имя пользователя. 

    ж. Введите пароль, связанный с именем пользователя.

    h. Нажмите кнопку **проверки**, чтобы убедиться, что эта среда выполнения интеграции может подключаться к серверу SQL Server.  
    Если подключение выполнено успешно, появится зеленый флажок. В противном случае вы получите сообщение об ошибке. Исправьте ошибки и проверьте, может ли среда выполнения интеграции подключаться к экземпляру SQL Server.

    Запишите эти значения. Они будут использоваться далее в этом руководстве.
    
## <a name="create-linked-services"></a>Создание связанных служб
Чтобы связать хранилища данных и службы вычислений с фабрикой данных, создайте в ней связанные службы. В этом руководстве вы свяжете учетную запись хранения Azure и локальную базу данных экземпляра SQL Server с хранилищем данных. Связанные службы содержат сведения о подключении, используемые службой фабрики данных для подключения к ним в среде выполнения. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Создание связанных служб хранилища Azure (место назначения и приемник)
На этом шаге вы свяжете учетную запись хранения Azure с фабрикой данных.

1. Создайте JSON-файл с именем *AzureStorageLinkedService.json* в папке *C:\ADFv2Tutorial* со следующим кодом. Создайте папку *ADFv2Tutorial*, если она не создана.  

    > [!IMPORTANT]
    > Перед сохранением файла замените значения \<accountName> и \<accountKey> на имя вашей учетной записи хранения Azure и ее ключ. Вы записали их ранее в разделе [Предварительные требования](#get-storage-account-name-and-account-key).

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

2. В PowerShell перейдите в папку *C:\ADFv2Tutorial*.

3. Чтобы создать связанную службу AzureStorageLinkedService, выполните командлет: `Set-AzureRmDataFactoryV2LinkedService` 

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

    Если появляется ошибка "Файл не найден", выполните команду `dir`, чтобы убедиться, что он существует. Если имя файла имеет расширение *.txt* (например, AzureStorageLinkedService.json.txt), удалите расширение и выполните команду PowerShell еще раз. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Создание и шифрование связанной службы SQL Server (источник)
На этом шаге вы свяжете экземпляр локальной базы данных SQL Server с фабрикой данных.

1. Создайте JSON-файл с именем *SqlServerLinkedService.json* в папке *C:\ADFv2Tutorial* со следующим кодом:

    > [!IMPORTANT]
    > Выберите раздел в зависимости от типа проверки подлинности, который используется для подключения к SQL Server.

    **Использование проверки подлинности SQL:**

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

    **Использование проверки подлинности Windows:**

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
    > - Выберите раздел в зависимости от типа проверки подлинности, который используется для подключения к экземпляру SQL Server.
    > - Замените **\<integration runtime name>** именем своей среды выполнения интеграции.
    > - Перед сохранением файла замените **\<servername>**, **\<databasename>**, **\<username>** и **\<password>** значениями экземпляра SQL Server.
    > - Если в имени учетной записи пользователя или имени сервера необходимо использовать символ обратной косой черты (\\), добавьте escape-символ (\\). Например, *mydomain\\\\myuser*. 

2. Чтобы зашифровать конфиденциальные данные (имя пользователя, пароль и т. д.), выполните командлет `New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential`.  
    Шифрование гарантирует, что учетные данные будут зашифрованы с помощью программного интерфейса защиты данных (API защиты данных). Зашифрованные данные хранятся на узле локальной среды выполнения интеграции (на локальном компьютере). Полезные выходные данные можно перенаправить в другой JSON-файл (в этом случае *encryptedLinkedService.json*), содержащий зашифрованные учетные данные.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Выполните следующую команду, которая создает EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Создание наборов данных
На этом шаге вы создадите входные и выходные наборы данных. Они представляют собой входные и выходные данные для операции копирования данных из локальной базы данных SQL Server в хранилище BLOB-объектов Azure.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Создание набора данных для исходной базы данных SQL Server
На этом шаге вы определите набор данных, представляющий данные в экземпляре базы данных SQL Server. Это набор данных типа SqlServerTable. Он ссылается на связанную службу SQL Server, созданную на предыдущем шаге. Связанная служба содержит сведения о подключении, используемые службой фабрики данных для подключения к экземпляру SQL Server в среде выполнения. Этот набор данных указывает таблицу SQL в базе данных, содержащую эти данные. В нашем руководстве это таблица **emp**, содержащая исходные данные. 

1. Создайте JSON-файл с именем *SqlServerDataset.json* в папке *C:\ADFv2Tutorial* со следующим кодом:  

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

2. Чтобы создать набор данных SqlServerDataset, выполните командлет `Set-AzureRmDataFactoryV2Dataset`.

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

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Создание набора данных для хранилища BLOB-объектов Azure (приемника)
На этом шаге определите набор данных, которые необходимо копировать в хранилище BLOB-объектов Azure. Это набор данных типа AzureBlob. Он ссылается на связанную службу хранилища Azure, созданную ранее в рамках этого руководства. 

Связанная служба содержит сведения о подключении, используемые фабрикой данных для подключения к учетной записи хранения Azure в среде выполнения. В этом наборе данных указана папка в хранилище Azure, в которую копируются данные из базы данных SQL Server. В этом руководстве используется папка *adftutorial/fromonprem*, где `adftutorial` — это контейнер больших двоичных объектов, а `fromonprem` — собственно папка. 

1. Создайте JSON-файл с именем *AzureBlobDataset.json* в папке *C:\ADFv2Tutorial* со следующим кодом:

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

2. Чтобы создать набор данных AzureBlobDataset, выполните командлет `Set-AzureRmDataFactoryV2Dataset`.

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
В рамках этого руководства вы создадите конвейер с действием копирования. Действие копирования использует SqlServerDataset в качестве входного набора данных, а AzureBlobDataset — в качестве выходного. Для типа источника задано значение *SqlSource*, а для типа приемника — значение *BlobSink*.

1. Создайте JSON-файл с именем *SqlServerToBlobPipeline.json* в папке *C:\ADFv2Tutorial* со следующим кодом:

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

2. Чтобы создать конвейер SQLServerToBlobPipeline, выполните командлет `Set-AzureRmDataFactoryV2Pipeline`.

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
Запустите выполнение конвейера SQLServerToBlobPipeline и запишите идентификатор выполнения конвейера для последующего мониторинга.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

1. Чтобы постоянно проверять состояние выполнения конвейера SQLServerToBlobPipeline, выполните указанный ниже скрипт и выведите конечный результат:

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

2. Вы можете получить идентификатор выполнения конвейера SQLServerToBlobPipeline и просмотреть подробный результат выполнения действия с помощью следующей команды: 

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
Конвейер автоматически создает выходную папку с именем *fromonprem* в контейнере больших двоичных объектов `adftutorial`. Убедитесь, что вы видите в выходной папке файл *dbo.emp.txt*. 

1. В окне контейнера **adftutorial** на портале Azure нажмите кнопку **Обновить**, чтобы появилась входная папка.

    ![Входная папка создана](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. В списке папок выберите `fromonprem`. 
3. Убедитесь, что отображается файл `dbo.emp.txt`.

    ![Выходной файл](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создайте локальную среду выполнения интеграции.
> * Создадите SQL Server и связанные службы Azure. 
> * Создадите SQL Server и наборы данных больших двоичных объектов Azure.
> * Создадите конвейер с действием копирования для перемещения данных.
> * Запуск конвейера.
> * Выполнение мониторинга выполнения конвейера.

Список хранилищ данных, поддерживаемых фабрикой данных, см. в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Чтобы узнать о копировании данных в пакетном режиме из источника в место назначения, перейдите к следующему руководству:

> [!div class="nextstepaction"]
>[Копирование нескольких таблиц в пакетном режиме с помощью фабрики данных Azure](tutorial-bulk-copy.md)

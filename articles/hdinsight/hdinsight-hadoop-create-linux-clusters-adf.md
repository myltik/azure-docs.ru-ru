<properties
   pageTitle="Создание кластеров Hadoop под управлением Linux в HDInsight по запросу с помощью фабрики данных Azure | Microsoft Azure"
   	description="Узнайте, как создавать кластеры HDInsight по запросу с помощью фабрики данных Azure"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/16/2016"
   ms.author="jgao"/>

# Создание кластеров Hadoop под управлением Linux в HDInsight по запросу с помощью фабрики данных Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

[Фабрика данных Azure](../data-factory/data-factory-introduction.md) представляет собой облачную службу интеграции информации, которая организует и автоматизирует перемещение и преобразование данных. В этой статье вы узнаете, как создать [связанную службу Azure HDInsight по запросу](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) с помощью фабрики данных Azure и использовать кластер для выполнения задания Hive. Ниже приведена последовательность действий верхнего уровня.

1. Создание кластера HDInsight по запросу.
2. Запуск задания Hive для чтения необработанных данных веб-журнала из исходной учетной записи хранения BLOB-объектов, преобразования данных и записи выходных данных в целевую учетную запись хранения BLOB-объектов. 
3. Удаление кластера на основе параметра срока жизни.

Действие Hive, определенное в конвейере фабрики данных, вызывает предварительно определенный сценарий HiveQL. Сценарий создает внешнюю таблицу, которая ссылается на необработанные данные веб-журнала в хранилище BLOB-объектов Azure, а затем разбивает необработанные данные по годам и месяцам.

Вот пример строк за каждый месяц во входном файле.

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

На основании выходных данных сценарий создает три выходные папки. Каждая папка содержит файл с записями по каждому месяцу.

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Список действий преобразования данных фабрики данных (помимо действия Hive) см. в статье [Преобразования и анализ с помощью фабрики данных Azure](../data-factory/data-factory-data-transformation-activities.md).

Далее перечислены преимущества использования HDInsight с фабрикой данных.

- Счета за кластеры HDInsight выставляются пропорционально по минутам вне зависимости от того, пользовались вы кластером или нет. При использовании фабрики данных кластеры создаются по запросу. Кластеры автоматически удаляются по завершении задания. Поэтому вы платите только за время выполнения задания и непродолжительное время простоя (время жизни).
- Можно создать рабочий процесс с помощью конвейера фабрики данных.
- Можно запланировать выполнение рекурсивных заданий.  

##Предварительные требования:

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

- [Подписка Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure CLI или Azure PowerShell. 

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

##Подготовка учетной записи хранения

В этом сценарии можно использовать до трех учетных записей хранения:

- учетную запись хранения по умолчанию для кластера HDInsight;
- учетную запись хранения для входных данных;
- учетную запись хранения для выходных данных.

Для упрощения работы с учебником вы будете использовать одну учетную запись хранения для достижения 3 целей. Пример сценария Azure CLI и Azure PowerShell, приведенный в этом разделе, используется для выполнения перечисленных далее действий.

1. Вход в Azure.
2. Создание группы ресурсов Azure.
3. Создание учетной записи хранения Azure.
4. Создание контейнера BLOB-объектов в учетной записи хранения
5. Скопируйте следующие два файла в контейнер BLOB-объектов:

    - Файл входных данных: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - Сценарий HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    Оба файла хранятся в общедоступном контейнере больших двоичных объектов.

>[AZURE.IMPORTANT] Запишите имя группы ресурсов, имя учетной записи хранения и ключ учетной записи хранения, используемые в сценарии. Эти значения понадобятся в следующем разделе.

**Подготовка хранилища и копирование файлов с помощью Azure CLI**

    azure login
    
    azure config mode arm

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

Имя контейнера — *adfgetstarted*. Не изменяйте его. В противном случае потребуется обновить шаблон ARM.

Если вам необходима помощь в работе со сценарием CLI, см. статью [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](../storage/storage-azure-cli.md).

**Подготовка хранилища и копирование файлов с помощью Azure PowerShell**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName |  %{ $_.Key1 }

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

Если вам необходима помощь в работе со сценарием PowerShell, см. статью [Использование Azure PowerShell со службой хранилища Azure](../storage/storage-powershell-guide-full.md).

**Проверка учетной записи хранения и содержимого**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Щелкните **Группы ресурсов** на левой панели.
3. Дважды щелкните имя группы ресурсов, созданной в сценарии CLI или PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр. 
4. В элементе **Ресурсы** должен отображаться один ресурс, если только группа ресурсов не является общей для других проектов. Это учетная запись хранения с именем, указанным ранее. Щелкните имя учетной записи хранения.
5. Щелкните элемент **BLOB-объекты**.
6. Щелкните контейнер **adfgetstarted**. Вы увидите две папки — **input data** и **script**.
7. Откройте папку и проверьте файлы в папках.
 
## Создание фабрики данных

При наличии учетной записи хранения, входных данных и подготовленного сценария HiveQL вы можете создать фабрику данных Azure. Существует несколько способов создания фабрики данных. Для вызова настраиваемого шаблона ARM в этом учебнике будет использоваться портал Azure. Шаблон ARM можно также вызвать из [Azure CLI](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) и [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell). Описание других способов создания фабрики данных см. в документе [Учебник. Создание первой фабрики данных](../data-factory/data-factory-build-your-first-pipeline.md).

Содержимое шаблона ARM верхнего уровня:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Он содержит один ресурс фабрики данных с именем *hdinsight-hive-on-demand* (имя не показано на снимке экрана). В настоящее время фабрика данных поддерживается только в западной части США и Северной Европе.

Ресурс *hdinsight-hive-on-demand* содержит 4 ресурса:

- связанную службу для учетной записи хранения, которая будет использоваться в качестве учетной записи хранения HDInsight по умолчанию, хранилище входных данных и хранилище выходных данных.
- Создаваемая связанная служба для кластера HDInsight:

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    Даже если не указано, кластер создается в том же регионе, что и учетная запись хранения.
    
    Обратите внимание на параметр *timeToLive*. Фабрика данных автоматически удалит кластер по истечении 30 минут времени простоя.
- Набор данных для входных данных. Здесь определены имя файла и имя папки:

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- Набор данных для выходных данных, а затем конвейер для обработки данных. Здесь определен выходной путь:
        
        "folderPath": "adfgetstarted/partitioneddata",

    Параметр [dataset availability](../data-factory/data-factory-create-datasets.md#Availability) имеет следующий вид:
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    В фабрике данных Azure доступность выходного набора данных активирует конвейер. Это означает, что срез создается ежемесячно в последний день месяца. Дополнительные сведения см. в статье [Планирование и выполнение фабрики данных](../data-factory/data-factory-scheduling-and-execution.md).

    Определение конвейера имеет следующий вид:
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    Он содержит одно действие. Параметры *начала* и *окончания* действия имеют дату в прошлом, что означает, что будет существовать только один срез. Если окончание приходится на дату в будущем, фабрика данных создаст другой срез, когда наступит заданное время. Дополнительные сведения см. в статье [Планирование и выполнение фабрики данных](../data-factory/data-factory-scheduling-and-execution.md).

    Вот определение действия:
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    Определены входные данные, выходные данные и путь к сценарию.
    
**Создание фабрики данных**

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон ARM на портале Azure. Шаблон находится по адресу https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Введите значения для **DATAFACTORYNAME**, **STORAGEACCOUNTNAME** и **STORAGEACCOUNTKEY** для учетной записи, созданной в последнем разделе, и нажмите кнопку **ОК**. Имя фабрики данных должно быть глобально уникальным.
3. В разделе **Группа ресурсов** выберите ту же группу ресурсов, которая использовалась в последнем разделе.
4. Щелкните **Условия использования**, а затем нажмите кнопку **Создать**.
5. Щелкните **Создать**. На панели мониторинга появится элемент **Развертывание шаблона развертывания**. Подождите, пока текст элемента не изменится на имя группы ресурсов. Создание кластера HDInsight обычно занимает около 20 минут.
6. Щелкните элемент, чтобы открыть группу ресурсов. Теперь наряду с ресурсом учетной записи хранения должен отображаться еще один ресурс фабрики данных.
7. Щелкните **hdinsight-hive-on-demand**.
8. Щелкните элемент **Схема**. На схеме отображается одно действие с входным набором данных и выходным набором данных.

    ![Фабрика данных Azure — схема конвейера действия hive в HDInsight по запросу](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    Имена определены в шаблоне ARM.
9. Дважды щелкните **AzureBlobOutput**.
10. В разделе **Последние обновленные срезы** вы увидите один срез. Если отображается состояние **Выполняется**, подождите, пока оно не изменится на **Готово**.

**Проверка выходных данных фабрики данных**

1. Для проверки содержимого контейнера adfgetstarted используйте процедуру из последнего сеанса. Кроме контейнера **adfgetsarted**, существует два новых контейнера.

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx — это контейнер по умолчанию для кластера HDInsight. Имя контейнера по умолчанию соответствует шаблону "adf>yourdatafactoryname>-linkedservicename-datetimestamp". 
    - adfjobs — это контейнер для журналов заданий ADF.
    
    Выходные данные фабрики данных хранятся в afgetstarted, как было настроено в шаблоне ARM. 
2. Щелкните **adfgetstarted**.
3. Дважды щелкните **partitioneddata**. Вы увидите папку **year=2014**, так как все веб-журналы датированы 2014 годом. 

    ![Фабрика данных Azure — выходные данные конвейера действия hive в HDInsight по запросу](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Если перейти вниз по списку, вы увидите 3 папки за январь, февраль и март. Для каждого месяца существует журнал.

    ![Фабрика данных Azure — выходные данные конвейера действия hive в HDInsight по запросу](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##Очистка учебника

В случае связанной службы HDInsight по запросу кластер HDInsight создается для обработки каждого среза данных (если не используется динамический кластер (timeToLive)) и удаляется по завершении обработки. Для каждого кластера фабрика данных Azure создает хранилище BLOB-объектов Azure, используемое в качестве файловой системы по умолчанию для кластера. При удалении кластера HDInsight контейнер хранилища BLOB-объектов по умолчанию и соответствующая учетная запись не удаляются. Это сделано специально. По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Подобные контейнеры имеют имена вида "adfyourdatafactoryname-linkedservicename-datetimestamp".

[Диспетчер ресурсов Azure](../resource-group-overview.md) используется для развертывания, контроля и мониторинга решения в виде группы. При удалении группы ресурсов будут удалены все компоненты внутри нее.

**Удаление группы ресурсов**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Щелкните **Группы ресурсов** на левой панели.
3. Дважды щелкните имя группы ресурсов, созданной в сценарии CLI или PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр. Он позволит открыть группу ресурсов в новой колонке.
4. В элементе **Ресурсы** должны отображаться учетная запись хранения по умолчанию и фабрика данных, если только группа ресурсов не является общей для других проектов.
5. Щелкните **Удалить** в верхней части колонки. При этом также будет удалена учетная запись хранения и данные, хранящиеся в ней.
6. Введите имя группы ресурсов и нажмите кнопку **Удалить**.

Если вы не хотите удалять учетную запись хранения при удалении группы ресурсов, можно рассмотреть следующую архитектуру, отделив бизнес-данные от учетной записи хранения по умолчанию. В этом случае у вас будет одна группа ресурсов для учетной записи хранения с бизнес-данными, а другая — для учетной записи хранения по умолчанию и фабрики данных. Удаление второй группы ресурсов не затронет учетную запись хранения для бизнес-данных. Для этого выполните следующие действия:

- Вместе с ресурсом Microsoft.DataFactory/datafactories в шаблоне ARM добавьте следующее в группу ресурсов верхнего уровня. Будет создана учетная запись хранения.

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- Добавьте новую точку связанной службы в новую учетную запись хранения.

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- Настройте связанную службу HDInsight по запросу с дополнительными параметрами dependsOn и additionalLinkedServiceNames:

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##Дальнейшие действия
В этой статье вы узнали, как использовать фабрику данных Azure для создания кластера HDInsight по запросу для обработки заданий Hive. Дополнительные сведения

- [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Документация по HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Документация по фабрике данных](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0518_2016-->
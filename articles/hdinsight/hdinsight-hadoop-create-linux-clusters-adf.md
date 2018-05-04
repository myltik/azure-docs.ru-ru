---
title: Создание кластеров Hadoop по запросу с помощью фабрики данных — Azure HDInsight | Документы Майкрософт
description: Узнайте, как создавать кластеры Hadoop в HDInsight по запросу с помощью фабрики данных Azure.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: 6344b9a50f182a2b9ab05562c29099c9d6976f0b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Создание кластеров Hadoop в HDInsight по запросу с помощью фабрики данных Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Фабрика данных Azure](../data-factory/introduction.md) представляет собой облачную службу интеграции информации, которая организует и автоматизирует перемещение и преобразование данных. С ее помощью вы можете создать кластер Hadoop в HDInsight, когда это требуется для обработки входящих срезов данных. После завершения обработки кластер можно удалить. Ниже приведены некоторые преимущества использования кластера Hadoop HDInsight по требованию.

- Вы платите только за время выполнения задания в кластере Hadoop HDInsight (и непродолжительное настраиваемое время простоя). Счета за кластеры HDInsight выставляются пропорционально за минуту независимо от их использования. При использовании связанной службы HDInsight по требованию в фабрике данных можно создать кластеры по требованию. Кластеры автоматически удаляются по завершении задания. Таким образом, вы платите только за время выполнения задания и непродолжительное время простоя (параметр срока жизни).
- С помощью конвейера фабрики данных можно создать рабочий процесс. Например, у вас может быть конвейер для копирования данных из локального сервера SQL Server в хранилище BLOB-объектов Azure, обработки данных с помощью скриптов Hive и Pig в кластере Hadoop HDInsight по требованию. Скопируйте данные о результатах в хранилище данных SQL Azure для приложений бизнес-аналитики, необходимых для использования.
- Вы можете запланировать периодическое выполнение рабочего процесса (каждый час, день, неделю, месяц и т. д.).

В фабрике данных Azure фабрика данных может содержать один или несколько конвейеров. Конвейер данных включает одно или несколько действий. Существует два типа действий: [действия перемещения данных](../data-factory/copy-activity-overview.md) и [действия преобразования данных](../data-factory/transform-data.md). Для перемещения данных из исходного хранилища данных в целевое используются действия перемещения данных (сейчас только действие копирования). Для перемещения и обработки данных используются действия преобразования данных. Действие Hive HDInsight — одно из действий преобразования, которое поддерживает фабрика данных. В этом руководстве используется действие преобразования Hive.

Вы можете настроить действие Hive, чтобы использовать собственный кластер Hadoop HDInsight или кластер Hadoop HDInsight по требованию. В этом руководстве описывается настройка действия Hive в конвейере фабрики данных для использования кластера HDInsight по требованию. При выполнении действия для обработки среза данных происходит следующее.

1. Кластер Hadoop HDInsight автоматически создается для своевременной обработки среза.  
2. Для обработки входных данных выполняется скрипт HiveQL в кластере.
3. По завершении обработки кластер Hadoop HDInsight удаляется и не используется в течение заданного времени (параметр timeToLive). Если во время простоя (параметр timeToLive) можно обработать следующий срез данных, для этого используется тот же кластер.  

В этом руководстве описываются действия скрипта HiveQL, связанного с действием Hive.

1. Создается внешняя таблица, которая ссылается на необработанные данные веб-журнала в хранилище BLOB-объектов Azure.
2. Разделяются необработанные данные по году и месяцу.
3. Секционированные данные сохраняются в хранилище BLOB-объектов Azure.

В этом руководстве скрипт HiveQL, связанный с действием Hive, создает внешнюю таблицу, которая ссылается на необработанные данные веб-журнала в хранилище BLOB-объектов Azure. Вот пример строк за каждый месяц во входном файле.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Скрипт HiveQL разделяет необработанные данные по году и месяцу. Он создает три выходные папки на основе предыдущих входных данных. Каждая папка содержит файл с записями по каждому месяцу.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Сведения о преобразовании данных в фабрике данных см. в статье [Преобразование данных в фабрике данных Azure](../data-factory/transform-data.md).

> [!NOTE]
> Сейчас в фабрике данных Azure можно создать только кластер HDInsight версии 3.2.

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

* [Подписка Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Установите Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Подготовка учетной записи хранения
В этом сценарии можно использовать до трех учетных записей хранения:

- учетную запись хранения по умолчанию для кластера HDInsight;
- учетную запись хранения для входных данных;
- учетную запись хранения для выходных данных.

Чтобы упростить работу с руководством, для всех трех задач мы будем использовать одну учетную запись хранения. Ниже описаны действия для примера скрипта Azure PowerShell, приведенного в этом разделе.

1. Войдите в Azure.
2. Создание группы ресурсов Azure.
3. Создание учетной записи хранения Azure.
4. Создание контейнера больших двоичных объектов в учетной записи хранения.
5. Скопируйте следующие два файла в контейнер BLOB-объектов:

   * Файл входных данных: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Скрипт HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Оба файла хранятся в общедоступном контейнере больших двоичных объектов.


**Подготовка хранилища и копирование файлов с помощью Azure PowerShell:**
> [!IMPORTANT]
> Укажите имена для группы ресурсов Azure и учетную запись хранения Azure, которая будет создана с помощью скрипта.
> Запишите **имя группы ресурсов**, **имя учетной записи хранения** и **ключ учетной записи хранения**, выводимые скриптом. Они потребуются в следующем разделе.

```powershell
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
catch{Connect-AzureRmAccount}
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

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

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
```

Если вам необходима помощь в работе со скриптом PowerShell, необходимые сведения см. в статье [Использование Azure PowerShell со службой хранилища Azure](../storage/common/storage-powershell-guide-full.md). Если же вы хотите использовать Azure CLI, дополнительные сведения для скрипта Azure CLI см. в разделе [Приложение](#appendix).

**Проверка учетной записи хранения и содержимого**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Щелкните **Группы ресурсов** на левой панели.
3. Дважды щелкните имя группы ресурсов, созданной в скрипте PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр.
4. На плитке **Ресурсы** должен отображаться один ресурс, если только группа ресурсов не является общей для других проектов. Этим ресурсом будет учетная запись хранения с именем, которое вы указали ранее. Щелкните имя учетной записи хранения.
5. Щелкните элемент **BLOB-объекты** .
6. Щелкните контейнер **adfgetstarted** . Вы увидите две папки: **inputdata** и **script**.
7. Откройте папку и проверьте файлы в папках. Папка inputdata содержит файл input.log с входными данными, а папка script — файл скрипта HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Создание фабрики данных с помощью шаблона Resource Manager
При наличии учетной записи хранения, входных данных и подготовленного сценария HiveQL вы можете создать фабрику данных Azure. Существует несколько способов создания фабрики данных. Работая с этим руководством, вы создадите фабрику данных, развернув шаблон Resource Manager Azure на портале Azure. Вы также можете развернуть шаблон Resource Manager с помощью [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) или [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Описание других способов создания фабрики данных вы найдете в [руководстве по созданию фабрики данных](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure. Шаблон находится по адресу https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Дополнительные сведения о сущностях, определенных в шаблоне, см. в этом [разделе](#data-factory-entities-in-the-template). 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Для параметра **Группа ресурсов** выберите вариант **Использовать существующий**, а затем выберите имя группы ресурсов, созданной на предыдущем шаге (с использованием скрипта PowerShell).
3. Введите имя для фабрики данных (в поле **Имя фабрики данных**). Оно должно быть глобально уникальным.
4. Введите записанные на предыдущем шаге **имя учетной записи хранения** и **ключ учетной записи хранения**.
5. Ознакомившись с **условиями использования**, установите флажок **I agree to the terms and conditions** (Я принимаю указанные выше условия).
6. Кроме того, выберите **Закрепить на панели мониторинга**.
6. Щелкните **Purchase/Create** (Купить или Создать). На панели мониторинга появится плитка **Развертывание шаблона развертывания**. Подождите, пока для группы ресурсов откроется колонка **Группа ресурсов**. Чтобы открыть колонку группы ресурсов, можно также щелкнуть элемент с таким же названием, как имя вашей группы ресурсов.
6. Щелкните элемент, чтобы открыть группу ресурсов, если колонка группы ресурсов еще не открылась. Теперь наряду с ресурсом учетной записи хранения должен отображаться еще один ресурс фабрики данных.
7. Щелкните имя фабрики данных (значение, указанное для параметра **Имя фабрики данных**).
8. В колонке фабрики данных щелкните элемент **Схема**. На схеме отображается одно действие с входным набором данных и выходным набором данных.

    ![Фабрика данных Azure: схема конвейера действий Hive в кластере HDInsight по запросу](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Эти имена определяются в шаблоне Resource Manager.
9. Дважды щелкните **AzureBlobOutput**.
10. В разделе **Последние обновленные срезы**вы увидите один срез. Если отображается состояние **Выполняется**, подождите, пока оно не изменится на **Готово**. Создание кластера HDInsight обычно занимает около **20 минут**.

### <a name="check-the-data-factory-output"></a>Проверка выходных данных фабрики данных

1. Для проверки содержимого контейнера adfgetstarted используйте процедуру из предыдущего сеанса. Кроме контейнера **adfgetsarted**, существует два новых контейнера.

   * Контейнер с именем, соответствующим шаблону: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Этот контейнер для кластера HDInsight используется в качестве контейнера по умолчанию.
   * Контейнер для журналов заданий ADF — adfjobs.

     Выходные данные фабрики данных хранятся в контейнере **afgetstarted** в соответствии с настройками в шаблоне Resource Manager.
2. Щелкните **adfgetstarted**.
3. Дважды щелкните **partitioneddata**. Вы увидите папку **year=2014**, так как все веб-журналы датированы 2014 годом.

    ![Фабрика данных Azure: выходные данные конвейера действия Hive в кластере HDInsight по запросу](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Если перейти вниз по списку, вы увидите 3 папки за январь, февраль и март. Для каждого месяца существует журнал.

    ![Фабрика данных Azure: выходные данные конвейера действия Hive в кластере HDInsight по запросу](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Сущности фабрики данных в шаблоне
Шаблон Resource Manager верхнего уровня для фабрики данных выглядит следующим образом:

```json
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
```

### <a name="define-data-factory"></a>Определение фабрики данных
Фабрику данных следует определить в шаблоне Resource Manager, как показано в следующем примере:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
dataFactoryName — это имя фабрики данных, указанное при развертывании шаблона. Сейчас фабрика данных поддерживается только в восточной и западной частях США, а также в Северной Европе.

### <a name="defining-entities-within-the-data-factory"></a>Определение сущностей в фабрике данных
В шаблоне JSON определены следующие сущности фабрики данных:

* [Связанная служба хранения Azure](#azure-storage-linked-service)
* [Связанная служба кластера HDInsight по запросу](#hdinsight-on-demand-linked-service)
* [Входной набор данных большого двоичного объекта Azure](#azure-blob-input-dataset)
* [Выходной набор данных большого двоичного объекта Azure](#azure-blob-output-dataset)
* [Конвейер данных с действием копирования](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Связанная служба хранения Azure
Связанная служба хранилища Azure связывает учетную запись хранения Azure с фабрикой данных. В этом руководстве одна и та же учетная запись хранения используется в качестве учетной записи хранения HDInsight, хранилища входных и выходных данных. Таким образом, вы определяете только одну связанную службу хранилища Azure. В определении связанной службы необходимо указать имя и ключ учетной записи хранения Azure. Дополнительные сведения о свойствах JSON для определения связанной службы хранилища Azure см. в разделе [Связанная служба хранилища Azure](../data-factory/connector-azure-blob-storage.md).

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
Для **connectionString** используются параметры storageAccountName и storageAccountKey. Вам необходимо указать значения для этих параметров при развертывании шаблона.  

#### <a name="hdinsight-on-demand-linked-service"></a>Связанная служба кластера HDInsight по запросу
В определении связанной службы HDInsight по требованию необходимо указать значения для параметров конфигурации, которые служба фабрики данных использует для создания кластера Hadoop HDInsight во время выполнения. Дополнительные сведения о свойствах JSON, используемых для определения связанной службы кластера HDInsight по запросу, см. в статье [Связанные службы вычислений](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service).  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Обратите внимание на следующие моменты.

* Фабрика данных создает кластер HDInsight **под управлением Linux**.
* Кластер Hadoop HDInsight создается в том же регионе, что и учетная запись хранения.
* Обратите внимание на параметр *timeToLive* . Фабрика данных автоматически удалит кластер по истечении 30 минут времени простоя.
* Кластер HDInsight создает **контейнер по умолчанию** в хранилище BLOB-объектов, указанном в коде JSON (**linkedServiceName**). При удалении кластера HDInsight этот контейнер не удаляется. В этом весь замысел. Если используется связанная служба HDInsight по запросу, кластер HDInsight создается всякий раз, когда нужно обработать срез данных (если не используется динамический кластер**timeToLive**), после чего кластер удаляется.

Дополнительные сведения см. в разделе [Связанная служба Azure HDInsight по запросу](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

> [!IMPORTANT]
> По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Имена контейнеров указаны в формате adf**имя_фабрики_данных**-**имя_связанной_службы**-метка_даты_и_времени. Для удаления контейнеров в хранилище BLOB-объектов Azure используйте такие инструменты, как [Microsoft Storage Explorer](http://storageexplorer.com/) .

#### <a name="azure-blob-input-dataset"></a>Входной набор данных большого двоичного объекта Azure
В определении входного набора данных укажите имена контейнера больших двоичных объектов, папки и файла, содержащего входные данные. Подробные сведения о свойствах JSON, которые используюся для определения набора данных большого двоичного объекта Azure, см. в разделе [Свойства типа "Набор данных большого двоичного объекта Azure"](../data-factory/connector-azure-blob-storage.md).

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

Обратите внимание на следующие характерные параметры в определении JSON:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Выходной набор данных BLOB-объекта Azure
В определении выходного набора данных укажите имена контейнера больших двоичных объектов и папки, содержащей выходные данные. Подробные сведения о свойствах JSON, которые используюся для определения набора данных большого двоичного объекта Azure, см. в разделе [Свойства типа "Набор данных большого двоичного объекта Azure"](../data-factory/connector-azure-blob-storage.md).  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

FolderPath задает путь к папке, содержащей выходные данные.

```json
"folderPath": "adfgetstarted/partitioneddata",
```

Параметр [dataset availability](../data-factory/concepts-datasets-linked-services.md) имеет следующий вид:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

В фабрике данных Azure доступность выходного набора данных активирует конвейер. В этом примере срез создается ежемесячно в последний день месяца (EndOfInterval). 

#### <a name="data-pipeline"></a>Конвейер данных
Здесь вы определите конвейер для преобразования данных. Для этого нужно запустить скрипт Hive в кластере HDInsight Azure по требованию. В разделе [Конвейер JSON](../data-factory/concepts-pipelines-activities.md) описаны элементы JSON, используемые для определения конвейера в этом примере.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
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
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

Конвейер содержит одно действие (действие HDInsightHive). Так как даты начала и окончания находятся в пределах января 2016 г., обрабатываются данные только за один месяц (срез). И в *начале*, и в *конце* действия указана прошедшая дата, поэтому фабрика данных обрабатывает данные сразу за месяц. Если окончание приходится на дату в будущем, фабрика данных создаст другой срез, когда наступит заданное время. Дополнительные сведения см. в статье [Планирование и исполнение с использованием фабрики данных](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Очистка учебника

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Удаление контейнеров больших двоичных объектов, созданных кластером HDInsight по требованию
В случае связанной службы HDInsight по запросу кластер HDInsight создается для обработки каждого среза данных (если не используется динамический кластер (timeToLive)) и удаляется по завершении обработки. Для каждого кластера фабрика данных Azure создает контейнер больших двоичных объектов в хранилище BLOB-объектов Azure, которое используется в качестве учетной записи хранения по умолчанию для кластера. При удалении кластера HDInsight контейнер хранилища BLOB-объектов по умолчанию и соответствующая учетная запись не удаляются. В этом весь замысел. По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Имена этих контейнеров указаны по шаблону `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Удалите папки **adfjobs** и **adfyourdatafactoryname-linkedservicename-datetimestamp**. Контейнер adfjobs содержит журналы заданий из фабрики данных Azure.

### <a name="delete-the-resource-group"></a>удаление группы ресурсов.
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) используется для развертывания, контроля и мониторинга решения в виде группы.  Удаление группы ресурсов приведет к удалению всех компонентов внутри группы.  

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Щелкните **Группы ресурсов** на левой панели.
3. Щелкните имя группы ресурсов, созданной в сценарии PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр. Он позволит открыть группу ресурсов в новой колонке.
4. В элементе **Ресурсы** должны отображаться учетная запись хранения по умолчанию и фабрика данных, если только группа ресурсов не является общей для других проектов.
5. Нажмите кнопку **Удалить** в верхней части колонки. При этом также будет удалена учетная запись хранения и данные, хранящиеся в ней.
6. Введите имя группы ресурсов, чтобы подтвердить удаление, и щелкните **Удалить**.

Если вы не хотите удалять учетную запись хранения при удалении группы ресурсов, можно использовать следующую архитектуру, отделив бизнес-данные от учетной записи хранения по умолчанию. В этом случае у вас будет одна группа ресурсов для учетной записи хранения с бизнес-данными, а другая — для учетной записи хранения по умолчанию для связанной службы HDInsight и фабрики данных. Удаление второй группы ресурсов не затронет учетную запись хранения бизнес-данных. Для этого выполните следующие действия:

* Вместе с ресурсом Microsoft.DataFactory/datafactories в шаблоне Resource Manager добавьте следующее в группу ресурсов верхнего уровня. При этом будет создана учетная запись хранения.

    ```json
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
    ```
* Добавьте новую точку связанной службы в новую учетную запись хранения.

    ```json
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
    ```
* Настройте связанную службу HDInsight по запросу с дополнительными параметрами dependsOn и additionalLinkedServiceNames:

    ```json
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
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнали, как использовать фабрику данных Azure для создания кластера HDInsight по запросу для обработки заданий Hive. Дополнительные сведения

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Документация по HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Документация по фабрике данных](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Приложение

### <a name="azure-cli-script"></a>Скрипт Azure CLI
Для этого руководства вместо Azure PowerShell можно использовать Azure CLI. Чтобы использовать Azure CLI, вам необходимо сначала выполнить установку:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Подготовка хранилища и копирование файлов с помощью Azure CLI

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

Имя контейнера — *adfgetstarted*. Не изменяйте его. В противном случае потребуется обновить шаблон Resource Manager. Дополнительные сведения об этом сценарии CLI см. в статье [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](../storage/common/storage-azure-cli.md).

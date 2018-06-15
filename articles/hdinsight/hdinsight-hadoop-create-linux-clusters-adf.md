---
title: Руководство. Создание кластеров Hadoop в Azure HDInsight по запросу с помощью фабрики данных | Документация Майкрософт
description: Узнайте, как создавать кластеры Hadoop в HDInsight по запросу с помощью фабрики данных Azure.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nitinme
ms.openlocfilehash: 53ff14e00b88f6d182579ba0d9df630fae9b3d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771139"
---
# <a name="tutorial-create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Руководство. Создание кластеров Hadoop в HDInsight по запросу с помощью фабрики данных Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

В этой статье вы узнаете, как создать кластер Hadoop, по запросу в Azure HDInsight с помощью фабрики данных Azure. Затем как использовать конвейеры данных в фабрике данных Azure для выполнения заданий Hive и удалить кластер. Изучив это руководство вы узнаете, как реализовать запуск задания больших данных, когда создание кластера, выполнение задания и удаление кластера выполняются по расписанию.

В рамках этого руководства рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Создание учетной записи хранения Azure
> * Суть происходящего в фабрике данных Azure
> * Создание фабрики данных с помощью портала Azure
> * Создание связанных служб
> * Создание конвейера
> * Активация конвейера
> * Мониторинг конвейера
> * Проверка выходных данных

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* Установите Azure PowerShell. Инструкции см. в статье [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

* Субъект-служба Azure Active Directory. После создания субъект-службы, обязательно получите **идентификатор приложения** и **ключ проверки подлинности** с помощью инструкций в связанной статье. Эти значения вам понадобятся позже (в рамках этого руководства). Кроме того, убедитесь, что субъект-службе должна быть назначена роль *участника* подписки или группы ресурсов, в которой создается кластер. Инструкции по получению требуемых значений и назначению правильных ролей см. в разделе [Создание субъект-службы Azure Active Directory ](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

В этом разделе создайте учетную запись хранилища, которая будет использоваться в качестве хранилища по умолчанию, для кластера HDInsight, который создается по требованию. Эта учетная запись хранения также содержит пример сценария HiveQL (**hivescript.hql**), который позволяет моделировать образец задания Hive, работающего в кластере.

В этом разделе используется сценарий Azure PowerShell для создания учетной записи хранения и копирования необходимых файлов в учетную запись хранения. Ниже описаны действия для примера скрипта Azure PowerShell, приведенного в этом разделе.

1. Вход в Azure.
2. Создает группы ресурсов Azure.
3. Создает учетную запись хранения Azure.
4. Создание контейнера больших двоичных объектов в учетной записи хранения
5. Копирует пример сценария HiveQL (**hivescript.hql**) в контейнер больших двоичных объектов. Сценарий доступен по адресу [https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Пример сценария, который уже доступен в другом общедоступном контейнере больших двоичных объектов. Приведенный ниже сценарий PowerShell создает копии этих файлов в учетной записи хранилища Azure, которую он создал.


**Чтобы создать учетную запись хранения и скопировать файлы с помощью Azure PowerShell:**
> [!IMPORTANT]
> Укажите имена для группы ресурсов Azure и учетную запись хранения Azure, которая будет создана с помощью скрипта.
> Запишите **имя группы ресурсов**, **имя учетной записи хранения** и **ключ учетной записи хранения**, выводимые скриптом. Они потребуются в следующем разделе.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
<<<<<<< HEAD
Login-AzureRmAccount
=======
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
>>>>>>> refs/remotes/MicrosoftDocs/release-build-hdinsight-2018
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

**Проверка создания учетной записи хранения**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Выберите **Группы ресурсов** на левой панели.
3. Дважды щелкните имя группы ресурсов, созданной в скрипте PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр.
4. На плитке **Ресурсы** должен отображаться один ресурс, если только группа ресурсов не является общей для других проектов. Этим ресурсом будет учетная запись хранения с именем, которое вы указали ранее. Выберите имя учетной записи хранения.
5. Выберите плитки **больших двоичных объектов**.
6. Выберите контейнер **adfgetstarted** . Перед вами папка с именем **hivescripts**.
7. Откройте папку, она должна содержать образец файла сценария **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Суть происходящего в фабрике данных Azure

[Фабрика данных Azure](../data-factory/introduction.md) координирует и автоматизирует процессы перемещения и преобразования данных. С ее помощью вы можете создать кластер Hadoop в HDInsight, когда это требуется для обработки входящих срезов данных. После завершения обработки кластер можно удалить. 

В фабрике данных Azure фабрика данных может содержать один или несколько конвейеров. Конвейер данных включает одно или несколько действий. Существует два вида действий:

* Для перемещения данных из исходного хранилища данных в целевое используются [действия перемещения данных](../data-factory/copy-activity-overview.md).
* [Действия по преобразованию данных](../data-factory/transform-data.md). Для перемещения и обработки данных используются действия преобразования данных. Действие Hive HDInsight — одно из действий преобразования, которое поддерживает фабрика данных. В этом руководстве используется действие преобразования Hive.

В этой статье идет речь о настройке действия Hive для создания по требованию кластера Hadoop в HDInsight. При выполнении действия по обработке данных, происходит следующее.

1. Автоматически создается кластер Hadoop HDInsight для своевременной обработки среза. 

2. Для обработки входных данных выполняется скрипт HiveQL в кластере. В этом руководстве описываются действия скрипта HiveQL, связанного с действием Hive.

    * Он использует существующую таблицу (*hivesampletable*) для создания другой таблицы **HiveSampleOut**.
    * Скрипт заполняет таблицу **HiveSampleOut** только определенными столбцами из исходной таблицы *hivesampletable*.

3. По завершении обработки кластер Hadoop HDInsight удаляется и не используется в течение заданного времени (параметр timeToLive). Если во время простоя (параметр timeToLive) можно обработать следующий срез данных, для этого используется тот же кластер.  

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Войдите на [портал Azure](https://portal.azure.com/).

2. На портале Azure выберите **Создать ресурс** > **Данные+аналитика** > **Фабрика данных**.

    ![Фабрика данных Azure на портале](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Фабрика данных Azure на портале")

2. Введите или выберите значения, как показано на следующем снимке экрана:

    ![Создание фабрики данных Azure с помощью портала Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Создание фабрики данных Azure с помощью портала Azure")

    Введите или выберите следующие значения:
    
    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя** |  Введите имя фабрики данных. Оно должно быть глобально уникальным.|
    |**Подписка**     |  Выберите подписку Azure. |
    |**Группа ресурсов**     | Выберите **Использовать существующую** и затем выберите группу ресурсов, созданную с помощью сценария PowerShell. |
    |**Версия**     | Выберите **V2 (предварительная версия)** |
    |**Местоположение.**     | Автоматически задается то расположение, которое было указано при предыдущем создании группы ресурсов. В этом руководстве расположение установлено как **восточная часть США 2**. |
    

3. Установите флажок **Закрепить на панели мониторинга** и щелкните **Создать**. Вы увидите новую плитку под названием **Отправляется развертывание** на панели мониторинга портала. Создание фабрики данных может занять от 2 до 4 минут.

    ![Ход выполнения развертывания шаблона](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "Ход выполнения развертывания шаблона") 
 
4. После создания фабрики данных на портале будет показан обзор фабрики данных.

    ![Обзор фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Обзор фабрики данных Azure")

5. Выберите **Создание и мониторинг** для запуска портала создания и наблюдения фабрики данных Azure.

## <a name="create-linked-services"></a>Создание связанных служб

В этом разделе вы создаете две связанные службы на своей фабрике данных.

- **Связанную службу хранилища Azure**, которая связывает учетную запись хранения Azure с фабрикой данных. Это хранилище используется кластером HDInsight по запросу. Оно также содержит сценарий Hive, выполняющийся в кластере.
- **Связанную службу HDInsight по запросу**. Фабрика данных Azure автоматически создает кластер HDInsight и запускает сценарий Hive. Кластер Hadoop удаляется, если он не используется в течение заданного времени.

###  <a name="create-an-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure

1. В левой области страницы **Начать работу** выберите значок **Правка**.

    ![Создание связанной службы фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Создание связанной службы фабрики данных Azure")

2. Выберите в нижнем левом углу окна кнопку **Подключения**, а затем выберите **+Создать**.

    ![Создание подключений в фабрике данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Создание подключений в фабрике данных Azure")

3. В диалоговом окне **New Linked Service** (Новая связанная служба) выберите **Хранилище BLOB-объектов Azure** и щелкните **Продолжить**.

    ![Создание связанной службы хранения Azure для фабрики данных](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Создание связанной службы хранения Azure для фабрики данных")

4. Укажите имя для связанной службы хранилища, выберите созданную учетную запись хранения Azure в рамках сценария PowerShell и выберите **Готово**.

    ![Укажите имя для связанной службы хранилища Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Укажите имя для связанной службы хранилища Azure")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Создание связанной службы HDInsight по запросу

1. Снова нажмите кнопку **+ Создать**, чтобы создать еще одну связанную службу.

2. В окне **New Linked Service** (Новая связанная служба) выберите **Среда выполнения приложений** > **Azure HDInsight**, а затем выберите **Continue** (Продолжить).

    ![Создание связанной службы HDInsight для фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Создание связанной службы HDInsight для фабрики данных Azure")

3. В окне **New Linked Service** (Новая связанная служба), укажите нужные значения.

    ![Указание значений для связанной службы HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Указание значений для связанной службы HDInsight")

    Введите следующие значения и оставьте остальные по умолчанию.

    | Свойство | ОПИСАНИЕ |
    | --- | --- |
    | ИМЯ | Введите имя для связанной службы HDInsight |
    | type | Выберите **HDInsight по требованию** |
    | Связанная служба хранилища Azure | Выберите связанную службу хранилища, созданную ранее |
    | Тип кластера | Выберите **hadoop** |
    | Срок жизни | Укажите продолжительность времени, в течение которого кластер HDInsight должен быть доступен до его автоматического удаления|
    | Идентификатор субъекта-службы | Укажите идентификатор приложения субъекта-службы Azure Active Directory, созданного в рамках предварительных требований |
    | Ключ субъект-службы | Укажите ключ проверки подлинности для субъект-службы Azure Active Directory |
    | Префикс имени кластера | Укажите значение, которое будет добавлено как префикс ко всем типам кластера, созданным фабрикой данных |
    | Группа ресурсов | Выберите группу ресурсов, созданную в рамках сценария PowerShell, которая использовалась ранее| 
    | Имя пользователя кластера SSH | Введите имя пользователя SSH |
    | Пароль кластера SSH | Укажите пароль пользователя SSH. |

    Выберите **Готово**.

## <a name="create-a-pipeline"></a>Создание конвейера

1. Нажмите кнопку **+** (плюс) и выберите **Pipeline** (Конвейер).

    ![Создание конвейера в фабрике данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Создание конвейера в фабрике данных Azure")

2. В панели инструментов **Действия** разверните **HDInsight** и перетащите действие **Hive** в область конструктора конвейера. Во вкладке **Общие** укажите имя для действия.

    ![Добавление действий в конвейер фабрики данных](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Добавление действий в конвейер фабрики данных")

3. Убедитесь, что выбрано действие Hive, выберите вкладку **Кластер HDI** и в раскрывающемся списке **Связанная служба HDInsight** выберите связанную службу, созданную ранее для HDInsight.

    ![Указание сведений о кластере HDInsight для конвейера](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Указание сведений о кластере HDInsight для конвейера")

4. Выберите вкладку **Скрипт** и затем сделайте следующее:

    a. Для **Связанной службы скрипта**выберите **HDIStorageLinkedService**. Это — связанная служба хранилища, созданная ранее.

    Б. Для **Путь к файлу** выберите **Поиск в хранилище** и перейдите в расположение, где находится образец скрипта Hive. Если ранее выполнялся скрипт PowerShell, это расположение должно быть `adfgetstarted/hivescripts/hivescript.hql`.

    ![Указание сведений о скрипте Hive для конвейера](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Указание сведений о скрипте Hive для конвейера")

    c. В разделе **Расширенный** > **Параметры** выберите **Автозаполнение из скрипта**. Этот параметр ищет любые параметры в сценарии Hive, которым требуются значения во время выполнения. Использованный сценарий (**hivescript.hql**) имеет параметр **Вывод**. Укажите значение в формате `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` для указания существующей папки в хранилище Azure. Путь следует вводить с учетом регистра. Это путь, где будут храниться выходные данные сценария.

    ![Укажите параметры для скрипта Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Укажите параметры для скрипта Hive")

5. Чтобы проверить конвейер, выберите **Проверить**. Чтобы закрыть окно проверки, нажмите кнопку **>>** Стрелка вправо.

    ![Проверка конвейера фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Проверка конвейера фабрики данных Azure")

5. Наконец, выберите **Опубликовать все** для публикации артефактов фабрики данных Azure.

    ![Публикация конвейера фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Публикация конвейера фабрики данных Azure")

## <a name="trigger-a-pipeline"></a>Активация конвейера

1. Из панели инструментов на поверхности конструктора выберите **Триггер** > **Активировать сейчас**.

    ![Активация конвейера фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Активация конвейера фабрики данных Azure")

2. Выберите **Готово** во всплывающей боковой панели.

## <a name="monitor-a-pipeline"></a>Мониторинг конвейера

1. Перейдите на вкладку **Мониторинг** слева. Вы увидите, что запуск конвейера появится в списке **Pipeline Runs** (Запуски конвейера). Обратите внимание на состояние выполнения в столбце **Состояние**.

    ![Мониторинг конвейера фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Мониторинг конвейера фабрики данных Azure")

2. Щелкните **Обновить**, чтобы обновить состояние.

3. Можно также выбрать значок **Представление выполнения действия** для просмотра выполнения действия связанного с конвейером. На снимке экрана ниже видно только одно выполнение действия, так как в созданном конвейере есть только одно действие. Чтобы вернуться к предыдущему представлению, выберите **Конвейеры** в верхней части страницы.

    ![Мониторинг действий конвейера фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Мониторинг действий конвейера фабрики данных Azure")


## <a name="verify-the-output"></a>Проверка выходных данных

1. Чтобы проверить выходные данные, на портале Azure перейдите к учетной записи хранения, используемой в этом руководстве. Там должны быть следующие папки или контейнеры.

    - **adfgerstarted/outputfolder**, содержащий выходные данные сценария Hive, который был запущен в рамках конвейера.

    - Контейнер **adfhdidatafactory-\<linked-service-name>-\<timestamp>**. Этот контейнер по умолчанию является местом хранения кластера HDInsight, который был создан в рамках выполнения конвейера.

    - Контейнер **adfjobs**, содержащий журнал заданий фабрики данных Azure.  

        ![Проверка выходных данных конвейера фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Проверка выходных данных конвейера фабрики данных Azure")


## <a name="clean-up-the-tutorial"></a>Очистка учебника

Создавая кластер HDInsight по требованию, нет необходимости явно удалять кластер HDInsight. Удаление кластера заложено в основе конфигурации, которая указывается при создании конвейера. Тем не менее даже после удаления кластера, учетные записи хранения, связанные с кластером, продолжают существовать. Это сделано намеренно, чтобы сохранить данные без изменений. Однако, если вы не хотите сохранять данные, можно удалить созданную учетную запись хранения.

Кроме того можно целиком удалить группу ресурсов, созданную в этом руководстве. Это приведет к удалению учетной записи хранения и созданной фабрики данных Azure.

### <a name="delete-the-resource-group"></a>удаление группы ресурсов.

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Выберите **Группы ресурсов** на левой панели.
3. Выберите имя группы ресурсов, созданной в сценарии PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр. Он открывает группу ресурсов.
4. В элементе **Ресурсы** должны отображаться учетная запись хранения по умолчанию и фабрика данных, если только группа ресурсов не является общей для других проектов.
5. Выберите **Удалить группу ресурсов**. При этом также будет удалена учетная запись хранения и данные, хранящиеся в ней.

    ![Удаление группы ресурсов](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Удаление группы ресурсов")

6. Введите имя группы ресурсов, чтобы подтвердить удаление, и нажмите **Удалить**.


## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнали, как использовать фабрику данных Azure для создания кластера HDInsight по требованию и выполнения заданий Hive. Перейдите к следующей статье, чтобы узнать, как создавать кластеры HDInsight с настраиваемой конфигурацией.

> [!div class="nextstepaction"]
>[Создание кластеров Azure HDInsight с настраиваемой конфигурацией](hdinsight-hadoop-provision-linux-clusters.md)



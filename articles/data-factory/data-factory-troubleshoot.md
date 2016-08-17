<properties 
	pageTitle="Устранение неполадок фабрики данных Azure" 
	description="Узнайте, как устранять неполадки при использовании фабрики данных Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2016" 
	ms.author="spelluru"/>

# Устранение неполадок фабрики данных
В этой статье приводятся советы по устранению неполадок, возникающих при использовании фабрики данных Azure. Здесь указаны не все возможные проблемы использования службы, однако рассматриваются некоторые вопросы и приводятся общие рекомендации по устранению неполадок.

## Советы по устранению неполадок

### Ошибка. Подписка не зарегистрирована для использования пространства имен "Microsoft.DataFactory".
Если эта ошибка возникает, поставщик ресурсов фабрики данных Azure не был зарегистрирован на компьютере. Выполните следующие действия:

1. Запустите Azure PowerShell.
2. Войдите в свою учетную запись Azure с помощью следующей команды: Login-AzureRmAccount
3. Выполните следующую команду, чтобы зарегистрировать поставщик фабрики данных Azure: Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

### Проблема: ошибка авторизации при выполнении командлета фабрики данных
Скорее всего, для Azure PowerShell используется неправильная учетная запись или подписка Azure. Чтобы выбрать правильную учетную запись и подписку Azure для использования с Azure PowerShell, используйте такие командлеты:

1. Login-AzureRmAccount — ввод имени пользователя и пароля.
2. Get-AzureRmSubscription — просмотр всех подписок для этой учетной записи.
3. Select-AzureRmSubscription <имя\_подписки> — выбор нужной подписки. Используйте ту же подписку, что применялась для создания фабрики данных на классическом портале Azure.

### Проблема: не удается запустить экспресс-установку шлюза данных с портала Azure
Для экспресс-установки шлюза данных требуется Internet Explorer или другой веб-браузер, совместимый с Microsoft ClickOnce. Если не удается запустить экспресс-установку, выполните одно из следующих действий:

- Используйте Internet Explorer или другой браузер, совместимый с Microsoft ClickOnce.

	Если вы используете браузер Chrome, перейдите в [интернет-магазин Chrome](https://chrome.google.com/webstore/), введите ClickOnce в строке поиска, а затем выберите и установите одно из расширений ClickOnce.
	
	То же самое (установку расширения) необходимо сделать и в Firefox. Нажмите кнопку "Открыть меню" на панели инструментов (три горизонтальные линии в правом верхнем углу), нажмите кнопку "Надстройки", введите "ClickOnce" в строку поиска, выберите одно из расширений ClickOnce и установите его.

- Щелкните ссылку **Установка вручную** в той же колонке на портале, чтобы скачать файл установки и запустить его вручную. После успешного завершения установки откроется диалоговое окно настройки шлюза управления данными. Скопируйте **ключ** на экране портала и используйте его в диспетчере конфигурации, чтобы вручную зарегистрировать шлюз в службе.

### Проблема: не удается подключиться к локальному серверу SQL Server 
Запустите **диспетчер конфигурации шлюза управления данными** на компьютере шлюза и используйте вкладку **Устранение неполадок**, чтобы проверить подключение к SQL Server с компьютера шлюза. Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).
 

### Проблема: срезы входных данных постоянно находятся в состоянии "Waiting"

Срезы могут находиться в состоянии **Waiting** по ряду причин; одна из самых распространенных — для свойства **external** не указано значение **true**. Все наборы данных, созданные вне фабрики данных Azure, должны быть помечены свойством **external**. Это означает, что данные являются внешними и не поддерживаются какими-либо конвейерами в фабрике данных. После того как данные станут доступны в соответствующем хранилище, срезы данных помечаются флагом **Ready** (готово).

См. следующий пример использования свойства **external**. Если нужно, можно указать **externalData*** при задании значения true для external.

Дополнительные сведения об этом свойстве см. в статье [Наборы данных](data-factory-create-datasets.md).
	
	{
	  "name": "CustomerTable",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      }
	    }
	  }
	}

Чтобы устранить эту ошибку, добавьте свойство **external** и дополнительный раздел **externalData** в определение JSON входной таблицы и повторно создайте эту таблицу.

### Проблема: сбой гибридной операции копирования
Действия по устранению неполадок с копированием в локальное хранилище данных и из него с помощью шлюза управления данными см. в статье [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).

### Проблема: сбой подготовки HDInsight по запросу
При использовании связанной службы типа HDInsightOnDemandLinkedService нужно задать имя linkedServiceName, указывающее на хранилище BLOB-объектов Azure. Фабрика данных использует это хранилище для хранения журналов и вспомогательных файлов для кластера HDInsight по запросу. Иногда подготовка кластера HDInsight по запросу завершается следующей ошибкой:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Обычно эта ошибка указывает на то, что расположение учетной записи хранения, указанной в linkedServiceName, не совпадает с расположением центра обработки данных, в котором происходит подготовка HDInsight. Например, если ваша фабрика данных Azure находится на Западе США (West US) и подготовка HDInsight по запросу происходит на Западе США, но для учетной записи хранения больших двоичных объектов Azure задано расположение на Востоке США (East US), подготовка по запросу выполнена не будет.

Есть еще одно свойство JSON, additionalLinkedServiceNames, где можно указать дополнительные учетные записи хранения в HDInsight по запросу. Эти дополнительные связанные учетные записи хранения должны находиться в том же расположении, что и кластер HDInsight, иначе произойдет сбой с той же ошибкой.

### Проблема: сбой настраиваемого действия .NET
Подробные действия см. в статье [Debug a pipeline with custom activity](data-factory-use-custom-activities.md#debug-the-pipeline) (Отладка конвейера с помощью настраиваемого действия).

## Устранение неполадок с помощью портала Azure 

### Использование колонок на портале
Действия см. в статье [Мониторинг конвейера](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline).

### Использование приложения по мониторингу и управлению
Сведения см. в статье [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью приложения по мониторингу и управлению](data-factory-monitor-manage-app.md).

## Использование Azure PowerShell для устранения неполадок

### Использование Azure PowerShell для устранения ошибок  
Сведения см. в статье [Monitor Data Factory pipelines using Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) (Мониторинг конвейеров фабрики данных с помощью Azure PowerShell).


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=AcomDC_0803_2016-->
<properties title="Azure Data Factory - Release Notes" pageTitle="Фабрика данных - заметки о выпуске | Azure" description="Заметки о выпуске фабрика данных" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Заметки о выпуске фабрики данных Azure

## Заметки о выпуске фабрики данных 12.11.2014 г. ##

### Улучшения

- Интеграция машинного обучения Azure
	- Этот выпуск службы фабрики данных Azure дает возможность интегрировать фабрику данных Azure в службу машинного обучения Azure с помощью действий **AzureMLLinkedService** и **AzureMLBatchScoringActivity**. Подробные сведения см. в разделе [Создание прогнозных конвейеров с помощью фабрики данных и машинного обучения Azure][adf-azure-ml]. 
- Предоставляется состояние версии шлюза
	- На портале предварительной версии Azure будет отображаться состояние NewVersionAvailable, а в выходных данных командлета Get-AzureDataFactoryGateway будут сведения о наличии более новой версии шлюза. Вы можете продолжить изучение портала, загрузить новый установочный файл (.msi) и запустить его для установки последнего шлюза. Дополнительные настройки для этого не требуются.

### изменения

- Удален контейнер JobsContainer из службы HdInsightOnDemandLinkedService.
	- В определении JSON для службы HDInsightOnDemandLinkedService больше не нужно задавать свойство **jobsContainer**. Если для связанной службы по запросу уже задано это свойство, оно будет игнорироваться. Вы можете удалить свойство из определения JSON для связанной службы и обновить определение связанной службы с помощью командлета New-AzureDataFactoryLinkedService.
- Дополнительные параметры конфигурации для HDInsightOnDemandLinkedService
	- В этом выпуске мы добавлена поддержка нескольких дополнительных параметров конфигурации для HDInsightOnDemandLinked (кластера HDInsight по запросу). Подробные сведения см. в разделе [Свойства ClusterCreateParameters][on-demand-hdi-parameters].
- Удалено расположение шлюза
	- При создании шлюза фабрики данных Azure через портал или с помощью PowerShell (New-AzureDataFactoryGateway) теперь не требуется указывать расположение шлюза. Будет наследоваться область фабрики данных. Таким же образом, больше не требуется настраивать связанную службу сервера SQL Server с помощью свойства JSON "gatewayLocation". Пакет .NET SDK фабрики данных также обновлен для отражения этих изменениями.
	- При использовании более ранней версии SDK и Azure PowerShell вам по-прежнему придется выполнять настройку расположения.
 
     

#### Критические изменения
	
- Действие CustomActivity переименовано в DotNetActivity
	- Интерфейс **ICustomActivity** переименован в **IDotNetActivity**. Вам потребуется обновить пакеты NuGet для Data Factory и заменить строку ICustomActivity на IDotNetActivity в исходном коде пользовательского действия.  
	- В определении JSON пользовательского действия необходимо сменить тип действий с **CustomActivity** на тип **DotNetActivity**. 
	- Классы **CustomActivity** и **CustomActivityProperties** переименованы в **DotNetActivity** и **DotNetActivityProperties** соответственно с тем же набором свойств.

		В более ранней версии SDK и Azure PowerShell можно по-прежнему использовать CustomActivity вместо DotNetActivity.
    
  		Пошаговое руководство по созданию пользовательского действия и использованию его в конвейере фабрики данных Azure находится в разделе [Использование пользовательских действий в конвейере фабрики данных Azure][adf-custom-activities].  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->

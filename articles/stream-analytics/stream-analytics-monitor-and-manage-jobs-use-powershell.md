<properties 
	pageTitle="Отслеживание заданий Stream Analytics и управление ими с помощью PowerShell | Microsoft Azure" 
	description="Сведения об использовании Azure PowerShell и командлетов для отслеживания заданий Stream Analytics и управления ими." 
	keywords="powershell azure, командлеты powershell azure, команда powershell"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="10/06/2015" 
	ms.author="jeffstok"/>


# Отслеживание заданий Stream Analytics и управление ими с помощью командлетов Azure PowerShell

Узнайте, как отслеживать ресурсы Stream Analytics и управлять ими с помощью командлетов Azure PowerShell, выполняющих базовые задания Stream Analytics.


## Необходимые условия для запуска командлетов Azure PowerShell службы Stream Analytics

 - Создайте группу ресурсов Azure в своей подписке. Ниже приведен пример сценария Azure PowerShell. Дополнительную информацию об Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](../install-configure-powershell.md).  


 		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>
 
		# Create an Azure resource group	
			# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
			#Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

		# Create an Azure resource group
		New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
		


> [AZURE.NOTE]Отслеживание заданий Stream Analytics, созданных программным путем, по умолчанию отключено. Вы можете вручную включить отслеживание на портале Azure. Для этого перейдите на страницу "Отслеживание" задания и нажмите кнопку "Включить". Это также можно сделать программным путем, выполнив действия, приведенные в [статье об отслеживании заданий Stream Analytics в службе Azure Stream Analytics программным путем](stream-analytics-monitor-jobs.md).

## Командлеты Azure PowerShell для службы Stream Analytics
Следующие командлеты Azure PowerShell можно использовать для отслеживания заданий Azure Stream Analytics и управления ими.

### Get-AzureStreamAnalyticsJob
Выводит список всех заданий Stream Analytics, определенных в подписке Azure или указанной группе ресурсов, или показывает сведения о конкретном задании в группе ресурсов.

**Пример 1**

	Get-AzureStreamAnalyticsJob

Эта команда PowerShell возвращает сведения обо всех заданиях Stream Analytics в подписке Azure.

**Пример 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
Эта команда PowerShell возвращает сведения о всех заданиях Stream Analytics в группе ресурсов StreamAnalytics-Default-Central-US.

**Пример 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
Эта команда PowerShell возвращает сведения о задании Stream Analytics StreamingJob в группе ресурсов StreamAnalytics-Default-Central-US.

### Get-AzureStreamAnalyticsInput
Выводит список всех входных данных, определенных в указанном задании Stream Analytics, или показывает сведения о конкретных данных.

**Пример 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Эта команда PowerShell возвращает сведения о всех входных данных, определенных в задании StreamingJob.

**Пример 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
Эта команда PowerShell возвращает сведения о входных данных EntryStream, определенных в задании StreamingJob.

### Get-AzureStreamAnalyticsOutput
Выводит список всех выходных данных, определенных в указанном задании Stream Analytics, или показывает сведения о конкретных данных.

**Пример 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
Эта команда PowerShell возвращает сведения о выходных данных, определенных в задании StreamingJob.

**Пример 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
Эта команда PowerShell возвращает сведения о выходных данных Output, определенных в задании StreamingJob.

### Get-AzureStreamAnalyticsQuota
Возвращает сведения о квоте единиц потоковой передачи в указанном регионе.

**Пример 1**

	Get-AzureStreamAnalyticsQuota –Location "Central US" 
Эта команда PowerShell возвращает сведения о квоте и использовании единиц потоковой передачи в центральном регионе США.

### Get-AzureStreamAnalyticsTransformation
Возвращает сведения о конкретном преобразовании, определенном в задании Stream Analytics.

**Пример 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
Эта команда PowerShell возвращает сведения о преобразовании StreamingJob в задании StreamingJob.

### New-AzureStreamAnalyticsInput
Создает новые или обновляет существующие входные данные в задании Stream Analytics.
  
Имя входных данных можно указать в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указаны существующие входные данные и не задан параметр –Force, командлет предложит заменить существующие входные данные.

Если указать параметр –Force и существующее имя входных данных, входные данные будут заменены без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе [Создание входных данных (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] [справочника по API-интерфейсу REST управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
Эта команда PowerShell создает новые входные данные из файла Input.json. Если существующие входные данные с именем, указанным во входном файле определения, уже определены, командлет предложит их заменить.

**Пример 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
Эта команда PowerShell создает новые входные данные в задании EntryStream. Если существующие входные данные с таким именем уже определены, командлет предложит их заменить.

**Пример 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
Эта команда PowerShell заменяет определение существующего источника входных данных EntryStream на определение из файла.

### New-AzureStreamAnalyticsJob
Создает задание Stream Analytics в Microsoft Azure или обновляет определение существующего задания.

Имя задания можно указать в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указано существующее имя задания и не указан параметр –Force, командлет предложит заменить существующее задание.

Если указать параметр –Force и существующее имя задания, определение задания будет заменено без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе [Создание задания Stream Analytics][msdn-rest-api-create-stream-analytics-job] [справочника по API-интерфейсу REST управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
Эта команда PowerShell создает новое задание из определения в JobDefinition.json. Если существующее задание с именем, указанным в файле определения задания, уже определено, командлет предложит его заменить.

**Пример 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
Эта команда PowerShell заменяет определение задания StreamingJob.

### New-AzureStreamAnalyticsOutput
Создает новые или обновляет существующие выходные данные в задании Stream Analytics.

Имя выходных данных можно указать в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указаны существующие выходные данные и не задан параметр –Force, командлет предложит заменить существующие выходные данные.

Если указать параметр –Force и существующее имя выходных данных, выходные данные будут заменены без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе [Создание выходных данных][msdn-rest-api-create-stream-analytics-output] [справочника по API-интерфейсу REST управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
Эта команда PowerShell создает новые выходные данные output в задании StreamingJob. Если существующие выходные данные с таким именем уже определены, командлет предложит их заменить.

**Пример 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
Эта команда PowerShell заменяет определение для output в задании StreamingJob.

### New-AzureStreamAnalyticsTransformation
Создает новое или обновляет существующее преобразование в задании Stream Analytics.
  
Имя преобразования можно указано в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указано существующее преобразование и не задан параметр –Force, командлет предложит заменить существующее преобразование.

Если указать параметр –Force и существующее имя преобразования, преобразование будет заменено без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе [Создание преобразования (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] [справочника по API-интерфейсу REST управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
Эта команда PowerShell создает новое преобразование StreamingJobTransform в задании StreamingJob. Если существующее преобразование с таким именем уже определено, командлет предложит его заменить.

**Пример 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
 Эта команда PowerShell заменяет определение StreamingJobTransform в задании StreamingJob.

### Remove-AzureStreamAnalyticsInput
Асинхронно удаляет указанные входные данные из задания Stream Analytics в Microsoft Azure. Если указать параметр –Force, входные данные будут удалены без подтверждения.

**Пример 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
Эта команда PowerShell удаляет входные данные EventStream в задании StreamingJob.

### Remove-AzureStreamAnalyticsJob
Асинхронно удаляет указанное задание Stream Analytics в Microsoft Azure. Если указать параметр –Force, задание будет удалено без подтверждения.

**Пример 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Эта команда PowerShell удаляет задание StreamingJob.

### Remove-AzureStreamAnalyticsOutput
Асинхронно удаляет указанные выходные данные из задания Stream Analytics в Microsoft Azure. Если указать параметр –Force, выходные данные будут удалены без подтверждения.

**Пример 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Эта команда PowerShell удаляет выходные данные Output в задании StreamingJob.

### Start-AzureStreamAnalyticsJob
Асинхронно развертывает и запускает задание Stream Analytics в Microsoft Azure.

**Пример 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Это команда PowerShell запускает задание StreamingJob с пользовательским временем запуска выходных данных «12 декабря 2012 г., 12:12:12 UTC».


### Stop-AzureStreamAnalyticsJob
Асинхронно останавливает задание Stream Analytics в Microsoft Azure и освобождает используемые ресурсы. Определение задания и метаданные остаются доступны в подписке через портал Azure и интерфейсы API управления, поэтому задание всегда можно изменить и перезапустить. Вы не платите за задание в состоянии "Остановлено".

**Пример 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Эта команда PowerShell останавливает задание StreamingJob.

### Test-AzureStreamAnalyticsInput
Проверяет возможность подключения Stream Analytics к указанным входным данным.

**Пример 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
Эта команда PowerShell проверяет состояние подключения входных данных EntryStream в StreamingJob.

###Test-AzureStreamAnalyticsOutput
Проверяет возможность подключения Stream Analytics к указанным выходным данным.

**Пример 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Эта команда PowerShell проверяет состояние подключения выходных данных Output в StreamingJob.

## Получение поддержки
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).


## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=Oct15_HO4-->
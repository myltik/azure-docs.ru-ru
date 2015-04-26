<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="Отслеживание и управление заданиями Stream Analytics с помощью PowerShell | Azure" description="Узнайте, как использовать командлеты Azure PowerShell для мониторинга и управления заданиями потоковая аналитика" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Отслеживание и управление заданиями Stream Analytics с помощью Azure PowerShell

Узнайте, как управлять ресурсами Azure Stream Analytics с помощью Azure PowerShell.

##Содержание

- [Необходимые условия](#prerequisites)
- [Командлеты PowerShell сервиса Stream Analytics](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [См. также:](#seealso)

## <a name="prerequisites"></a>Необходимые условия для запуска командлетов PowerShell сервиса Stream Analytics

1.	Установка и настройка Azure Powershell

	Следуйте инструкциям по [установке и настройке Azure PowerShell][powershell-install].

2.	Настройка режима Azure

	После установки Azure PowerShell запустите командлет [Switch-AzureMode][msdn-switch-azuremode], чтобы установить необходимый режим Azure для доступа к командлетам Stream Analytics:

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Для заданий Stream Analytics, созданных с помощью Azure PowerShell, временно отключено отслеживание.  Чтобы снять это ограничение, на портале Azure перейдите на страницу "Отслеживание" конкретного задания и нажмите кнопку "Включить".  

##<a name="cmdlets"></a>Командлеты PowerShell сервиса Stream Analytics
В следующей таблице приведен список командлетов, которые можно использовать для отслеживания и управления заданиями Azure Stream Analytics с помощью Azure PowerShell.

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Выводит список всех заданий Stream Analytics, определенных в подписке Azure или указанной группе ресурсов, или показывает сведения о конкретном задании в группе ресурсов.

**Пример 1.**

	Get-AzureStreamAnalyticsJob

Эта команда возвращает сведения обо всех заданиях Stream Analytics в подписке Azure.

**Пример 2.**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
Эта команда возвращает сведения обо всех заданиях Stream Analytics в группе ресурсов StreamAnalytics-Default-West-US.

**Пример 3.**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
Эта команда возвращает сведения о задании Stream Analytics StreamingJob в группе ресурсов StreamAnalytics-Default-West-US.

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
Выводит список всех входных данных, определенных в указанном задании Stream Analytics, или показывает сведения о конкретных данных.

**Пример 1.**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

Эта команда возвращает сведения обо всех входных данных, определенных в задании StreamingJob.

**Пример 2.**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Эта команда возвращает сведения о входных данных EntryStream, определенных в задании StreamingJob.

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
Выводит список всех выходных данных, определенных в указанном задании Stream Analytics, или показывает сведения о конкретных данных.

**Пример 1.**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
Эта команда возвращает сведения о выходных данных, определенных в задании StreamingJob.

**Пример 2.**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Эта команда возвращает сведения о выходных данных Output, определенных в задании StreamingJob.

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
Возвращает сведения о квоте Streaming Unit указанного региона.

**Пример 1.**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
Эта команда возвращает сведения о квоте и использовании Streaming Unit в регионе "Запад США".

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
Возвращает сведения о конкретном преобразовании, определенном в задании Stream Analytics.

**Пример 1.**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
Эта команда возвращает сведения о преобразовании StreamingJob в задании StreamingJob.

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
Создает новые входные данные в задании Stream Analytics или обновляет существующие.
  
Имя входных данных можно указать в файле JSON или в командной строке.  Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указаны существующие входные данные и не указан параметр -Force, командлет предложит заменить существующие данные.

При указании параметра -Force и существующего имени входных данных входные данные будут заменены без подтверждения.

**Пример 1.**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
Эта команда создает новые входные данные из файла Input.json.  Если существующие входные данные с именем, указанным во входном файле определения, уже определены, командлет предложит их заменить.

**Пример 2.**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
Эта команда создает новые входные данные в задании EntryStream.  Если существующие входные данные с таким именем уже определены, командлет предложит их заменить.

**Пример 3.**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
Эта команда заменяет определение существующего источника входных данных EntryStream определением из файла.

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Создает новое задание Stream Analytics в Microsoft Azure или обновляет определение существующего задания.

Имя задания можно указать в файле JSON или в командной строке.  Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указано существующее имя задания и не указан параметр -Force, командлет предложит заменить существующее задание.

При указании параметра -Force и существующего имени задания определение задания будет заменено без подтверждения.

**Пример 1.**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
Эта команда создает новое задание из определения в JobDefinition.json.  Если существующее задание с именем, указанным в файле определения задания, уже определено, командлет предложит его заменить.

**Пример 2.**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
Эта команда заменяет определение задания StreamingJob.

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
Создает новые выходные данные в задании Stream Analytics или обновляет существующие.  

Имя выходных данных можно указать в файле JSON или в командной строке.  Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указаны существующие выходные данные и не указан параметр -Force, командлет предложит заменить существующие данные.

При указании параметра -Force и существующего имени выходных данных выходные данные будут заменены без подтверждения.

**Пример 1.**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
Эта команда создает новые выходные данные output в задании StreamingJob.  Если существующие выходные данные с таким именем уже определены, командлет предложит их заменить.

**Пример 2.**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
Эта команда заменяет определение output в задании StreamingJob.

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
Создает новое преобразование в задании Stream Analytics или обновляет существующее.
  
Имя преобразования можно указано в файле JSON или в командной строке.  Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указано существующее преобразование и не указан параметр -Force, командлет предложит заменить существующее преобразование.

При указании параметра -Force и существующего имени преобразования преобразование будет заменено без подтверждения.

**Пример 1.**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
Эта команда создает новое преобразование StreamingJobTransform в задании StreamingJob.  Если существующее преобразование с таким именем уже определено, командлет предложит его заменить.

**Пример 2.**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 Эта команда заменяет определение StreamingJobTransform в задании StreamingJob.

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Асинхронно удаляет указанные входные данные из задания Stream Analytics в Microsoft Azure.  
При указании параметра -Force входные данные будут удалены без подтверждения.

**Пример 1.**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
Эта команда удаляет входные данные EventStream в задании StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Асинхронно удаляет указанное задание Stream Analytics в Microsoft Azure.  
При указании параметра -Force задание будет удалено без подтверждения.

**Пример 1.**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Эта команда удаляет задание StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Асинхронно удаляет указанные выходные данные из задания Stream Analytics в Microsoft Azure.  
При указании параметра -Force выходные данные будут удалены без подтверждения.

**Пример 1.**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Эта команда удаляет выходные данные Output в задании StreamingJob.  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
Асинхронно развертывает и запускает задание Stream Analytics в Microsoft Azure.

**Пример 1.**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Эта команда запускает задание StreamingJob.  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Асинхронно останавливает задание Stream Analytics в Microsoft Azure и освобождает используемые ресурсы. Определение задания и метаданные остаются доступны в подписке через портал Azure и через API управления Так что задание всегда можно изменить и перезапустить. Вы не платите за задание в состоянии "Остановлено".

**Пример 1.**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Эта команда останавливает задание StreamingJob.  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
Проверяет возможность подключения Stream Analytics к указанным входным данным.

**Пример 1.**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Проверяет состояние подключения входных данных EntryStream в StreamingJob.  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
Проверяет возможность подключения Stream Analytics к указанным выходным данным.

**Пример 1.**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Проверяет состояние подключения выходных данных Output в StreamingJob.  


##<a name="seealso"></a>См. также

- [Введение в Azure Stream Analytics][stream.analytics.introduction]
- [Начало работы с Stream Analytics][stream.analytics.get.started]
- [Ограничения в предварительном выпуске Stream Analytics][stream.analytics.limitations]
- [Руководство по Stream Analytics для разработчика][stream.analytics.developer.guide]
- [Справочник по языку запросов Stream Analytics][stream.analytics.query.language.reference]
- [Справочник по REST API для Stream Analytics][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/ru-ru/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->

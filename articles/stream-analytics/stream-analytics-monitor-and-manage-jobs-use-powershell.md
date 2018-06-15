---
title: Мониторинг заданий Azure Stream Analytics и управление ими с помощью PowerShell
description: В этом статье объясняется, как с помощью Azure PowerShell и командлетов отслеживать задания Azure Stream Analytics и управлять ими.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 61c26c55725c19f526680d70f3621d41e9590965
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600915"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Отслеживание заданий Stream Analytics и управление ими с помощью командлетов Azure PowerShell
Узнайте, как отслеживать ресурсы Stream Analytics и управлять ими с помощью командлетов Azure PowerShell и сценариев PowerShell, выполняющих базовые задания Stream Analytics.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Необходимые условия для запуска командлетов Azure PowerShell службы Stream Analytics
* Создайте группу ресурсов Azure в своей подписке. Ниже приведен пример сценария Azure PowerShell. Дополнительную информацию об Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](/powershell/azure/overview).  

Azure PowerShell 0.9.8:  

         # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0.  

         # Log in to your Azure account
        Connect-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>



> [!NOTE]
> Отслеживание заданий Stream Analytics, созданных программным путем, по умолчанию отключено.  Вы можете вручную включить отслеживание на портале Azure. Для этого перейдите на страницу "Отслеживание" задания и нажмите кнопку "Включить". Это также можно сделать программным путем, выполнив действия, приведенные в статье [Azure Stream Analytics. Отслеживание заданий Stream Analytics программным путем](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Командлеты Azure PowerShell для службы Stream Analytics
Следующие командлеты Azure PowerShell можно использовать для отслеживания заданий Azure Stream Analytics и управления ими. Обратите внимание, что Azure PowerShell имеет различные версии. 
**В приведенных примерах первая команда приведена для Azure PowerShell 0.9.8, вторая — для Azure PowerShell 1.0.** В названиях команд Azure PowerShell 1.0 всегда содержится "AzureRM".

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Выводит список всех заданий Stream Analytics, определенных в подписке Azure или указанной группе ресурсов, или показывает сведения о конкретном задании в группе ресурсов.

**Пример 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsJob

Эта команда PowerShell возвращает сведения обо всех заданиях Stream Analytics в подписке Azure.

**Пример 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Эта команда PowerShell возвращает сведения о всех заданиях Stream Analytics в группе ресурсов StreamAnalytics-Default-Central-US.

**Пример 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Эта команда PowerShell возвращает сведения о задании Stream Analytics StreamingJob в группе ресурсов StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Выводит список всех входных данных, определенных в указанном задании Stream Analytics, или показывает сведения о конкретных данных.

**Пример 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Эта команда PowerShell возвращает сведения о всех входных данных, определенных в задании StreamingJob.

**Пример 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Эта команда PowerShell возвращает сведения о входных данных EntryStream, определенных в задании StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Выводит список всех выходных данных, определенных в указанном задании Stream Analytics, или показывает сведения о конкретных данных.

**Пример 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Эта команда PowerShell возвращает сведения о выходных данных, определенных в задании StreamingJob.

**Пример 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Эта команда PowerShell возвращает сведения о выходных данных Output, определенных в задании StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Возвращает сведения о квоте единиц потоковой передачи в указанном регионе.

**Пример 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Эта команда PowerShell возвращает сведения о квоте и использовании единиц потоковой передачи в центральном регионе США.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Возвращает сведения о конкретном преобразовании, определенном в задании Stream Analytics.

**Пример 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0.  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Эта команда PowerShell возвращает сведения о преобразовании StreamingJob в задании StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzureRMStreamAnalyticsInput
Создает новые или обновляет существующие входные данные в задании Stream Analytics.

Имя входных данных можно указать в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указаны существующие входные данные и не задан параметр –Force, командлет предложит заменить существующие входные данные.

Если указать параметр –Force и существующее имя входных данных, входные данные будут заменены без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе о [создании входных данных][msdn-rest-api-create-stream-analytics-input] [справочника по интерфейсу REST API управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Эта команда PowerShell создает новые входные данные из файла Input.json. Если существующие входные данные с именем, указанным во входном файле определения, уже определены, командлет предложит их заменить.

**Пример 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Эта команда PowerShell создает новые входные данные в задании EntryStream. Если существующие входные данные с таким именем уже определены, командлет предложит их заменить.

**Пример 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Эта команда PowerShell заменяет определение существующего источника входных данных EntryStream на определение из файла.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzureRMStreamAnalyticsJob
Создает задание Stream Analytics в Microsoft Azure или обновляет определение существующего задания.

Имя задания можно указать в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указано существующее имя задания и не указан параметр –Force, командлет предложит заменить существующее задание.

Если указать параметр –Force и существующее имя задания, определение задания будет заменено без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе о [создании задания Stream Analytics][msdn-rest-api-create-stream-analytics-job] [справочника по интерфейсу REST API управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Эта команда PowerShell создает новое задание из определения в JobDefinition.json. Если существующее задание с именем, указанным в файле определения задания, уже определено, командлет предложит его заменить.

**Пример 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Эта команда PowerShell заменяет определение задания StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzureRMStreamAnalyticsOutput
Создает новые или обновляет существующие выходные данные в задании Stream Analytics.  

Имя выходных данных можно указать в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указаны существующие выходные данные и не задан параметр –Force, командлет предложит заменить существующие выходные данные.

Если указать параметр –Force и существующее имя выходных данных, выходные данные будут заменены без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе о [создании выходных данных][msdn-rest-api-create-stream-analytics-output] [справочника по интерфейсу REST API управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Эта команда PowerShell создает новые выходные данные output в задании StreamingJob. Если существующие выходные данные с таким именем уже определены, командлет предложит их заменить.

**Пример 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Эта команда PowerShell заменяет определение для output в задании StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzureRMStreamAnalyticsTransformation
Создает новое или обновляет существующее преобразование в задании Stream Analytics.

Имя преобразования можно указано в JSON-файле или в командной строке. Если указаны оба, имя в командной строке должно совпадать с именем в файле.

Если указано существующее преобразование и не задан параметр –Force, командлет предложит заменить существующее преобразование.

Если указать параметр –Force и существующее имя преобразования, преобразование будет заменено без подтверждения.

Подробные сведения о структуре и содержимом JSON-файла см. в разделе о [создании преобразования][msdn-rest-api-create-stream-analytics-transformation] [справочника по интерфейсу REST API управления Stream Analytics][stream.analytics.rest.api.reference].

**Пример 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Эта команда PowerShell создает новое преобразование StreamingJobTransform в задании StreamingJob. Если существующее преобразование с таким именем уже определено, командлет предложит его заменить.

**Пример 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0.  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Эта команда PowerShell заменяет определение StreamingJobTransform в задании StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Асинхронно удаляет указанные входные данные из задания Stream Analytics в Microsoft Azure.  
Если указать параметр –Force, входные данные будут удалены без подтверждения.

**Пример 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0.  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Эта команда PowerShell удаляет входные данные EventStream в задании StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Асинхронно удаляет указанное задание Stream Analytics в Microsoft Azure.  
Если указать параметр –Force, задание будет удалено без подтверждения.

**Пример 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0.  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Эта команда PowerShell удаляет задание StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
Асинхронно удаляет указанные выходные данные из задания Stream Analytics в Microsoft Azure.  
Если указать параметр –Force, выходные данные будут удалены без подтверждения.

**Пример 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0.  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Эта команда PowerShell удаляет выходные данные Output в задании StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Асинхронно развертывает и запускает задание Stream Analytics в Microsoft Azure.

**Пример 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0.  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Это команда PowerShell запускает задание StreamingJob с пользовательским временем запуска выходных данных «12 декабря 2012 г., 12:12:12 UTC».

### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Асинхронно останавливает задание Stream Analytics в Microsoft Azure и освобождает используемые ресурсы. Определение задания и метаданные остаются доступны в подписке через портал Azure и интерфейсы API управления, поэтому задание всегда можно изменить и перезапустить. Вы не платите за задание в состоянии "Остановлено".

**Пример 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0.  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Эта команда PowerShell останавливает задание StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Проверяет возможность подключения Stream Analytics к указанным входным данным.

**Пример 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0.  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Эта команда PowerShell проверяет состояние подключения входных данных EntryStream в StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Проверяет возможность подключения Stream Analytics к указанным выходным данным.

**Пример 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0.  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Эта команда PowerShell проверяет состояние подключения выходных данных Output в StreamingJob.  

## <a name="get-support"></a>Получение поддержки
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


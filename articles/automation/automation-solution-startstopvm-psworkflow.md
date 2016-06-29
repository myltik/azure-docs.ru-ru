<properties 
	pageTitle="Запуск и остановка виртуальных машин с помощью службы автоматизации Azure: рабочий процесс PowerShell | Microsoft Azure"
	description="Версия сценария с графическим интерфейсом для службы автоматизации Azure, включая модули Runbook для запуска и остановки классических виртуальных машин."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="jwhit"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/14/2016"
	ms.author="bwren" />

# Сценарий для службы автоматизации Azure: запуск и остановка виртуальных машин

В этот сценарий для службы автоматизации Azure входят модули Runbook для запуска и остановки классических виртуальных машин. С этим сценарием вы можете:

- использовать модули Runbook без внесения изменений в свою рабочую среду; 
- реализовать дополнительные функциональные возможности посредством изменения модулей Runbook;  
- вызывать модули Runbook из другого модуля Runbook в рамках общего решения; 
- использовать модули Runbook в качестве учебников для ознакомления с основными концепциями создания таких модулей. 

> [AZURE.SELECTOR]
- [Графический](automation-solution-startstopvm-graphical.md)
- [Рабочий процесс PowerShell](automation-solution-startstopvm-psworkflow.md)

В этой статье описывается сценарий применения модулей Runbook для рабочих процессов PowerShell. Также есть статья о [модулях Runbook с графическим интерфейсом](automation-solution-startstopvm-graphical.md).

## Получение сценария

Этот сценарий состоит из двух модулей Runbook для рабочих процессов PowerShell. Эти модули можно скачать по следующим ссылкам. Ссылки на модули Runbook для сценария с графическим интерфейсом приведены в [соответствующей статье](automation-solution-startstopvm-graphical.md).

| Модуль Runbook | Ссылка | Тип | Описание |
|:---|:---|:---|:---|
| Start-AzureVMs | [Запуск классических виртуальных машин Azure](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Для рабочих процессов PowerShell | Запуск всех классических виртуальных машин в подписке Azure или всех виртуальных машин с определенным именем службы. |
| Stop-AzureVMs | [Остановка классических виртуальных машин Azure](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Для рабочих процессов PowerShell | Остановка всех классических виртуальных машин в учетной записи службы автоматизации или всех виртуальных машин с определенным именем службы. |


## Установка и настройка сценария

### 1\. Установка модулей Runbook

Загрузив модули Runbook, импортируйте их, как описано в статье [Импорт модуля Runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### 2\. Просмотр описания и требований
Модули Runbook содержат справочные комментарии с описанием и сведениями о необходимых ресурсах. Аналогичную информацию можно получить из этой статьи.

### 3\. Настройка ресурсов
Для модулей Runbook нужно создать перечисленные ниже ресурсы, после чего добавить в них соответствующие значения.

| Тип ресурса | Имя ресурса | Описание |
|:---|:---|:---|:---|
| Учетные данные | AzureCredential | Содержит учетные данные для учетной записи, имеющей полномочия запускать и останавливать виртуальные машины в подписке Azure. Кроме того, вы можете указать другой ресурс учетных данных. Для этого используйте параметр **Credential** действия **Add-AzureAccount**. |
| Переменная | AzureSubscriptionId | Содержит идентификатор вашей подписки Azure. |

## Использование сценария

### Параметры

Модули Runbooks имеют следующие параметры. Вам необходимо указать значения всех обязательных параметров. Также по желанию можно указать значения дополнительных параметров.

| Параметр | Тип | Обязательно | Описание |
|:---|:---|:---|:---|
| ServiceName | string | Нет | Если значение указано, запускаются или останавливаются все виртуальные машины с таким именем службы. Если значение не указано, запускаются или останавливаются все классические виртуальные машины в подписке Azure. |
| AzureSubscriptionIdAssetName | string | Нет | Содержит имя [ресурса переменной](#installing-and-configuring-the-scenario), в котором указан идентификатор вашей подписки Azure. Если значение не указано, используется значение *AzureSubscriptionId*. |
| AzureCredentialAssetName | string | Нет | Содержит имя [ресурса учетных данных](#installing-and-configuring-the-scenario), в котором указаны учетные данные используемого модуля Runbook. Если не указать это значение, будет использоваться значение *AzureCredential*. |

### Запуск модулей Runbook

Для запуска модуля Runbook в данном сценарии можно использовать любой из методов, описанных в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

Приведенные ниже команды выполняются в Windows PowerShell. Они запускают модуль **StartAzureVMs**, который запускает все виртуальные машины с именем службы *MyVMService*.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### Выходные данные

При выполнении модулей выводятся [сообщения](automation-runbook-output-and-messages.md) для каждой виртуальной машины. Из них можно узнать о результатах инструкций запуска или остановки. Чтобы определить результат выполнения каждого модуля Runbook, найдите в выходных данных соответствующую строку. В следующей таблице перечислены возможные варианты выходных данных.

| Модуль Runbook | Условие | Сообщение |
|:---|:---|:---|
| Start-AzureVMs | Виртуальная машина уже запущена | MyVM is already running |
| Start-AzureVMs | Запрос на запуск виртуальной машины успешно отправлен | MyVM has been started |
| Start-AzureVMs | Не удалось отправить запрос на запуск виртуальной машины | MyVM failed to start |
| Stop-AzureVMs | Виртуальная машина уже запущена | MyVM is already stopped |
| Stop-AzureVMs | Запрос на запуск виртуальной машины успешно отправлен | MyVM has been started |
| Stop-AzureVMs | Не удалось отправить запрос на запуск виртуальной машины | MyVM failed to start |

Например, следующий фрагмент кода в модуле Runbook пытается запустить все виртуальные машины с именем службы *MyServiceName*. Если какой-либо из запросов на запуск не будет выполнен, вы сможете попытаться устранить ошибку.

	$results = Start-AzureVMs -ServiceName "MyServiceName"
	foreach ($result in $results) {
		if ($result -like "* has been started" ) {
			# Action to take in case of success.
		}
		else {
			# Action to take in case of error.
		}
	}


## Подробный разбор

Ниже приведен подробный разбор модулей Runbook из этого сценария. Вы можете использовать эти сведения для настройки модулей Runbook или просто для их изучения с целью создания собственных сценариев автоматизации.

### Параметры

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Рабочий процесс начинается с получения значений для [входных параметров](#using-the-scenario). Если имена ресурсов не указаны, используются имена по умолчанию.

### Выходные данные

    # Returns strings with status messages
    [OutputType([String])]

В этой строке кода объявляется строковый тип выходных данных модуля Runbook. Это действие не обязательно, но рекомендуется не пропускать его на случай, если модуль будет использоваться как [дочерний](automation-child-runbooks.md). В результате родительский модуль Runbook будет знать тип выходных данных дочернего модуля.

### Аутентификация

	# Connect to Azure and select the subscription to work against
	$Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
	$null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
	$SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Следующие строки определяют [учетные данные](automation-configuring.md#configuring-authentication-to-azure-resources) и подписку Azure, которые будут использоваться в остальной части модуля. Сначала мы используем **Get-AutomationPSCredential**, чтобы получить ресурс с учетными данными для запуска и остановки виртуальных машин в подписке Azure. Затем **Add-AzureAccount** использует этот ресурс для добавления учетных данных. Выходные данные передаются в фиктивную переменную, чтобы они не включались в выходные данные модуля Runbook.

Затем с помощью **Get-AutomationVariable** извлекается ресурс переменной с идентификатором подписки, а с помощью **Select-AzureSubscription** выбирается подписка.

### Получение виртуальных машин

	# If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
	{ 
		$VMs = Get-AzureVM -ServiceName $ServiceName
	}
    else 
	{ 
		$VMs = Get-AzureVM
	}

**Get-AzureVM** используется для получения виртуальных машин, с которыми будет работать модуль. Если входной переменной **ServiceName** присвоено значение, будут извлечены только виртуальные машины с указанным именем службы. Если значение **ServiceName** пустое, извлекаются все виртуальные машины.

### Запуск и остановка виртуальных машин, отправка выходных данных

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
		if ($VM.PowerState -eq "Started")
		{
			# The VM is already started, so send notice
			Write-Output ($VM.InstanceName + " is already running")
		}
		else
		{
			# The VM needs to be started
        	$StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

	        if ($StartRtn.OperationStatus -ne 'Succeeded')
	        {
				# The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
	        }
			else
			{
				# The VM started, so send notice
				Write-Output ($VM.InstanceName + " has been started")
			}
		}
    }

На следующем этапе выполняется перебор всех виртуальных машин. Сначала проверяется свойство **PowerState**, чтобы определить состояние виртуальной машины: запущена или остановлена (в зависимости от модуля Runbook). Если машина уже находится в нужном состоянии, в выходные данные отправляется сообщение и выполнение модуля Runbook завершается. В противном случае используется команда **Start-AzureVM** или **Stop-AzureVM** для запуска или остановки виртуальной машины. Результат запроса сохраняется в переменную. Затем в выходные данные отправляется сообщение с указанием результата запроса на запуск или остановку.


## Дальнейшие действия

- [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md) 
- [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0615_2016-->
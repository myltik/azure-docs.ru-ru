<properties 
   pageTitle="Объекты Webhook в службе автоматизации Azure"
   description="Объект Webhook, который позволяет клиенту запустить модуль Runbook в службе автоматизации Azure с помощью HTTP-запроса. В статье рассматривается создание объекта Webhook и вызов такого объекта для запуска модуля Runbook."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/08/2015"
   ms.author="bwren;sngun"/>

# Объекты Webhook в службе автоматизации Azure

*Webhook* позволяет запустить модуль Runbook в службе автоматизации Azure с помощью одного HTTP-запроса. Это позволяет внешним службам, таким как Visual Studio Online, GitHub, или пользовательским приложениям запускать модули Runbook без реализации полного решения с помощью API-интерфейса службы автоматизации Azure.

![Объекты Webhook](media/automation-webhooks/webhooks-overview.png)

Сравнение объектов Webhook с другими методами запуска модуля Runbook см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

## Подробные сведения о Webhook

В следующей таблице описаны свойства, которые необходимо настроить для объекта Webhook.

| Свойство | Описание |
|:---|:---|
|Имя | Для объекта Webhook можно указать любое имя, так как оно не раскрывается клиенту. Имя используется для идентификации модуля Runbook в службе автоматизации Azure. <br> Рекомендуется задать объекту Webhook имя, связанное с клиентом, который будет его использовать. |
|URL-адрес |URL-адрес Webhook — это уникальный адрес, который использует клиент для метода HTTP POST для запуска модуля Runbook, связанного с объектом Webhook. URL-адрес создается автоматически при создании объекта Webhook. Нельзя указать другой URL-адрес. <br> <br> URL-адрес содержит токен безопасности, который позволяет сторонней системе вызвать модуль Runbook без дополнительной проверки подлинности. По этой причине он должен рассматриваться как пароль. По соображениям безопасности просмотреть URL-адрес в предварительной версии портала Azure можно только в момент создания объекта Webhook. URL-адрес следует записать в надежном месте, чтобы использовать его в дальнейшем. |
|Срок действия | Как и сертификат, каждый объект Webhook имеет срок действия, после которого он больше не используется. Срок действия нельзя изменить после создания Webhook, а сам Webhook нельзя включить снова после истечения срока действия. Потребуется создать новый объект Webhook, чтобы заменить текущий объект, и обновить клиент для использования нового объекта. |
| Включено | При создании объекта Webhook он по умолчанию включается. Если его отключить, клиенты не смогут его использовать. Свойство **Включен** можно задать при создании объекта Webhook или в любое время после его создания. |


### Параметры
Объект Webhook может определять значения для параметров модуля Runbook, которые используются при запуске модуля Runbook этим объектом Webhook. Объект Webhook должен содержать значения для всех обязательных параметров модуля Runbook и может также включать необязательные параметры. Несколько объектов Webhook, привязанных к одному модулю Runbook, могут использовать разные значения параметров.

Когда клиент запускает модуль Runbook с помощью объекта Webhook, клиент не может переопределить значения параметров, определяемые Webhook. Чтобы получать данные от клиента, модуль Runbook может принимать один параметр **$WebhookData** типа [object], который содержит данные, включаемые клиентом в POST-запрос.

![Webhookdata](media/automation-webhooks/webhookdata.png)

Объект **$WebhookData** имеет следующие свойства.

| Свойство | Описание |
|:--- |:---|
| WebhookName | Имя объекта Webhook. |
| RequestHeader | Хэш-таблица, содержащая заголовки входящего запроса POST. |
| RequestBody | Текст входящего запроса POST. Это сохранит все форматирование, такое как строка, JSON, XML или данные, закодированные в форме. Модуль Runbook должен быть написан для работы с форматом данных, который ожидается.|


Нет настройки объекта Webhook, которая была бы обязательной для поддержки параметра **$WebhookData**, однако модуль Runbook не обязан принимать его. Если модуль Runbook не определяет параметр, любые сведения в запросе клиента пропускаются.

Если указать значение для параметра $WebhookData в момент создания объекта Webhook, это значение будет переопределено, когда объект Webhook запустит модуль Runbook с данными из клиентского POST-запроса, даже если клиент не включает какие-либо данные в текст запроса. При запуске модуля Runbook, у которого параметр $WebhookData использует метод, отличный от Webhook, можно указать значение для $Webhookdata, которое будет распознаваться модулем Runbook. Это значение должно быть объектом с теми же свойствами, что и $Webhookdata, чтобы модуль Runbook смог с ним работать.

>[AZURE.NOTE]Значения входных параметров вводятся вместе с заданием Runbook. Это означает, что любые входные данные, предоставленные клиентом в запросе Webhook, будут записаны в журнал и станут доступны любому пользователю с доступом к заданию автоматизации. По этой причине необходимо соблюдать осторожность при указании критических данных в вызовах Webhook.

## Безопасность

Безопасность объекта Webhook зависит от безопасности URL-адреса, который содержит токен безопасности, позволяющий вызывать объект Webhook. Служба автоматизации Azure не выполняет проверку подлинности для запроса, если он выполняется с правильным URL-адресом. По этой причине объекты Webhook не следует применять для модулей Runbook, которые выполняют важные функции, без использования дополнительной проверки запроса.

Можно включить логику в модуль Runbook, которая будет определять вызов объектом Webhook путем проверки свойства **WebhookName** для параметра $WebhookData. Runbook может выполнять последующие проверки путем поиска определенной информации в свойствах **RequestHeader** или **RequestBody**.

Еще одна стратегия заключается в том, чтобы модуль Runbook выполнял проверку внешнего условия после получения запроса Webhook. Например, рассмотрим модуль Runbook, вызываемый из GitHub каждый раз, когда на GitHub появляется обновление репозитория. Модуль Runbook может подключаться к GitHub, чтобы проверить доступность обновления.

## Создание объекта Webhook

Для создания нового объекта Webhook, связанного с модулем Runbook в предварительной версии портала Azure, воспользуйтесь следующей процедурой.

1. В области **Модули Runbook** в предварительной версии портала Azure щелкните модуль Runbook, запускаемый объектом Webhook, чтобы просмотреть его подробные сведения. 
3. Щелкните **Webhook** в верхней части колонки, чтобы открыть колонку **Добавить объект Webhook**. <br> ![Кнопка Webhook](media/automation-webhooks/webhooks-button.png)
4. Щелкните **Создать объект Webhook**, чтобы открыть колонку **Создание объекта Webhook**.
5. Укажите **Имя**, **Срок действия** для объекта Webhook и необходимость его включения. Подробные сведения об этих свойствах см. в разделе [Сведения об объекте Webhook](#details-of-a-webhook).
6. Щелкните значок копирования и нажмите Ctrl+C, чтобы скопировать URL-адрес объекта Webhook. Сохраните URL-адрес в безопасном месте. **После создания объекта Webhook URL-адрес нельзя получить повторно.** <br> ![URL-адрес Webhook](media/automation-webhooks/copy-webhook-url.png)
3. Щелкните **Параметры**, чтобы указать значения для параметров модуля Runbook. Если модуль Runbook содержит обязательные параметры, то его нельзя будет создать, не указав значения.
1. Щелкните **Создать**, чтобы создать объект Webhook.


## Использование объекта Webhook

Чтобы использовать объект Webhook после его создания, клиентское приложение должно создать запрос HTTP POST с URL-адресом объекта Webhook. Синтаксис объекта Webhook будет иметь следующий формат.

	http://<Webhook Server>/token?=<Token Value>

Клиент получит один из следующих кодов возврата в ответ на запрос POST.

| Код | Текст | Описание |
|:---|:----|:---|
| 202 | Принято | Запрос был принят, и модуль Runbook успешно поставлен в очередь. |
| 400 | Ошибка запроса | Запрос не был принят по одной из следующих причин. <ul> <li>Срок действия Webhook истек.</li> <li>Webhook отключен.</li> <li>Недопустимый токен в URL-адресе.</li> </ul>|
| 404 | Не найдено | Запрос не был принят по одной из следующих причин. <ul> <li>Объект Webhook не найден.</li> <li>Последовательность Runbook не найдена.</li> <li>Учетная запись не найдена.</li> </ul> |
| 500 | Внутренняя ошибка сервера | URL-адрес допустимый, но произошла ошибка. Отправьте запрос повторно. |

Если предположить, что запрос выполнен успешно, ответ Webhook будет содержать идентификатор задания в формате JSON, как показано далее. Он будет содержать идентификатор одного задания, но формат JSON предоставляет возможность потенциальных будущих улучшений.

	{"JobIds":["<JobId>"]}  

Клиент не может определить момент завершения задания Runbook и состояние его завершения из объекта Webhook. Он может определить эти сведения с помощью идентификатора задания, используя другой метод, например [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) или [API службы автоматизации Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### Пример

В следующем примере модуль Runbook запускается с помощью Webhook из Windows PowerShell. Обратите внимание, что в любом языке, способном выполнить HTTP-запрос, можно использовать объект Webhook; Windows PowerShell используется здесь просто для примера.

Модуль Runbook ожидает список виртуальных машин, отформатированный в JSON, в теле запроса. Также мы включаем в заголовок запроса сведения о том, кто запускает модуль Runbook, а также о дате и времени запуска.

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


На следующем рисунке показаны данные заголовка (с использованием трассировки [Fiddler](http://www.telerik.com/fiddler)) из этого запроса. Они включают стандартные заголовки HTTP-запроса в дополнение к пользовательским заголовкам Date и From, которые мы добавили. Каждое из этих значений доступно модулю Runbook в свойстве **RequestHeaders** объекта **WebhookData**.

![Кнопка Webhook](media/automation-webhooks/webhook-request-headers.png)

На следующем рисунке показано тело запроса (с использованием трассировки [Fiddler](http://www.telerik.com/fiddler)), доступное модулю Runbook в свойстве **RequestBody** объекта **WebhookData**. Оно отформатировано как JSON, так как это формат, который был включен в тело запроса.

![Кнопка Webhook](media/automation-webhooks/webhook-request-body.png)

На следующем рисунке показан запрос, отправляемый из Windows PowerShell, а также получаемый в результате ответ. Идентификатор задания извлекается из ответа и преобразуется в строку.

![Кнопка Webhook](media/automation-webhooks/webhook-request-response.png)

Следующий образец модуля Runbook принимает предыдущий пример запроса и запускает виртуальные машины, указанные в теле запроса.

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = ConvertFrom-Json -InputObject $WebhookBody
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				$VMName = $VM.Name
				Write-Output "Starting $VMName"
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}


## Запуск модулей Runbook в ответ на оповещения Azure

Модули Runbook с поддержкой веб-перехватчика можно использовать для реагирования на [оповещения Azure](Azure-portal/insights-receive-alert-notifications.md). Ресурсы в Azure можно отслеживать, собирая статистику производительности, доступности и использования с помощью оповещений Azure. Вы можете получать оповещения на основе отслеживания метрик или событий в ресурсах Azure. Когда значение заданной метрики превышает установленный порог, запускается заданное значение, а затем администратору или соадминистраторам службы отправляется уведомление о разрешении оповещения. Дополнительные сведения о метриках и событиях см. в статье [Оповещения Azure](Azure-portal/insights-receive-alert-notifications.md).

Оповещения Azure можно использовать не только как систему уведомлений, но и для запуска модулей Runbook. Служба автоматизации Azure позволяет запускать модули Runbook с поддержкой веб-перехватчика в ответ на оповещения Azure. Как только метрика превышает заданное пороговое значение, правило оповещения становится активным и запускает веб-перехватчик службы автоматизации, который, в свою очередь, запускает модуль Runbook.

![Объекты Webhook](media/automation-webhooks/webhook-alert.jpg)

### Контекст предупреждения

Для ресурса Azure, такого как виртуальная машина, одной из основных метрик производительности является загрузка ЦП. Если загрузка ЦП составляет 100 % или превышает определенный порог в течение долгого времени, перезапустите виртуальную машину, чтобы решить проблему. Ситуацию можно исправить, настроив правило оповещения для виртуальной машины, используя в качестве метрики процент загрузки ЦП. Процент загрузки ЦП в данном случае взят для примера, и в ресурсах Azure можно настраивать другие метрики, а перезапуск виртуальной машины — лишь одно из действий по решению проблемы, и можно настраивать модуль Runbook на выполнение других действий.

Когда правило оповещения становится активным и запускает модуль Runbook с поддержкой веб-перехватчика, в модуль Runbook отправляется контекст оповещения. [Контекст оповещения](Azure-portal/insights-receive-alert-notifications.md) содержит сведения, позволяющие модулю Runbook идентифицировать ресурс, к которому будет применено действие, включая **SubscriptionID** (идентификатор подписки), **ResourceGroupName** (имя группы ресурсов), **ResourceName** (имя ресурса), **ResourceType** (тип ресурса), **ResourceId** (идентификатор ресурса) и **Timestamp** (метка времени). Контекст оповещения внедряется в основную часть объекта **WebhookData**, передаваемого в модуль Runbook, и извлекается с помощью свойства **Webhook.RequestBody**.


### Пример

Создание виртуальной машины Azure в подписке и привязка [оповещения для мониторинга метрики процента загрузки ЦП](Azure-portal/insights-receive-alert-notifications.md). При создании оповещения введите в поле веб-перехватчика URL-адрес, сгенерированный при создании веб-перехватчика.

Приведенный ниже пример модуля Runbook запускается, когда правило оповещения становится активным и собирает параметры контекста Azure, необходимые модулю Runbook для идентификации ресурса, к которому нужно применить действие.

	workflow Invoke-RunbookUsingAlerts
	{
	    param (  	
	        [object]$WebhookData 
	    ) 

	    # If runbook was called from Webhook, WebhookData will not be null.
	    if ($WebhookData -ne $null) {   
	        # Collect properties of WebhookData. 
	        $WebhookName    =   $WebhookData.WebhookName 
	        $WebhookBody    =   $WebhookData.RequestBody 
	        $WebhookHeaders =   $WebhookData.RequestHeader 

	        # Outputs information on the webhook name that called This 
	        Write-Output "This runbook was started from webhook $WebhookName." 

	        
			# Obtain the WebhookBody containing the AlertContext 
			$WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody) 
	        Write-Output "`nWEBHOOK BODY" 
	        Write-Output "=============" 
	        Write-Output $WebhookBody 

	        # Obtain the AlertContext     
	        $AlertContext = [object]$WebhookBody.context

	        # Some selected AlertContext information 
	        Write-Output "`nALERT CONTEXT DATA" 
	        Write-Output "===================" 
	        Write-Output $AlertContext.name 
	        Write-Output $AlertContext.subscriptionId 
	        Write-Output $AlertContext.resourceGroupName 
	        Write-Output $AlertContext.resourceName 
	        Write-Output $AlertContext.resourceType 
	        Write-Output $AlertContext.resourceId 
	        Write-Output $AlertContext.timestamp 

	    	# Act on the AlertContext data, in our case restarting the VM. 
	    	# Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine. 
	        $cred = Get-AutomationPSCredential -Name "MyAzureCredential" 
	        Add-AzureAccount -Credential $cred 
	        Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN" 
	      
	        #Check the status property of the VM
	        Write-Output "Status of VM before taking action"
	        Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
	        Write-Output "Restarting VM"

	        # Restart the VM by passing VM name and Service name which are same in this case
	        Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName 
	        Write-Output "Status of VM after alert is active and takes action"
	        Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
	    } 
	    else  
	    { 
	        Write-Error "This runbook is meant to only be started from a webhook."  
	    }  
	}

 

## Связанные статьи

- [Запуск модуля Runbook](automation-starting-a-runbook.md)
- [Просмотр состояния задания Runbook](automation-viewing-the-status-of-a-runbook-job.md)
- [Использование службы автоматизации Azure для реагирования на оповещения Azure](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/)

<!---HONumber=Oct15_HO3-->
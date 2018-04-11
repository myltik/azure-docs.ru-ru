---
title: Запуск Runbook службы автоматизации Azure с помощью объекта webhook
description: Объект Webhook, который позволяет клиенту запустить модуль Runbook в службе автоматизации Azure с помощью HTTP-запроса.  В статье рассматривается создание объекта Webhook и вызов такого объекта для запуска модуля Runbook.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 4ea7366a02dd95fac5c1a7307e6156a0481fa16d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Запуск Runbook службы автоматизации Azure с помощью объекта webhook
*Webhook* позволяет запустить модуль Runbook в службе автоматизации Azure с помощью одного HTTP-запроса. Это позволяет внешним службам, таким как Visual Studio Team Services, GitHub, Azure Log Analytics или пользовательским приложениям, запускать модули runbook без реализации полного решения с помощью API службы автоматизации Azure.  
![Обзор веб-перехватчиков](media/automation-webhooks/webhook-overview-image.png)

Сравнение объектов webhook с другими методами запуска модуля Runbook см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

## <a name="details-of-a-webhook"></a>Подробные сведения о Webhook
В следующей таблице описаны свойства, которые необходимо настроить для объекта Webhook.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| ИМЯ |Для объекта Webhook можно указать любое имя, так как оно не раскрывается клиенту.  Имя используется для идентификации модуля Runbook в службе автоматизации Azure. <br>  Рекомендуется задать объекту Webhook имя, связанное с клиентом, который будет его использовать. |
| URL-адрес |URL-адрес Webhook — это уникальный адрес, который использует клиент для метода HTTP POST для запуска модуля Runbook, связанного с объектом Webhook.  URL-адрес создается автоматически при создании объекта Webhook.  Нельзя указать другой URL-адрес. <br> <br>  URL-адрес содержит токен безопасности, который позволяет сторонней системе вызвать модуль Runbook без дополнительной проверки подлинности. По этой причине он должен рассматриваться как пароль.  По соображениям безопасности просмотреть URL-адрес на портале Azure можно только в момент создания веб-перехватчика. URL-адрес следует записать в надежном месте, чтобы использовать его в дальнейшем. |
| Срок действия |Как и сертификат, каждый объект Webhook имеет срок действия, после которого он больше не используется.  Этот срок можно изменить после создания объекта webhook. |
| Включено |При создании объекта Webhook он по умолчанию включается.  Если его отключить, клиенты не смогут его использовать.  Свойство **Включен** можно задать при создании объекта Webhook или в любое время после его создания. |

### <a name="parameters"></a>Параметры
Объект Webhook может определять значения для параметров модуля Runbook, которые используются при запуске модуля Runbook этим объектом Webhook. Объект Webhook должен содержать значения для всех обязательных параметров модуля Runbook и может также включать необязательные параметры. Значение параметра, который настроен для веб-перехватчика, можно изменить даже после создания веб-перехватчика. Несколько объектов Webhook, привязанных к одному модулю Runbook, могут использовать разные значения параметров.

Когда клиент запускает модуль Runbook с помощью объекта Webhook, клиент не может переопределить значения параметров, определяемые Webhook.  Чтобы получать данные от клиента, модуль Runbook может принимать один параметр **$WebhookData** типа [object], который содержит данные, включаемые клиентом в POST-запрос.

![Свойства Webhookdata](media/automation-webhooks/webhook-data-properties.png)

Объект **$WebhookData** имеет следующие свойства.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| WebhookName |Имя объекта Webhook. |
| RequestHeader |Хэш-таблица, содержащая заголовки входящего запроса POST. |
| RequestBody |Текст входящего запроса POST.  Это сохранит все форматирование, такое как строка, JSON, XML или данные, закодированные в форме. Модуль Runbook должен быть написан для работы с форматом данных, который ожидается. |

Нет настройки объекта Webhook, которая была бы обязательной для поддержки параметра **$WebhookData** , однако модуль Runbook не обязан принимать его.  Если модуль Runbook не определяет параметр, любые сведения в запросе клиента пропускаются.

Если указать значение для параметра $WebhookData в момент создания веб-перехватчика, это значение будет переопределено, когда веб-перехватчик запустит модуль runbook с данными из клиентского POST-запроса, даже если клиент не включит данные в текст запроса.  При запуске модуля Runbook, у которого параметр $WebhookData использует метод, отличный от Webhook, можно указать значение для $Webhookdata, которое будет распознаваться модулем Runbook.  Это значение должно быть объектом с теми же [свойствами](#details-of-a-webhook) , что и $Webhookdata, чтобы модуль Runbook смог с ним правильно работать как с фактическим WebhookData, переданным веб-перехватчиком.

Например, если на портале Azure запускается следующий модуль Runbook и требуется передать несколько примеров WebhookData для тестирования, то, так как WebhookData является объектом, он должен быть передан в пользовательском интерфейсе как JSON.

![Параметр WebhookData из пользовательского интерфейса](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Для приведенного выше модуля Runbook, если у вас есть следующие свойства для параметра WebhookData:

1. WebhookName: *MyWebhook*
2. RequestHeader: *From=Test User*
3. RequestBody: *[“VM1”, “VM2”]*

То в пользовательском интерфейсе для параметра WebhookData следует передать следующее значение JSON:  

* {"WebhookName":"MyWebhook", "RequestHeader":{"From":"Test User"}, "RequestBody":"[\"VM1\",\"VM2\"]"}

![Запуск параметра WebhookData из пользовательского интерфейса](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Значения входных параметров вводятся вместе с заданием Runbook.  Это означает, что любые входные данные, предоставленные клиентом в запросе Webhook, будут записаны в журнал и станут доступны любому пользователю с доступом к заданию автоматизации.  По этой причине необходимо соблюдать осторожность при указании критических данных в вызовах Webhook.
>

## <a name="security"></a>Безопасность
Безопасность объекта Webhook зависит от безопасности URL-адреса, который содержит токен безопасности, позволяющий вызывать объект Webhook. Служба автоматизации Azure не выполняет проверку подлинности для запроса, если он выполняется с правильным URL-адресом. По этой причине объекты Webhook не следует применять для модулей Runbook, которые выполняют важные функции, без использования дополнительной проверки запроса.

Можно включить логику в модуль Runbook, которая будет определять вызов объектом Webhook путем проверки свойства **WebhookName** для параметра $WebhookData. Модуль Runbook может выполнять последующие проверки путем поиска определенной информации в свойствах **RequestHeader** или **RequestBody**.

Еще одна стратегия заключается в том, чтобы модуль Runbook выполнял проверку внешнего условия после получения запроса Webhook.  Например, рассмотрим модуль Runbook, вызываемый из GitHub каждый раз, когда на GitHub появляется обновление репозитория.  Модуль Runbook может подключаться к GitHub, чтобы проверить доступность обновления.

## <a name="creating-a-webhook"></a>Создание объекта Webhook
Чтобы создать новый веб-перехватчик, связанный с модулем Runbook, на портале Azure, воспользуйтесь следующей процедурой.

1. На странице **Модули Runbook** на портале Azure щелкните модуль runbook, запускаемый веб-перехватчиком, чтобы открыть страницу с подробной информацией о нем.
2. Щелкните **Веб-перехватчик** в верхней части страницы, чтобы открыть страницу **Добавить веб-перехватчик**. <br>
   ![Кнопка Webhook](media/automation-webhooks/webhooks-button.png)
3. Щелкните **Создать новый веб-перехватчик**, чтобы открыть страницу **Создать веб-перехватчик**.
4. Укажите для объекта webhook **имя**, **Срок действия** и необходимость его включения. Подробные сведения об этих свойствах см. в разделе [Details of a webhook](#details-of-a-webhook) (Сведения об объекте webhook).
5. Щелкните значок копирования и нажмите Ctrl+C, чтобы скопировать URL-адрес объекта Webhook.  Сохраните URL-адрес в безопасном месте.  **После создания объекта webhook URL-адрес нельзя получить повторно.** <br>
   ![URL-адрес Webhook](media/automation-webhooks/copy-webhook-url.png)
6. Щелкните **Параметры** , чтобы указать значения для параметров модуля Runbook.  Если модуль Runbook содержит обязательные параметры, то его нельзя будет создать, не указав значения.
7. Щелкните **Создать** , чтобы создать объект Webhook.

## <a name="using-a-webhook"></a>Использование объекта Webhook
Чтобы использовать объект Webhook после его создания, клиентское приложение должно создать запрос HTTP POST с URL-адресом объекта Webhook.  Синтаксис объекта Webhook будет иметь следующий формат.

    http://<Webhook Server>/token?=<Token Value>

Клиент получит один из следующих кодов возврата в ответ на запрос POST.  

| Код | текст | ОПИСАНИЕ |
|:--- |:--- |:--- |
| 202 |Принято |Запрос был принят, и модуль Runbook успешно поставлен в очередь. |
| 400 |Ошибка запроса |Запрос не был принят по одной из следующих причин. <ul> <li>Срок действия объекта webhook истек.</li> <li>Объект webhook отключен.</li> <li>Недопустимый токен в URL-адресе.</li>  </ul> |
| 404 |Не найдено |Запрос не был принят по одной из следующих причин. <ul> <li>Объект webhook не найден.</li> <li>Модуль Runbook не найден.</li> <li>Учетная запись не найдена.</li>  </ul> |
| 500 |Внутренняя ошибка сервера |URL-адрес допустимый, но произошла ошибка.  Отправьте запрос повторно. |

Если предположить, что запрос выполнен успешно, ответ Webhook будет содержать идентификатор задания в формате JSON, как показано далее. Он будет содержать идентификатор одного задания, но формат JSON предоставляет возможность потенциальных будущих улучшений.

    {"JobIds":["<JobId>"]}  

Клиент не может определить момент завершения задания Runbook и состояние его завершения из объекта Webhook.  Он может определить эти сведения с помощью идентификатора задания, используя другой метод, например [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) или [API службы автоматизации Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Пример
В следующем примере модуль Runbook запускается с помощью Webhook из Windows PowerShell.  Обратите внимание, что в любом языке, способном выполнить HTTP-запрос, можно использовать объект Webhook; Windows PowerShell используется здесь просто для примера.

Модуль Runbook ожидает список виртуальных машин, отформатированный в JSON, в теле запроса. Также мы включаем в заголовок запроса сведения о том, кто запускает модуль Runbook, а также о дате и времени запуска.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


На следующем рисунке показаны данные заголовка (с использованием трассировки [Fiddler](http://www.telerik.com/fiddler) ) из этого запроса. Они включают стандартные заголовки HTTP-запроса в дополнение к пользовательским заголовкам Date и From, которые мы добавили.  Каждое из этих значений доступно модулю Runbook в свойстве **RequestHeaders** объекта **WebhookData**.

![Кнопка Webhook](media/automation-webhooks/webhook-request-headers.png)

На следующем рисунке показан текст запроса (с использованием трассировки [Fiddler](http://www.telerik.com/fiddler)), доступное модулю Runbook в свойстве **RequestBody** объекта **WebhookData**. Оно отформатировано как JSON, так как это формат, который был включен в тело запроса.     

![Кнопка Webhook](media/automation-webhooks/webhook-request-body.png)

На следующем рисунке показан запрос, отправляемый из Windows PowerShell, а также получаемый в результате ответ.  Идентификатор задания извлекается из ответа и преобразуется в строку.

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
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

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


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Запуск модулей Runbook в ответ на оповещения Azure
Модули Runbook с поддержкой объектов webhook можно использовать для реагирования на [оповещения Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Ресурсы в Azure можно отслеживать, собирая статистику производительности, доступности и использования с помощью оповещений Azure. Вы можете получать оповещения на основе отслеживания метрик или событий в ресурсах Azure, сейчас учетные записи автоматизации поддерживают только метрики. Когда значение заданной метрики превышает установленный порог или активируется заданное событие, администратору или соадминистраторам службы отправляется уведомление о том, что оповещение необходимо разрешить. Дополнительные сведения о метриках и событиях см. в статье [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) (Получение уведомлений об оповещениях).

Оповещения Azure можно использовать не только как систему уведомлений, но и для запуска модулей Runbook. Служба автоматизации Azure позволяет запускать модули Runbook с поддержкой веб-перехватчика в ответ на оповещения Azure. Как только метрика превышает заданное пороговое значение, правило оповещения становится активным и запускает веб-перехватчик службы автоматизации, который, в свою очередь, запускает модуль Runbook.

![Объекты Webhook](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Контекст оповещения
Для ресурса Azure, такого как виртуальная машина, одной из основных метрик производительности является загрузка ЦП. Если загрузка ЦП составляет 100 % или превышает определенный порог в течение долгого времени, перезапустите виртуальную машину, чтобы решить проблему. Ситуацию можно исправить, настроив правило оповещения для виртуальной машины, используя в качестве метрики процент загрузки ЦП. Процент загрузки ЦП в данном случае взят для примера, и в ресурсах Azure можно настраивать другие метрики, а перезапуск виртуальной машины — лишь одно из действий по решению проблемы, и можно настраивать модуль Runbook на выполнение других действий.

Когда правило оповещения становится активным и запускает модуль Runbook с поддержкой веб-перехватчика, в модуль Runbook отправляется контекст оповещения. [Контекст оповещения](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) содержит сведения, позволяющие модулю Runbook идентифицировать ресурс, к которому будет применено действие, включая **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** и **Timestamp**. Контекст оповещения внедряется в основную часть объекта **WebhookData**, передаваемого в модуль Runbook, и извлекается с помощью свойства **Webhook.RequestBody**.

### <a name="example"></a>Пример
Создание виртуальной машины Azure в подписке и привязка [оповещения для мониторинга метрики процента загрузки ЦП](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). При создании оповещения введите в поле веб-перехватчика URL-адрес, сгенерированный при создании веб-перехватчика.

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



## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Сведения о просмотре состояния задания Runbook см. в статье [Выполнение модуля Runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Узнать, как использовать службу автоматизации Azure для выполнения действий на основе оповещений Azure, можно в статье [Обработка оповещений виртуальной машины Azure с помощью модулей Runbook службы автоматизации](automation-azure-vm-alert-integration.md).

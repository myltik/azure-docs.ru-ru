---
title: Запланированные события для виртуальных машин Windows в Azure | Документация Майкрософт
description: Запланированные события, использующие службу метаданных Azure, для виртуальных машин Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 63318b78607802d7d70d65a186a396cbc655c40b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423598"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Служба метаданных Azure. Запланированные события для виртуальных машин Windows

Запланированные события — это служба метаданных Azure, которая дает время вашему приложению для подготовки к обслуживанию виртуальной машины. Эта служба предоставляет сведения о предстоящем событии обслуживания (например, о перезагрузке), чтобы приложение могло подготовиться к ним и ограничить перебои в работе. Она доступна для всех типов виртуальных машин Azure, включая PaaS и IaaS (как для Windows, так и для Linux). 

Сведения о запланированных событиях в Linux см. в статье [Служба метаданных Azure. Запланированные события (предварительная версия) для виртуальных машин Linux](../linux/scheduled-events.md).

> [!Note] 
> Служба "Запланированные события" общедоступна во всех регионах Azure. В разделе [Доступность версий в регионах](#version-and-region-availability) вы найдете сведения о последних выпусках.

## <a name="why-scheduled-events"></a>Зачем нужны запланированные события

Во многих приложениях время на подготовку к обслуживанию виртуальной машины может положительно повлиять на их работу. Его можно использовать для выполнения конкретных задач по повышению доступности, надежности и удобства обслуживания: 

- создание контрольных точек и восстановление;
- фильтрация подключений;
- отработка отказа первичной реплики; 
- удаление из пула подсистемы балансировки нагрузки;
- ведение журнала событий.
- Корректное завершение работы 

Используя запланированные события, приложение может учесть время, когда будет выполняться техническое обслуживание, и запустить задачи для ограничения его влияния.  

Запланированные события предусматривают события в следующих случаях:
- обслуживание, инициированное платформой (например, обновление ОС узла);
- обслуживание, инициированное пользователем (например, пользователь перезагружает или переразвертывает виртуальную машину).

## <a name="the-basics"></a>Основы  

Служба метаданных Azure предоставляет сведения о работающих виртуальных машинах через конечную точку REST, доступную из виртуальной машины. Эта информация предоставляется через немаршрутизируемый IP-адрес, то есть она недоступна вне виртуальной машины.

### <a name="endpoint-discovery"></a>Обнаружение конечной точки
Для виртуальных машин с поддержкой виртуальной сети служба метаданных доступна со статического немаршрутизируемого IP-адреса, `169.254.169.254`. Полная конечная точка для последней версии службы "Запланированные события" выглядит так: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Если виртуальная машина не создается в виртуальной сети, что является стандартным сценарием для облачных служб и классических виртуальных машин, для обнаружения используемого IP-адреса требуется дополнительная логика. Просмотрите этот пример, чтобы узнать как выполнить [обнаружение конечной точки узла](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Доступность версий в регионах
Служба запланированных событий имеет версии. Версии являются обязательными. На данный момент используется версия от `2017-08-01`.

| Version (версия) | Тип выпуска | регионы | Заметки о выпуске | 
| - | - | - | - |
| 2017-08-01 | Общедоступная версия | Все | <li> Удалено подчеркивание перед именами ресурсов для виртуальных машин Iaas.<br><li>Требование заголовка метаданных принудительно применяется для всех запросов. | 
| 2017-03-01 | Предварительный просмотр | Все |<li>Первый выпуск

> [!NOTE] 
> В предыдущих выпусках предварительной версии в качестве api-version поддерживалось значение {latest}. Этот формат больше не поддерживается и в дальнейшем будет считаться устаревшим.

### <a name="enabling-and-disabling-scheduled-events"></a>Включение и выключение службы "Запланированные события"
Служба "Запланированные события" включается для вашей службы, когда вы впервые запрашиваете события. При первом вызове возможна задержка ответа до двух минут.

Служба "Запланированные события" выключается для вашей службы, если она не отправляет запросы в течение 24 часов.

### <a name="user-initiated-maintenance"></a>Обслуживание, инициированное пользователем
Обслуживание виртуальных машин, инициированное пользователем на портале Azure, в API, Azure CLI или PowerShell, приведет к созданию запланированных событий. Это позволяет протестировать логику подготовки обслуживания в вашем приложении, а также позволит ему подготовиться к обслуживанию, инициированном пользователем.

При перезапуске виртуальной машины планируется событие с типом `Reboot`. При развертывании виртуальной машины планируется событие с типом `Redeploy`.

## <a name="using-the-api"></a>Использование API

### <a name="headers"></a>Заголовки
При запросе службы метаданных необходимо указать заголовок `Metadata:true`, чтобы избежать случайного перенаправления запроса. Заголовок `Metadata:true` является обязательным для всех запросов запланированных событий. Если не удалось включить заголовок в запрос, служба метаданных выдаст ответ "Ошибка запроса".

### <a name="query-for-events"></a>Запрос сведений о событиях
Чтобы получить сведения о запланированных событиях, достаточно отправить следующий запрос:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Ответ содержит массив запланированных событий. Если это будет пустой массив, значит сейчас запланированных событий нет.
Если передаются запланированные события, массив событий в ответе выглядит так: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Свойства события
|Свойство  |  ОПИСАНИЕ |
| - | - |
| EventId | Глобальный уникальный идентификатор этого события. <br><br> Пример: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Влияние, которое оказывает это событие. <br><br> Значения: <br><ul><li> `Freeze`. Виртуальная машина будет приостановлена на несколько секунд. Работа ЦП приостанавливается, но это действие не повлияет на память, открытые файлы и сетевые подключения. <li>`Reboot`. Планирование перезагрузки виртуальной машины (временная память будет потеряна). <li>`Redeploy`. Виртуальная машина будет перемещена на другой узел с потерей данных на временных дисках. |
| ResourceType | Тип ресурса, на который влияет это событие. <br><br> Значения: <ul><li>`VirtualMachine`|
| Ресурсы| Список ресурсов, на которые влияет это событие. Обязательно будет содержать виртуальные машины максимум из одного [домена обновления](manage-availability.md), но не может содержать все машины в таком домене. <br><br> Пример: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Event Status | Состояние этого события. <br><br> Значения: <ul><li>`Scheduled`. Это запланированное событие состоится по истечении времени, указанного в свойстве `NotBefore`.<li>`Started`. Это событие запущено.</ul> Состояние `Completed` (или аналогичное) никогда не предоставляется; событие не возвращается после завершения события.
| NotBefore| Время, после которого может быть запущено это событие. <br><br> Пример: <br><ul><li> Пн, 19 сентября 2016 г., 18:29:47 (GMT)  |

### <a name="event-scheduling"></a>Планирование события
В зависимости от типа каждое будущее событие будет выполняться минимальное количество времени. Это время отражается в свойстве события `NotBefore`. 

|EventType  | Минимальное время уведомления |
| - | - |
| Freeze| 15 минут |
| Reboot | 15 минут |
| Повторное развертывание | 10 минут |

### <a name="event-scope"></a>Область события     
Запланированные события доставляются:        
 - всем виртуальным машинам в облачной службе;      
 - всем виртуальным машинам в группе доступности;      
 - всем виртуальным машинам в группе размещения масштабируемого набора.         

Поэтому важно проверять поле `Resources` в событии, чтобы определить, на какие виртуальные машины повлияет конкретное событие. 

### <a name="starting-an-event"></a>Запуск события 

Когда вы узнаете о предстоящем событии и выполняете все действия для корректного завершения работы, вы можете утвердить ожидающее событие, выполнив вызов `POST` к службе метаданных Azure с помощью `EventId`. Это указывает службе Azure, что она может сократить минимальное время уведомления (если возможно). 

Ниже приводится ожидаемый код json в тексте запроса `POST`. Запрос должен содержать список `StartRequests`. Каждый `StartRequest` содержит `EventId` для события, которое нужно ускорить:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Подтверждение событий позволяет выполнить событие для всех ресурсов `Resources` в событии, а не только для виртуальной машины, которая подтверждает событие. Таким образом, можно выбрать "лидера" для координации подтверждения, например первую виртуальную машину в поле `Resources`.


## <a name="powershell-sample"></a>Пример для PowerShell 

В следующем примере выполняется запрос запланированных событий в службе метаданных, а затем утверждение каждого ожидающего события.

```PowerShell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-08-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Дополнительная информация 

- Смотрите [демонстрационную версию службы "Запланированные события" ](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) в серии "Пятница с Azure". 
- Просмотрите примеры кода запланированных событий в статье [Azure Metadata Service: Scheduled Events Samples](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm) (Служба метаданных Azure: примеры запланированных событий).
- Дополнительные сведения об API, доступных в [службе метаданных экземпляра](instance-metadata-service.md).
- Подробнее о [плановом обслуживании виртуальных машин Windows в Azure](planned-maintenance.md).

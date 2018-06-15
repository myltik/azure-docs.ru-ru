---
title: Как регистрировать события в концентраторах событий Azure в службе управления Azure API | Документация Майкрософт
description: Узнайте, как регистрировать события в концентраторах событий Azure в службе управления Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 3f4da70d94d28496f5b08035ead0ef7acf1ca3bc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
ms.locfileid: "29969603"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Как регистрировать события в концентраторах событий Azure в службе управления Azure API
Концентраторы событий Azure — это высокомасштабируемая служба приема данных, которая может обрабатывать миллионы событий в секунду, позволяя вам обрабатывать и анализировать огромное количество данных, создаваемых подключенными устройствами и приложениями. Концентраторы событий выступают в качестве "входной двери"для событий конвейера. После того как данные поступили в концентратор событий, они могут быть преобразованы и сохранены с использованием любого поставщика аналитики в режиме реального времени или адаптера пакетной обработки или хранения. Концентраторы событий отделяют создание потока событий от потребления этих событий, чтобы потребители событий могли обращаться к событиям по собственному расписанию.

Эта статья сопровождает видеоролик [Интеграция службы управления API Azure с концентраторами событий](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/). В ней рассматривается регистрация событий управления API с помощью концентраторов событий Azure.

## <a name="create-an-azure-event-hub"></a>Создание концентратора событий Azure

Подробные инструкции о том, как создать концентратор событий и получить строки подключения, необходимые для отправки событий в концентратор и получения событий из него, см. в статье [Создание пространства имен концентраторов событий и концентратора событий с помощью портала Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Создание средства ведения журнала для управления API
Теперь, когда концентратор событий создан, нужно настроить [средство ведения журнала](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) в службе управления API, которое сможет регистрировать события в концентраторе событий.

Средства ведения журнала службы управления API настраиваются с помощью [REST API службы управления API](http://aka.ms/smapi). Прежде чем использовать REST API впервые, просмотрите информацию о [необходимых компонентах](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) и убедитесь, что [включен доступ к REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Чтобы создать средство ведения журнала, выполните HTTP-запрос PUT, используя следующий шаблон URL-адреса:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Замените `{your service}` именем экземпляра службы управления API.
* Замените `{new logger name}` требуемым именем нового средства ведения журнала. Вы укажете это имя при настройке политики [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub).

Добавьте в запрос следующие заголовки:

* тип содержимого: «application/json»;
* авторизация: SharedAccessSignature 58...
  * указания по созданию `SharedAccessSignature` см. в [справочнике по REST API Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Укажите текст запроса, используя следующий шаблон:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* Для параметра `loggerType` нужно задать значение `AzureEventHub`.
* `description` предоставляет дополнительное описание средства ведения журнала. При желании эту строку можно оставить пустой.
* `credentials` содержит `name` и `connectionString` концентратора событий Azure.

Если при выполнении запроса средство ведения журнала создано, возвращается код состояния `201 Created` .

> [!NOTE]
> Другие возможные коды возврата и их причины см. в статье, посвященной [созданию средств ведения журнала](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Чтобы узнать, как выполнять другие операции, такие как перечисление, обновление и удаление, см. документацию по [средству ведения журнала](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity).
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Настройка политик регистрации в концентраторах событий

Если средство ведения журналов в управлении API уже настроено, вы можете настроить политики регистрации в концентраторах событий для регистрации нужных событий. Политику регистрации в концентраторах событий можно использовать в разделе входящей или исходящей политики.

1. Перейдите к экземпляру службы управления API Azure.
2. Выберите вкладку API.
3. Выберите API-интерфейс, для которого требуется добавить политику. В этом примере мы добавляем политику **Echo API** в продукт **Unlimited**.
4. Выберите **Все операции**.
5. В верхней части экрана выберите вкладку "Конструктор".
6. В окне обработки входящих или исходящих запросов щелкните треугольник (рядом со значком карандаша).
7. Выберите редактор кода. Дополнительные сведения см. в статье [Настройка или изменение политик службы управления API Azure](set-edit-policies.md).
8. Наведите указатель мыши на раздел политики `inbound` или `outbound`.
9. В окне справа выберите **Дополнительные политики** > **Log to Event Hub** (Регистрация в концентраторе событий). В результате будет вставлен шаблон инструкции политики `log-to-eventhub`.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Замените `logger-id` именем средства ведения журнала службы управления API, настроенного на предыдущем этапе.

Вы можете использовать любое выражение, которое возвращает строку в качестве значения для элемента `log-to-eventhub` . В этом примере регистрируется строка, содержащая дату и время, имя службы, идентификатор запроса, IP-адрес запроса и имя операции.

Нажмите кнопку **Сохранить** , чтобы сохранить конфигурацию обновленной политики. Сразу же после сохранения политика становится активной, а события регистрируются в указанном концентраторе событий.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительная информация о концентраторах событий Azure
  * [Приступая к работе с концентраторами событий Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Прием сообщений через EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Руководство по программированию концентраторов событий](../event-hubs/event-hubs-programming-guide.md)
* Дополнительные сведения об интеграции службы управления API и концентраторов событий
  * [Справочник по сущности "Средство ведения журнала"](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Справочник по политике регистрации в концентраторе событий](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Мониторинг API-интерфейсов с помощью управления API Azure, концентраторов событий и Runscope](api-management-log-to-eventhub-sample.md)    

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png

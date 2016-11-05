---
title: Как регистрировать события в концентраторах событий Azure в службе управления Azure API | Microsoft Docs
description: Узнайте, как регистрировать события в концентраторах событий Azure в службе управления Azure API.
services: api-management
documentationcenter: ''
author: steved0x
manager: erikre
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2016
ms.author: sdanie

---
# Как регистрировать события в концентраторах событий Azure в службе управления Azure API
Концентраторы событий Azure — это высокомасштабируемая служба приема данных, которая может обрабатывать миллионы событий в секунду, позволяя вам обрабатывать и анализировать огромное количество данных, создаваемых подключенными устройствами и приложениями. Концентраторы событий выступают в качестве "входной двери"для событий конвейера. После того как данные поступили в концентратор событий, они могут быть преобразованы и сохранены с использованием любого поставщика аналитики в режиме реального времени или адаптера пакетной обработки или хранения. Концентраторы событий отделяют создание потока событий от потребления этих событий, чтобы потребители событий могли обращаться к событиям по собственному расписанию.

Эта статья сопровождает видеоролик [Интеграция службы управления API Azure с концентраторами событий](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/). В ней рассматривается регистрация событий управления API с помощью концентраторов событий Azure.

## Создание концентратора событий Azure
Чтобы создать новый концентратор событий, войдите на [классический портал Azure](https://manage.windowsazure.com) и последовательно щелкните **Создать**->**Службы приложений**->**Служебная шина**->**Концентратор событий**->**Быстрое создание**. Введите имя концентратора событий, регион, выберите подписку и пространство имен. Если пространство имен не было создано ранее, его можно создать, введя имя в текстовом поле **Пространство имен**. После настройки всех свойств щелкните **Создать новый концентратор событий**, чтобы создать концентратор событий.

![Создание концентратора событий][create-event-hub]

Затем перейдите на вкладку **Настройка**, связанную с новым концентратором событий, и создайте две **политики общего доступа**. Назовите первую **Отправка** и назначьте ей разрешения **Отправка**.

![Политика «Отправка»][sending-policy]

Назовите вторую **Получение**, назначьте ей разрешения **Прослушивание** и щелкните **Сохранить**.

![Политика «Получение»][receiving-policy]

Каждая политика общего доступа позволяет приложениям отправлять события концентратору событий и получать их. Чтобы получить доступ к строкам подключения для этих политик, перейдите на вкладку **Панель мониторинга** концентратора событий и щелкните **Сведения о подключении**.

![Строка подключения][event-hub-dashboard]

Строка подключения **Отправка** используется при регистрации событий, а строка подключения **Получение** — при загрузке событий из концентратора событий.

![Строка подключения][event-hub-connection-string]

## Создание средства ведения журнала для управления API
Теперь, когда концентратор событий создан, нужно настроить [средство ведения журнала](https://msdn.microsoft.com/library/azure/mt592020.aspx) в службе управления API, которое сможет регистрировать события в концентраторе событий.

Средства ведения журнала службы управления API настраиваются с помощью [REST API службы управления API](http://aka.ms/smapi). Прежде чем использовать REST API впервые, просмотрите информацию о [необходимых компонентах](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) и убедитесь, что [включен доступ к REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Чтобы создать средство ведения журнала, выполните HTTP-запрос PUT, используя следующий шаблон URL-адреса.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

* Замените `{your service}` именем экземпляра службы управления API.
* Замените `{new logger name}` требуемым именем нового средства ведения журнала. Вы укажете это имя при настройке политики [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) (регистрация в концентраторе событий).

Добавьте в запрос следующие заголовки:

* тип содержимого: «application/json»;
* авторизация: «SharedAccessSignature uid=...»:
  * указания по созданию `SharedAccessSignature` см. в разделе [Проверка подлинности REST API управления API Azure](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Укажите текст запроса, используя следующий шаблон.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

* Для параметра `type` нужно задать значение `AzureEventHub`.
* `description` предоставляет дополнительное описание средства ведения журнала. При желании эту строку можно оставить пустой.
* `credentials` содержит `name` и `connectionString` концентратора событий Azure.

Если при выполнении запроса средство ведения журнала создано, возвращается код состояния `201 Created`.

> [!NOTE]
> Другие возможные коды возврата и их причины см. в разделе [Создание средства ведения журнала](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Чтобы узнать, как выполнять другие операции, такие как перечисление, обновление и удаление, см. документацию по сущности [Средство ведения журнала](https://msdn.microsoft.com/library/azure/mt592020.aspx).
> 
> 

## Настройка политик регистрации в концентраторах событий
Если средство ведения журналов в управлении API уже настроено, вы можете настроить политики регистрации в концентраторах событий для регистрации нужных событий. Политику регистрации в концентраторах событий можно использовать в разделе входящей или исходящей политики.

Чтобы настроить политики, войдите на [классический портал Azure](https://manage.windowsazure.com), перейдите к службе управления API и щелкните **портал издателя** или **Управление**, чтобы получить доступ к порталу издателя.

![Портал издателя][publisher-portal]

В меню управления API слева щелкните **Политики**, выберите желаемый продукт и интерфейс API и щелкните **Добавить политику**. В этом примере мы добавляем политику **Echo API** (API вывода на экран) в продукт **Unlimited**.

![добавление политики;][add-policy]

Наведите указатель мыши на раздел политики `inbound` и щелкните политику **Регистрация в концентраторе событий**, чтобы вставить шаблон инструкции политики `log-to-eventhub`.

![Редактор политики][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Замените `logger-id` именем средства ведения журнала службы управления API, настроенного на предыдущем этапе.

Вы можете использовать любое выражение, которое возвращает строку в качестве значения для элемента `log-to-eventhub`. В этом примере регистрируется строка, содержащая дату и время, имя службы, идентификатор запроса, IP-адрес запроса и имя операции.

Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию обновленной политики. Сразу же после сохранения политика становится активной, а события регистрируются в указанном концентраторе событий.

## Дальнейшие действия
* Дополнительная информация о концентраторах событий Azure
  * [Приступая к работе с концентраторами событий Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
  * [Прием сообщений через EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
  * [Руководство по программированию концентраторов событий](../event-hubs/event-hubs-programming-guide.md)
* Дополнительные сведения об интеграции службы управления API и концентраторов событий
  * [Справочник по сущности "Средство ведения журнала"](https://msdn.microsoft.com/library/azure/mt592020.aspx)
  * [Справочник по политике регистрации в концентраторе событий](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
  * [Мониторинг API-интерфейсов с помощью службы управления API Azure, концентраторов событий и Runscope](api-management-log-to-eventhub-sample.md)

## Просмотрите видеоруководство
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
> 
> 

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png

<!---HONumber=AcomDC_0810_2016-->
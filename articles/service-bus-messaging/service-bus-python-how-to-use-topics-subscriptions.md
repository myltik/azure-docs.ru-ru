---
title: "Использование разделов служебной шины Azure с Python | Документация Майкрософт"
description: "Узнайте, как использовать разделы и подписки служебной шины Azure в Python."
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: d0714870a0b01eca5e07c171c5ca8d0c0c1d6df1
ms.openlocfilehash: 69f8807d509c31ae4aadeb16731fc481039a7e20


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Как использовать разделы и подписки служебной шины
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этой статье описывается использование разделов и подписок служебной шины. Примеры написаны на Python и используют [пакет Azure для Python][Python Azure package]. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Если требуется установить Python или [пакет Azure для Python][Python Azure package], дополнительные сведения см. в документе [Руководство по установке Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Создание раздела
Объект **ServiceBusService** позволяет работать с разделами. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к служебной шине программным способом:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Следующий код создает объект **ServiceBusService**. Замените `mynamespace`, `sharedaccesskeyname` и `sharedaccesskey` своим пространством имен, именем и значением ключа подписанного URL-адреса (SAS).

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Значения для имени и значение ключа SAS можно получить на [портале Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

Метод **create\_topic** также поддерживает дополнительные параметры, позволяющие переопределить настройки раздела по умолчанию, такие как срок жизни сообщения или максимальный размер раздела. В следующем примере показано, как установить максимальный размер раздела 5 ГБ и значение срока жизни в 1 минуту.

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Создание подписок
Подписки на разделы также создаются с помощью объекта **ServiceBusService**. Подписки имеют имена и могут использовать дополнительный фильтр, который ограничивает набор сообщений, доставляемых в виртуальную очередь подписки.

> [!NOTE]
> Подписки хранятся постоянно и продолжают существовать либо до их удаления, либо до удаления раздела, на который они подписаны.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Создание подписки с фильтром по умолчанию (MatchAll)
Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, то все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка AllMessages и используется фильтр по умолчанию **MatchAll**.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Создание подписок с фильтрами
Вы также можете задать фильтры, позволяющие определять, какие отправляемые в раздел сообщения должны отображаться в рамках той или иной подписки.

Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Фильтры можно добавить в подписку с помощью метода **create\_rule** объекта **ServiceBusService**. Этот метод позволяет добавлять новые фильтры в существующую подписку.

> [!NOTE]
> Так как ко всем новым подпискам автоматически применяется фильтр по умолчанию, сначала необходимо удалить фильтр по умолчанию, иначе фильтр **MatchAll** переопределит поведение всех остальных заданных фильтров. Вы можете удалить правило по умолчанию с помощью метода **delete\_rule** объекта **ServiceBusService**.
> 
> 

В следующем примере создается подписка с именем `HighMessages`, содержащая объект **SqlFilter**, который выбирает только те сообщения, значение настраиваемого свойства **messagenumber** которых больше 3.

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Аналогичным образом в следующем примере создается подписка с именем `LowMessages` и фильтром **SqlFilter**. Он выбирает только те сообщения, у которых значение свойства **messagenumber** меньше или равно 3.

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Теперь, когда в раздел `mytopic` отправляется сообщение, оно будет всегда доставляться получателям, подписанным на раздел **AllMessages**, и выборочно доставляться получателям, подписанным на разделы **HighMessages** и **LowMessages** в зависимости от содержания сообщения.

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
Чтобы отправить сообщение в раздел служебной шины, ваше приложение должно использовать метод **send\_topic\_message** объекта **ServiceBusService**.

В следующем примере показано, как отправить пять тестовых сообщений в `mytopic`. Обратите внимание, что значение свойства **messagenumber** сообщения зависит от итерации цикла (определяет, какие подписки его получают).

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
Сообщения извлекаются из подписки с помощью метода **receive\_subscription\_message** в объекте **ServiceBusService**.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Прочитанные сообщения удаляются из подписки, если для параметра **peek\_lock** установлено значение **False**. Вы можете прочитать (извлечь) и заблокировать сообщение, не удаляя его из очереди, присвоив параметру **peek\_lock** значение **True**.

Поведение по умолчанию — чтение и удаление сообщения как часть операции получения — является простейшей моделью, оптимальной для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если параметр **peek\_lock** имеет значение **True**, получение становится операцией из двух этапов, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Заканчивая обработку сообщения (или надежно сохраняя его для будущей обработки), приложение завершает второй этап процесса получения, вызывая метод **delete** объекта **Message**. Метод **delete** помечает сообщение как использованное и удаляет его из подписки.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод **unlock** объекта **Message**. После этого служебная шина разблокирует сообщение в подписке и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, при блокировке сообщения в подписке предусмотрено определенное время ожидания. Если приложение не может обработать сообщение до истечения времени блокировки (например, аварийно завершит работу), то служебная шина автоматически разблокирует сообщение и оно станет доступным для повторного получения.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **delete**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="delete-topics-and-subscriptions"></a>Удаление разделов и подписок
Разделы и подписки хранятся постоянно, и их нужно удалять явным образом на [портале Azure][Azure portal] или с помощью программных средств. В следующем примере показано, как удалить раздел с именем `mytopic`

```python
bus_service.delete_topic('mytopic')
```

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере кода показано, как удалить подписку с именем `HighMessages` из раздела `mytopic`.

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Дальнейшие действия
Вы узнали основные сведения о разделах служебной шины. Для получения дополнительных сведений используйте следующие ссылки.

* Дополнительные сведения см. в статье [Очереди, разделы и подписки служебной шины][Queues, topics, and subscriptions].
* Справочник по [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Python Azure package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus quotas]: service-bus-quotas.md 



<!--HONumber=Jan17_HO2-->



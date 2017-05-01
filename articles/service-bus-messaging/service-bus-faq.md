---
title: "Часто задаваемые вопросы о служебной шине Azure | Документация Майкрософт"
description: "Ответы на некоторые часто задаваемые вопросы о служебной шине Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: cbbef00cb4c54f4df2634fbe7b3dacbd34a175bd
ms.lasthandoff: 04/07/2017


---
# <a name="service-bus-faq"></a>Часто задаваемые вопросы о служебной шине
В этой статье содержатся ответы на некоторые часто задаваемые вопросы о служебной шине Microsoft Azure. Вы также можете посетить страницу [Часто задаваемые вопросы о поддержке Azure](http://go.microsoft.com/fwlink/?LinkID=185083) , где содержатся общие сведения о расценках и поддержке Azure.

## <a name="general-questions-about-azure-service-bus"></a>Общие вопросы о служебной шине Azure
### <a name="what-is-azure-service-bus"></a>Что такое служебная шина Azure?
[Служебная шина Azure](service-bus-messaging-overview.md) — это облачная платформа асинхронного обмена сообщениями, которая позволяет отправлять данные между несвязанными системами. Майкрософт предоставляет этот механизм в качестве службы. Это значит, что для его использования не потребуется устанавливать собственное оборудование.

### <a name="what-is-a-service-bus-namespace"></a>Что такое пространство имен служебной шины?
[Пространство имен](service-bus-create-namespace-portal.md) предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении. Необходимо создать пространство имен, чтобы использовать служебную шину. Это будет одним из первых шагов.

### <a name="what-is-an-azure-service-bus-queue"></a>Что такое очередь служебной шины Azure?
[Очередь служебной шины](service-bus-queues-topics-subscriptions.md) — это сущность, в которой сохраняются сообщения. Их целесообразно использовать при наличии нескольких приложений или нескольких частей распределенного приложения, которые должны взаимодействовать друг с другом. Очередь подобна центру распределения, куда поступают несколько продуктов (сообщений), которые затем отправляются из этого расположения.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Что такое разделы и подписки служебной шины Azure?
Раздел можно представить в виде очереди. При использовании нескольких подписок он становится расширенной моделью обмена сообщениями. По сути, это инструмент связи по принципу "один ко многим". В рамках этой модели публикации и подписки (*pub/sub*) сообщение может быть отправлено из приложения в раздел с несколькими подписками, а затем получено несколькими приложениями.

### <a name="what-is-a-partitioned-entity"></a>Что такое секционированная сущность?
Обычная очередь или раздел обрабатываются одним брокером сообщений и сохраняются в одном хранилище сообщений. [Секционированная очередь или раздел](service-bus-partitioning.md) обрабатывается несколькими брокерами сообщений и хранится в нескольких хранилищах сообщений. Это означает, что общая пропускная способность секционированной очереди или раздела больше не ограничивается производительностью одного брокера сообщений или хранилища сообщений. Кроме того, при возникновении временного сбоя хранилища сообщений секционированная очередь или раздел останутся доступными.

Имейте в виду, что при использовании секционирования сущностей их упорядоченность не гарантируется. Если раздел недоступен, вы по-прежнему можете отправлять и получать сообщения из других секций.

## <a name="best-practices"></a>Рекомендации
### <a name="what-are-some-azure-service-bus-best-practices"></a>Рекомендации по работе со служебной шиной Azure
* [Рекомендации по повышению производительности с помощью обмена сообщениями через служебную шину][Best practices for performance improvements using Service Bus]. В этой статье описывается, как оптимизировать производительность обмена сообщениями.

### <a name="what-should-i-know-before-creating-entities"></a>Что необходимо знать перед созданием сущностей?
Приведенные ниже свойства очереди и раздела являются неизменяемыми. Учитывайте это при подготовке сущностей, так как эти свойства можно изменить, только создав сущность для замены.

* Размер
* Секционирование
* Сеансы:
* Обнаружение дубликатов
* экспресс-сущность.

## <a name="pricing"></a>Цены
В этом разделе содержатся ответы на некоторые часто задаваемые вопросы о ценах на использование служебной шины. Также можно посетить страницу [часто задаваемых вопросов о поддержке Azure](http://go.microsoft.com/fwlink/?LinkID=185083), где содержатся общие сведения о структуре ценообразования Microsoft Azure. Дополнительные сведения о ценах на использование служебной шины см. на странице [цен на служебную шину](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus"></a>Как выставляется цена на служебную шину?
Подробные сведения о ценах на использование служебной шины см. [здесь][Pricing overview]. Помимо указанной платы, также взимается плата за связанные операции передачи данных, исходящих из центра обработки данных, в котором подготавливается ваше приложение.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Какое использование служебной шины считается передачей данных? А какое не считается?
Любая передача данных в пределах заданного региона Azure выполняется бесплатно, как и любая входящая передача данных. Передача данных за пределы региона оплачивается как исходящий трафик. Соответствующие тарифы указаны [здесь](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Взимает ли служебная шина плату за использование хранилища?
Нет, служебная шина не взимает плату за использование хранилища. Тем не менее существует квота, ограничивающая максимальный объем данных, которые могут быть сохранены в каждой очереди или разделе. См. следующий раздел часто задаваемых вопросов.

## <a name="quotas"></a>Квоты

Список ограничений и квот на служебную шину доступен в этом [обзоре квот служебной шины][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Есть ли у служебной шины квоты использования?
По умолчанию для любой облачной службы Майкрософт устанавливается квота совокупного месячного использования в рамках всех подписок клиента. Мы понимаем, что вам может потребоваться больше, чем разрешено этими ограничениями. В таком случае вы можете в любое время обратиться в службу поддержки пользователей, чтобы мы изменили эти ограничения на основе ваших потребностей. Для служебной шины суммарная квота на использование составляет 5 миллиардов сообщений в месяц.

Хотя мы оставляем за собой право отключить учетную запись клиента, превысившего свои месячные квоты использования, перед выполнением каких-либо действий мы отправим уведомления по электронной почте, а также предпримем несколько попыток, чтобы связаться с клиентом. Клиентам, превысившим свои квоты, будут выставлены соответствующие счета.

Как и в случае с другими службами в Azure, служебная шина использует набор особых квот для обеспечения справедливого использования ресурсов. Ниже приведены квоты использования, применяемые службой.

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Какие исключения порождаются интерфейсами API служебной шины Azure? Какие действия можно предпринять в отношении этих исключений?
Список возможных исключений служебной шины приведен в разделе [Общие сведения об исключениях][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Что такое подписанный URL-адрес? На каких языках можно создавать подписи?
Подписанные URL-адреса представляют собой механизм аутентификации на базе алгоритма безопасного хэширования SHA-256 или URI. Дополнительные сведения о том, как создавать собственные подписи с использованием Node, PHP, Java и C\#, см. в статье [Подписи коллективного доступа][Shared Access Signatures].

## <a name="subscription-and-namespace-management"></a>Управление подпиской и пространством имен
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Как перенести пространство имен в другую подписку Azure?
Следуя [этим инструкциям](../azure-resource-manager/resource-group-move-resources.md#use-portal), с помощью портала Azure можно перенести пространства имен служебной шины в другую подписку. Если вы предпочитаете использовать PowerShell, выполните инструкции, приведенные ниже. 

Следующая последовательность команд перемещает пространство имен из одной подписки Azure в другую. Для выполнения этой операции пространство имен уже должно быть активным, а пользователь, выполняющий команды PowerShell, должен обладать правами администратора в исходной и целевой подписках.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о служебной шине см. в следующих разделах.

* [Introducing Azure Service Bus Premium Messaging](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Общие сведения об обмене сообщениями через служебную шину Azure уровня "Премиум") (запись блога)
* [Introducing Azure Service Bus Premium Messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Общие сведения об обмене сообщениями через служебную шину Azure уровня "Премиум") (Channel9)
* [Обзор служебной шины](service-bus-messaging-overview.md)
* [Обзор архитектуры служебной шины Azure](service-bus-fundamentals-hybrid-solutions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md


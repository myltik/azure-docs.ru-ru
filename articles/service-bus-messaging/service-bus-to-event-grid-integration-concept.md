---
title: "Общие сведения об интеграции служебной шины Azure со службой \"Сетка событий\" | Документация Майкрософт"
description: "Описание обмена сообщениями служебной шины и интеграции со службой \"Сетка событий\""
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 17f6c107ea1adfd4463ff4cc205fe11d111acb84
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Общие сведения об интеграции служебной шины Azure со службой "Сетка событий Azure"

В служебной шине Azure появилась возможность интеграции со службой "Сетка событий Azure". Ключевой сценарий применения этой возможности — благодаря ей для очередей или подписок служебной шины, имеющих низкий объем сообщений, не требуется постоянный опрос на наличие сообщений получателем. Теперь когда в подписке или в очереди есть сообщения, но получатели отсутствуют, служебная шина может выдавать события для службы "Сетка событий Azure". Вы можете создавать подписки на службу "Сетка событий Azure" для пространств имен служебной шины, ожидать передачи этих событий и реагировать на них, запустив получатель. Благодаря этой возможности служебную шину можно использовать в моделях реактивного программирования.

Чтобы включить эту функцию, вам понадобится следующее:

* Пространство имен служебной шины Azure уровня "Премиум" минимум с одной очередью служебной шины или раздел служебной шины минимум с одной подпиской.
* Доступ к пространству имен служебной шины Azure с правами участника.
* Подписка на службу "Сетка событий Azure" для пространства имен служебной шины. Эта подписка необходима для получения из службы "Сетка событий Azure" уведомления о том, что имеются сообщения, ожидающие действий. Типичными подписчиками могут быть приложения логики, функции Azure или веб-перехватчик, связанные с веб-приложением, которое обрабатывает сообщения. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Проверка наличия доступа с правами участника

Перейдите к пространству имен служебной шины и выберите "Управление доступом (IAM)", как показано ниже.

![1][]

### <a name="events-and-event-schemas"></a>События и схемы событий

Сейчас служебная шина Azure отправляет события в двух сценариях.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Кроме того, она использует стандартные механизмы безопасности и [проверки подлинности](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication) службы "Сетка событий Azure".

Чтобы получить дополнительные сведения о схемах событий службы "Сетка событий", перейдите по [этой](https://docs.microsoft.com/en-us/azure/event-grid/event-schema) ссылке.

#### <a name="active-messages-available-event"></a>Событие наличия активных сообщений

Это событие создается, если в очереди или подписке присутствуют активные сообщения, а получатели отсутствуют.

Схема для этого события выглядит следующим образом.

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>Событие доступных сообщений, перемещенных в очередь недоставленных

На одну очередь недоставленных сообщений приходится минимум одно событие, имеющее сообщения, для которых отсутствуют активные получатели.

Схема для этого события выглядит следующим образом.

```JSON
[{
     "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>Как часто выдаются события и в каком количестве?

При наличии нескольких очередей и разделов или подписок в пространстве имен можно получить по крайней мере одно событие в очереди и одно в подписке. События выдаются незамедлительно, если в сущности служебной шины нет сообщений и приходит новое сообщение, или каждые две минуты, если служебная шина Azure не обнаружит активного получателя. События не прерываются при просмотре сообщений.

По умолчанию служебная шина Azure выдает события для всех сущностей в пространстве имен. Если необходимо получать события только для определенных сущностей, ознакомьтесь со сведениями в разделе о фильтрации.

### <a name="filtering-limiting-from-where-you-get-events"></a>Фильтрация для ограничения источников получения событий

Если нужно получать события, например только одной очереди или одной подписки в пространстве имен, используйте фильтры Begins with (Начинается с) или Ends with (Заканчивается на), предоставляемые в службе "Сетка событий Azure". В некоторых интерфейсах эти фильтры называются "Префикс" и "Суффикс". Если требуется получить события для нескольких, но не всех очередей и подписок, создайте несколько разных подписок на службу "Сетка событий Azure" и укажите для каждой из них фильтр.

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>Создание подписки на службу "Сетка событий Azure" для пространства имен служебной шины

Существует три разных способа создания подписки на службу "Сетка событий Azure" для пространств имен служебной шины.

* [Портал Azure](#portal-instructions)
* [интерфейс командной строки Azure](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>Инструкции по работе на портале

Чтобы создать подписку на службу "Сетка событий Azure", перейдите к пространству имен на портале Azure и выберите колонку "Сетка событий". Щелкните "+ Подписка на события". На изображении ниже показано пространство имен, в котором уже есть несколько подписок на службу "Сетка событий".

![20][]

На следующем снимке экрана показан образец оформления подписки на функцию Azure или веб-перехватчик без применения каких-либо фильтров.

![21][]

## <a name="azure-cli-instructions"></a>Инструкции по работе в Azure CLI

Сначала убедитесь в наличии Azure CLI версии 2.0 и выше. Вы можете скачать установщик здесь. Затем нажмите клавиши Windows+X и откройте новую консоль PowerShell с правами администратора. В качестве альтернативы можно использовать командную оболочку на портале Azure.

Выполните следующий код.

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Инструкции по работе в среде PowerShell

Убедитесь в наличии Azure PowerShell. Ее можно найти здесь. Затем нажмите клавиши Windows+X и откройте новую консоль PowerShell с правами администратора. В качестве альтернативы можно использовать командную оболочку на портале Azure.

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

Здесь вы можете просмотреть остальные параметры установки или [выполнить тестирование поступления событий](#test-that-events-are-flowing).

## <a name="next-steps"></a>Дополнительная информация

* [Примеры](service-bus-to-event-grid-integration-example.md) использования служебной шины со службой "Сетка событий".
* Дополнительные сведения о службе "Сетка событий" Azure см. [здесь](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Дополнительные сведения о решении "Функции Azure" см. [здесь](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Узнайте больше о [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/).
* Дополнительные сведения о [служебной шине Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
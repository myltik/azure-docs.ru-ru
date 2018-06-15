---
title: Общие сведения об интеграции служебной шины Azure со службой "Сетка событий" | Документация Майкрософт
description: Описание обмена сообщениями служебной шины и интеграции со службой "Сетка событий"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: fbea3d4b6bb82ac002550434d737f27c441d439e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187112"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Общие сведения об интеграции служебной шины Azure со службой "Сетка событий"

В служебной шине Azure появилась возможность интеграции со службой "Сетка событий Azure". Благодаря этой возможности для очередей или подписок служебной шины c небольшим количеством сообщений теперь не требуется получатель для непрерывного опроса сообщений. 

Теперь, когда в подписке или в очереди есть сообщения, но получатели отсутствуют, служебная шина может выдавать события для службы "Сетка событий". Вы можете создавать подписки на службу "Сетка событий" для пространств имен служебной шины, ожидать передачи этих событий и реагировать на них, запустив получатель. Благодаря этой возможности служебную шину можно использовать в моделях реактивного программирования.

Чтобы включить эту функцию, вам понадобится следующее:

* Пространство имен служебной шины уровня "Премиум" минимум с одной очередью служебной шины или раздел служебной шины минимум с одной подпиской.
* Доступ к пространству имен служебной шины с правами участника.
* Подписка на службу "Сетка событий" для пространства имен служебной шины. Эта подписка необходима для получения из службы "Сетка событий" уведомления о том, что существуют сообщения, ожидающие действий. Типичными подписчиками могут быть компоненты Logic Apps службы приложений Azure или решения "Функции Azure" либо веб-перехватчики, связанные с веб-приложением. Следовательно, подписчики обрабатывают сообщения. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Проверка наличия доступа с правами участника

Перейдите к пространству имен служебной шины и выберите **Управление доступом (IAM)**, как показано ниже:

![1][]

### <a name="events-and-event-schemas"></a>События и схемы событий

Сейчас служебная шина отправляет события в двух сценариях:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Кроме того, служебная шина использует стандартные механизмы безопасности и [проверки подлинности](https://docs.microsoft.com/azure/event-grid/security-authentication) службы "Сетка событий".

Дополнительные сведения см. в статье [Схема событий службы "Сетка событий Azure"](https://docs.microsoft.com/azure/event-grid/event-schema).

#### <a name="active-messages-available-event"></a>Событие наличия активных сообщений

Это событие создается, если в очереди или подписке присутствуют активные сообщения, а получатели отсутствуют.

Схема для этого события выглядит следующим образом:

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

#### <a name="dead-letter-messages-available-event"></a>Событие доступных сообщений, перемещенных в очередь недоставленных

На одну очередь недоставленных сообщений приходится минимум одно событие с сообщениями, для которых отсутствуют активные получатели.

Схема для этого события выглядит следующим образом:

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

### <a name="how-many-events-are-emitted-and-how-often"></a>Как часто и в каком количестве выдаются события?

При наличии нескольких очередей и разделов или подписок в пространстве имен можно получить по крайней мере одно событие в очереди и одно в подписке. События выдаются незамедлительно, если в сущности служебной шины нет сообщений и приходит новое сообщение. Или они выдаются каждые две минуты, если служебная шина не обнаружит активного получателя. События не прерываются при просмотре сообщений.

По умолчанию служебная шина выдает события для всех сущностей в пространстве имен. Если необходимо получать события только для определенных сущностей, ознакомьтесь со следующим разделом.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Использование фильтров для ограничения источников получения событий

Если нужно получать события, например только из одной очереди или одной подписки в пространстве имен, используйте в службе "Сетка событий" фильтры *Начинается с* или *Оканчивается на*. В некоторых интерфейсах эти фильтры называются *Префикс* и *Суффикс*. Если требуется получить события для нескольких, но не всех очередей и подписок, создайте несколько подписок на службу "Сетка событий" и укажите для каждой из них фильтр.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Создание подписок на службу "Сетка событий" для пространств имен служебной шины

Вы можете создать подписки на службу "Сетка событий" для пространств имен служебной шины тремя способами:

* На [портале Azure](#portal-instructions):
* в [Azure CLI](#azure-cli-instructions);
* в [PowerShell](#powershell-instructions).

## <a name="azure-portal-instructions"></a>Инструкции по созданию подписки на портале Azure

Чтобы создать подписку на службу "Сетка событий", выполните следующие действия:
1. На портале Azure перейдите к своему пространству имен.
2. В области слева щелкните **Сетка событий**. 
3. Выберите **Подписка на события**.  

   На следующем изображении показано пространство имен с несколькими подписками на службу "Сетка событий":

   ![20][]

   На следующем изображении показано, как подписаться на функцию или веб-перехватчик без применения конкретной фильтрации:

   ![21][]

## <a name="azure-cli-instructions"></a>Инструкции по работе в Azure CLI

Для начала установите Azure CLI 2.0 или более поздней версии. [Скачайте установщик](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Нажмите клавиши **Windows+X** и откройте новую консоль PowerShell с разрешениями администратора. В качестве альтернативы можно использовать командную оболочку на портале Azure.

Выполните следующий код.

 ```azurecli-interactive
az login

az account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Инструкции по работе в среде PowerShell

Убедитесь в наличии Azure PowerShell. [Скачайте установщик](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.4.0). Нажмите клавиши **Windows+X** и откройте новую консоль PowerShell с разрешениями администратора. В качестве альтернативы можно использовать командную оболочку на портале Azure.

```PowerShell-interactive
Connect-AzureRmAccount

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
* Дополнительные сведения о [службе "Сетка событий Azure"](https://docs.microsoft.com/azure/azure-functions/).
* Дополнительные сведения о решении "Функции Azure" см. [здесь](https://docs.microsoft.com/azure/azure-functions/).
* Дополнительные сведения о [Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
* Дополнительные сведения о [служебной шине](https://docs.microsoft.com/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png

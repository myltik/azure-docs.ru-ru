---
title: "Примеры интеграции служебной шины Azure со службой \"Сетка событий\" | Документация Майкрософт"
description: "Примеры обмена сообщениями через служебную шину и интеграции со службой \"Сетка событий\""
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
ms.openlocfilehash: 3819a274696762861fbe76a9684b8495f1724f6a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Примеры интеграции служебной шины Azure со службой "Сетка событий" Azure

В этом документе представлены сведения о настройке функций Azure и приложения логики, которое получает сообщения на основе приема событий из службы "Сетка событий". В этом примере предполагается, что раздел служебной шины имеет две подписки и что подписка на службу "Сетка событий" создана таким образом, чтобы отправлять события только для одной подписки служебной шины. Затем вы отправляете сообщения в раздел служебной шины и проверяете, создается ли событие для этой подписки служебной шины. После этого функция или приложение логики получает сообщения из подписки служебной шины и выполняет его.

* Сначала убедитесь, что у вас есть все [необходимые компоненты](#prerequisites).
* Создайте [простую функцию проверки Azure](#test-function-setup) для отладки и просмотра исходного потока событий из службы "Сетка событий".  **Этот шаг необходимо выполнить независимо от выполнения шага 3 или 4.**
* Создайте [функцию Azure для приема и обработки сообщений служебной шины](#receive-messages-using-azure-function) на основе событий службы "Сетка событий".
* Используйте [приложения логики](#receive-messages-using-azure-logic-app).

## <a name="prerequisites"></a>предварительным требованиям

### <a name="service-bus-namespace"></a>Пространство имен служебной шины

Создайте пространство имен категории "Премиум" служебной шины. Создайте раздел служебной шины с двумя подписками.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Код для отправки сообщения в раздел служебной шины

Вы можете использовать любые способы отправки сообщения в раздел служебной шины. Кроме того, можно использовать приведенный ниже пример. В примере кода предполагается, что вы используете Visual Studio 2017.

Клонируйте [этот репозиторий GitHub](https://github.com/Azure/azure-service-bus/).

Перейдите в следующую папку:

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration. Затем откройте файл SBEventGridIntegration.sln.

Перейдите к проекту MessageSender и откройте файл Program.cs.

![8][]

Введите имя своего раздела, а также строку подключения и выполните консольное приложение.

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Настройка функции проверки

Прежде чем полностью выполнить сценарий, необходимо создать по меньшей мере небольшую функцию проверки, которую можно будет использовать для отладки и просмотра поступающих событий.

На портале создайте приложение-функцию Azure. Общие сведения о решении "Функции Azure" см. [здесь](https://docs.microsoft.com/en-us/azure/azure-functions/).

В созданной функции щелкните значок плюса, чтобы добавить функцию триггера HTTP.

![2][]

![3][]

Скопируйте в функцию следующий код:

```CSharp
#r "Newtonsoft.Json"
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    // parse query parameter
    var content = req.Content;

    string jsonContent = await content.ReadAsStringAsync(); 
    log.Info($"Received Event with payload: {jsonContent}");

IEnumerable<string> headerValues;
if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
{
var validationHeaderValue = headerValues.FirstOrDefault();
if(validationHeaderValue == "SubscriptionValidation")
{
var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
     var code = events[0].Data["validationCode"];
     return req.CreateResponse(HttpStatusCode.OK,
     new { validationResponse = code });
}
}

    return jsonContent == null
    ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
    : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
}

public class GridEvent
{
    public string Id { get; set; }
    public string EventType { get; set; }
    public string Subject { get; set; }
    public DateTime EventTime { get; set; }
    public Dictionary<string, string> Data { get; set; }
    public string Topic { get; set; }
}
```

Нажмите кнопку "Сохранить" и запустите функцию.

## <a name="connect-function-and-namespace-via-event-grid"></a>Соединение функции и пространства имен с помощью службы "Сетка событий"

Следующий шаг — соединение функции и пространства имен служебной шины. Для этого примера используйте портал Azure. Сведения об использовании для этого PowerShell или Azure CLI см. на странице с [основными понятиями](service-bus-to-event-grid-integration-concept.md).

Чтобы создать подписку на службу "Сетка событий Azure", перейдите к пространству имен на портале Azure и выберите колонку "Сетка событий". Щелкните "+ Подписка на события".

На следующем снимке экрана представлено пространство имен, в котором уже есть несколько подписок на службу "Сетка событий".

![20][]

На следующем снимке экрана показано, как подписаться на функцию Azure или веб-перехватчик без применения конкретной фильтрации. Добавьте соответствующий фильтр для подписки служебной шины в качестве фильтра суффикса.

![21][]

Отправьте сообщение в раздел служебной шины, как указано в предварительных требованиях, и убедитесь, что события передаются через компонент мониторинга функции Azure.

![9][]

### <a name="receive-messages-using-azure-function"></a>Получение сообщений с помощью функции Azure

В предыдущем разделе мы рассмотрели простой сценарий проверки и отладки и убедились, что события отправляются. В этой части документации рассматривается получение и обработка сообщений после получения события.

Мы выбрали такой способ добавления функции Azure, потому что функции служебной шины в рамках функций Azure сами по себе не поддерживают новую интеграцию со службой "Сетка событий".

В решении Visual Studio, которое вы открывали при выполнении предварительных требований, выберите файл ReceiveMessagesOnEvent.cs. Введите строку подключения в код.

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Затем перейдите на портал Azure и загрузите профиль публикации для функции Azure, созданной в разделе [2. Настройка функции проверки](#2-test-function-setup).

![11][]

Затем в Visual Studio щелкните правой кнопкой мыши SBEventGridIntegration и выберите "Опубликовать". Используйте ранее загруженный профиль публикации. Выберите "Импорт профиля" и нажмите кнопку "Опубликовать".

![12][]

После публикации функции Azure создайте подписку на службу "Сетка событий Azure", указав на новую функцию. Примените правильный фильтр Ends with (Заканчивается на). Это должно быть имя подписки служебной шины.

Затем отправьте сообщение в раздел служебной шины Azure, которую вы создали ранее. В журнале функций Azure на портале проверьте, передаются ли события и получены ли сообщения.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Получение сообщений с помощью приложения логики Azure

В этом разделе показано, как соединить приложение логики Azure, служебную шину Azure и службу "Сетка событий Azure".

Сначала создайте приложение логики на портале Azure и выберите сетку событий в качестве действия при запуске.

![13.][]

Первоначальное представление в конструкторе приложений логики должно выглядеть, как на снимке экрана ниже. Необходимо заменить имя ресурса собственным именем пространства имен. Кроме того, разверните дополнительные параметры и добавьте фильтр суффикса для своей подписки.

![14][]

Затем добавьте действие получения из служебной шины, чтобы получать сообщения из подписки раздела. В итоге действие должно выглядеть, как на снимке экрана ниже.

![15][]

Затем добавьте событие завершения, которое должно выглядеть следующим образом.

![16][]

Сохраните приложение логики и отправьте сообщение в раздел служебной шины, как указано в предварительных требованиях. Затем отследите выполнение приложений логики. Чтобы получить подробные сведения о выполнении, щелкните "Обзор", а затем — "Журнал запусков".

![17][]

![18][]

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о службе "Сетка событий" Azure см. [здесь](https://docs.microsoft.com/azure/event-grid/).
* Дополнительные сведения о решении "Функции Azure" см. [здесь](https://docs.microsoft.com/azure/azure-functions/).
* Узнайте больше о [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
* Дополнительные сведения о [служебной шине Azure](https://docs.microsoft.com/azure/service-bus/).

[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png

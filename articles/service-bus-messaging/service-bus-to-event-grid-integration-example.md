---
title: Примеры интеграции служебной шины Azure со службой "Сетка событий" | Документация Майкрософт
description: В статье представлены примеры интеграции обмена сообщениями через служебную шину с Сеткой событий.
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
ms.openlocfilehash: 5d0ab8cf9e87fc13b78b00dbe77ec6f9fb38c4b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189397"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Примеры интеграции служебной шины Azure со службой "Сетка событий Azure"

В этой статье вы узнаете, как настроить функции Azure и приложение логики, получающих сообщения о произошедших событиях, зарегистрированных в Сетке событий Azure. Необходимо выполнить следующие шаги.
 
* Создайте [простую функцию проверки Azure](#test-function-setup) для отладки и просмотра начального потока событий из службы "Сетка событий". Вне зависимости от того, предпримете ли вы последующие шаги, выполните этот.
* Создайте [функцию Azure для приема и обработки сообщений служебной шины Azure](#receive-messages-using-azure-function) на основе событий, зарегистрированных службой "Сетка событий".
* Используйте [компонент Logic Apps службы приложений Azure](#receive-messages-using-azure-logic-app).

В создаваемом примере предполагается, что в разделе служебной шины есть две подписки, а также, что подписка Сетки событий создается для пересылки событий только для одной подписки служебной шины. 

В рамках примера сообщения отправляются в раздел служебной шины с последующей проверкой того, были ли события сгенерированы именно для этой подписки служебной шины. Функция или приложения логики получают сообщения из подписки служебной шины и затем выполняют соответствующие действия в качестве реакции на эти сообщения.

## <a name="prerequisites"></a>предварительным требованиям
Перед началом нужно убедиться, что выполнены действия, описанные в следующих двух разделах.

### <a name="create-a-service-bus-namespace"></a>Создание пространства имен служебной шины

Создайте пространство имен категории "Премиум" служебной шины и раздел служебной шины с двумя подписками.

### <a name="send-a-message-to-the-service-bus-topic"></a>Отправка сообщения в раздел служебной шины

Для отправки сообщения в раздел служебной шины можно использовать любой метод. Код примера в конце этой операции предполагает, что вы используете Visual Studio 2017.

1. Клонируйте [репозиторий с azure-service-bus GitHub](https://github.com/Azure/azure-service-bus/).

2. В Visual Studio перейдите к папке *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration*, а затем откройте файл *SBEventGridIntegration.sln*.

3. Перейдите к проекту **MessageSender**, а затем выберите **Program.cs**.

   ![8][]

4. Введите имя раздела и строку подключения, а затем выполните следующий код из консоли.

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Создание проверочной функции

Перед запуском скрипта создайте по крайней мере небольшую проверочную функцию, которую можно использовать для отладки и наблюдения за потоком событий.

1. На портале Azure создайте приложение службы "Функции Azure". Основные сведения об этой службе см. в [документации по службе "Функции Azure"](https://docs.microsoft.com/azure/azure-functions/).

2. В только что созданной функции выберите знак плюса (+), чтобы добавить функцию триггера HTTP.

    ![2][]
    
    Откроется окно**Быстрее приступайте к работе благодаря готовой функции**.

    ![3][]

3. Нажмите кнопку **Веб-перехватчик + API**, а затем **CSharp** и **Создайте эту функцию**.
 
4. Внутри функции вставьте следующий код.

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

5. Выберите **Сохранить и запустить**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Подключение функции и пространства имен с помощью службы "Сетка событий"

В этом разделе вы свяжете функцию и пространство имен служебной шины. Для этого примера используйте портал Azure. Дополнительные сведения о выполнении этого действия с помощью PowerShell или Azure CLI см. в статье [Общие сведения об интеграции служебной шины Azure со службой "Сетка событий Azure"](service-bus-to-event-grid-integration-concept.md).

Чтобы создать подписку на службу "Сетка событий Azure", сделайте следующее:
1. На портале Azure перейдите к пространству имен и выберите на левой панели **Сетка событий**.  
    На правой панели откроется окно пространства имен с двумя подписками на Сетку событий.

    ![20][]

2. Выберите **Подписка на события**.  
    Откроется окно **Подписка на события**. На следующем рисунке представлена форма для подписки на функцию или веб-перехватчик Azure без фильтров.

    ![21][]

3. Заполните форму, как показано в поле **Фильтр суффиксов**, не забудьте указать соответствующий фильтр.

4. Нажмите кнопку **Создать**.

5. Отправьте сообщение в раздел служебной шины, как указано в разделе предварительных требований, и убедитесь, что события передаются через компонент мониторинга функции Azure.

Следующий шаг — связывание функции и пространства имен служебной шины. Для этого примера используйте портал Azure. Дополнительные сведения о выполнении этого действия с помощью PowerShell или Azure CLI см. в статье [Общие сведения об интеграции служебной шины Azure со службой "Сетка событий Azure"](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Получение сообщений с помощью службы "Функции Azure"

В предыдущем разделе мы рассмотрели простой сценарий проверки и отладки и убедились, что события отправляются. 

В этом разделе вы узнаете, как получать и обрабатывать сообщения после получения события.

В следующем примере добавим функцию Azure, так как функции служебной шины Azure в службе "Функции Azure" пока что не поддерживают интеграцию с новой Сеткой событий по умолчанию.

1. В том же решении Visual Studio, которое вы открывали для того, чтобы выполнить предварительные требования, выберите файл **ReceiveMessagesOnEvent.cs**. 

    ![10][]

2. Введите строку подключения в следующем коде.

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

3. На портале Azure скачайте профиль публикации для функции Azure, созданной в разделе "Создание проверочной функции".

    ![11][]

4. Затем в Visual Studio щелкните правой кнопкой мыши **SBEventGridIntegration** и выберите **Опубликовать**. 

5. На панели **Опубликовать** для профиля публикаций, скачанного ранее, выберите **Импортировать профиль**, а затем **Опубликовать**.

    ![12][]

6. После публикации новой функции Azure создайте подписку на службу "Сетка событий Azure", указывающую на новую функцию.  
    Примените правильный фильтр в поле **Ends with** (Заканчивается на), который является именем вашей подписки служебной шины.

7. Отправьте сообщение в раздел служебной шины Azure, созданный ранее, и просматривайте журнал службы "Функции Azure" на портале Azure, чтобы убедиться в том, что события передаются и приходят соответствующие сообщения.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Получение сообщений с помощью Logic Apps

Подключите приложение логики к служебной шине Azure и Сетке событий Azure следующим образом.

1. Сначала создайте приложение логики на портале Azure и выберите **Сетка событий** в качестве действия при запуске.

    ![13][]

    Откроется окно конструктора Logic Apps.

    ![14][]

2. Добавьте свои данные следующим образом.

    a. В поле **Имя ресурса** введите имя пространства имен. 

    Б. В разделе **Дополнительные параметры** в поле **Фильтр суффиксов** введите фильтр своей подписки.

3. Затем добавьте действие по получению сообщений служебной шины от подписки раздела.  
    Итоговое действие показано на следующем рисунке.

    ![15][]

4. Добавьте событие завершения процесса, как показано на следующем рисунке.

    ![16][]

5. Сохраните приложение логики и отправьте сообщение в раздел служебной шины, как указано в разделе предварительных требований.  
    Наблюдайте за выполнением приложения логики. Чтобы просмотреть дополнительные данные о ходе выполнения, выберите **Обзор**, а затем просмотрите данные в группе **Журнал запусков**.

    ![17][]

    ![18][]

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о службе "Сетка событий" Azure см. [здесь](https://docs.microsoft.com/azure/event-grid/).
* Дополнительные сведения о решении "Функции Azure" см. [здесь](https://docs.microsoft.com/azure/azure-functions/).
* Дополнительные сведения о компоненте Logic Apps службы приложений Azure см. [здесь](https://docs.microsoft.com/azure/logic-apps/).
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

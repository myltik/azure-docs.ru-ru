---
title: Мониторинг интерфейсов API с помощью службы управления API Azure, концентраторов событий и Runscope | Документация Майкрософт
description: Пример приложения, демонстрирующий политику регистрации в концентраторе событий путем подключения службы управления API Azure, концентраторов событий Azure и службы Runscope для регистрации и мониторинга HTTP-запросов
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 8ef8d64ba90960281faffc350821d7934e35749a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29117074"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Мониторинг API-интерфейсов с помощью управления API Azure, концентраторов событий и Runscope
[Служба управления API](api-management-key-concepts.md) предоставляет множество возможностей для улучшения обработки HTTP-запросов, адресованных API-интерфейсу HTTP. Однако запросы и ответы существуют очень недолго. Созданный запрос проходит через службу управления API на серверный API вашей службы. API обрабатывает запрос и отправляет ответ обратно потребителю API. Служба управления API частично сохраняет важные статистические данные об интерфейсах API и отображает их на панели мониторинга портала Azure, но вся остальная информация теряется безвозвратно.

Вы можете использовать политику [регистрации в концентраторах событий](../event-hubs/event-hubs-what-is-event-hubs.md), чтобы служба управления API отправляла любые нужные сведения о запросе и ответе в концентратор событий Azure. Существует ряд причин для создания событий из сообщений HTTP, отправляемых в API-интерфейсы. В частности, это происходит при создании журнала аудита обновлений, анализе использования, оповещении об исключениях или интеграции служб сторонних поставщиков.   

В этой статье мы покажем, как зафиксировать полное сообщение о запросе и ответе HTTP, как отправить это сообщение в концентратор событий, а затем передать его в сторонние службы, которые выполняют ведение журналов и мониторинг HTTP-запросов.

## <a name="why-send-from-api-management-service"></a>Почему лучше отправлять сообщения из службы управления API?
Конечно, можно создать ПО промежуточного слоя HTTP, которое может подключаться к платформам HTTP API для фиксации HTTP-запросов и ответов и их передачи в системы ведения журналов и мониторинга. Но у этого подхода есть недостатки: ПО промежуточного слоя HTTP должно интегрироваться с серверной частью API и соответствовать платформе, на которой размещен API-интерфейс. Если используется несколько интерфейсов API, ПО промежуточного слоя следует развернуть на каждом из них. Зачастую серверный API-интерфейс невозможно обновить по той или иной причине.

Служба управления API Azure предоставляет вам централизованное решение для интеграции с инфраструктурой ведения журналов, не зависящее от платформы. Также оно обеспечит хорошую масштабируемость, в том числе благодаря возможностям [георепликации](api-management-howto-deploy-multi-region.md) службы управления API Azure.

## <a name="why-send-to-an-azure-event-hub"></a>Почему лучше отправлять сообщения в концентратор событий Azure?
Вполне логичным будет вопрос, почему создаваемая политика должна использовать концентраторы событий Azure. Есть много различных служб, в которых можно регистрировать запросы. Что мешает отправлять запросы сразу конечному получателю?  Такой вариант возможен. Однако при отправке запросов на регистрацию из службы управления API следует учитывать то, как сообщения из журнала влияют на производительность API. Постепенное увеличение нагрузки можно компенсировать, увеличив количество доступных экземпляров компонентов системы или используя георепликацию. Но короткие пиковые скачки трафика могут привести к задержке запросов, если при увеличении нагрузки замедляется обработка запросов к инфраструктуре ведения журнала.

Концентраторы событий Azure рассчитаны на огромные объемы входящих данных. Их пропускная способность существенно превышает возможности большинства API-процессов по обработке HTTP-запросов. Концентратор событий выполняет роль сложного буфера между службой управления API и инфраструктурой, в которой хранятся и обрабатываются сообщения. Это гарантирует, что ограничения инфраструктуры ведения журналов не повлияют на производительность API.  

Данные, переданные в концентратор событий, сохраняются там и ожидают обработки со стороны потребителей концентратора событий. Для концентратора событий не имеет значения, как обрабатывается сообщение, — он просто гарантирует успешную доставку сообщения.     

Концентраторы событий могут выполнять потоковую передачу событий сразу нескольким группам потребителей. Это позволяет обрабатывать события различным системам. Благодаря этому можно реализовать множество сценариев интеграции без увеличения задержек на обработку запросов в службе управления API. Службе в любом случае достаточно создать только одно событие.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Политика отправки сообщений приложений и сообщений HTTP
Концентратор событий принимает данные события в виде простой строки с произвольным содержимым. Чтобы сформировать HTTP-запрос и отправить его в концентраторы событий, следует дополнить строку информацией о запросе или ответе. В таких случаях можно повторно использовать уже существующий формат, чтобы не писать новый код для синтаксического анализа. Сначала мне показалось, что для отправки HTTP-запросов и ответов подойдет формат [HAR](http://www.softwareishard.com/blog/har-12-spec/). Но этот формат оптимизирован для сохранения последовательности HTTP-запросов в формате JSON. Он содержит несколько обязательных элементов, которые создают излишние сложности при передаче HTTP-сообщения по сети.  

Еще один вариант — тип носителя `application/http` , описанный в спецификации HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Этот тип носителя имеет точно такой же формат, как и обычные HTTP-сообщения, отправляемые по сети, но позволяет разместить сообщение целиком в тексте другого HTTP-запроса. В нашем случае в тексте запроса мы и будем отправлять сообщение в концентраторы событий. В библиотеках [клиента Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) уже есть средство синтаксического анализа для этого формата, которое преобразует его в собственные объекты `HttpRequestMessage` и `HttpResponseMessage`.

Чтобы создать такое сообщение, следует воспользоваться [выражениями политики](https://msdn.microsoft.com/library/azure/dn910913.aspx) службы управления API Azure, которые используют синтаксис C#. Приведенная ниже политика будет отправлять сообщение с HTTP-запросом в концентраторы событий Azure.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Объявление политики
Некоторые элементы этого выражения политики следует разъяснить. Политика регистрации в концентраторе событий имеет атрибут logger-id, который обозначает средство ведения журнала, созданное в службе управления API. Подробные сведения о том, как в службе управления API настроить средство ведения журналов в концентраторах событий, можно найти в документе [Как регистрировать события в концентраторах событий Azure в службе управления Azure API](api-management-howto-log-event-hubs.md). Второй (необязательный) атрибут сообщает концентраторам событий, в каком разделе нужно хранить сообщение. Концентраторы событий используют разделы для поддержки масштабируемости. Для работы требуется не меньше двух разделов. Правильная последовательность доставки сообщений гарантируется только в пределах раздела. Если не указать, в каком разделе концентратор событий должен разместить сообщение, он использует их поочередно для равномерного распределения нагрузки. Но это может привести к тому, что сообщения будут обрабатываться не по порядку.  

### <a name="partitions"></a>Разделы
Чтобы обеспечить правильный порядок доставки сообщений потребителям, не теряя при этом преимущества распределения нагрузки по разделам, я решил отправлять сообщения с HTTP-запросами в один раздел, а сообщения с ответами — в другой. Это обеспечивает равномерное распределение нагрузки и одновременно гарантирует, что все запросы и ответы обрабатываются по порядку. Может случиться так, что ответ будет обработан раньше соответствующего запроса, но это не проблема. У нас есть отдельный механизм сопоставления запросов и ответов, и мы доподлинно знаем, что запросы всегда поступают раньше ответов.

### <a name="http-payloads"></a>Полезные данные HTTP
После создания `requestLine` мы проверяем, нужно ли обрезать текст запроса. Длина текста запроса ограничена 1024 символами. Это значение можно увеличить, но размер одного сообщения для концентратора событий не может превышать 256 КБ, поэтому есть вероятность, что некоторые HTTP-запросы не поместятся в одно сообщение. При ведении журнала и анализе значительный объем информации можно получить уже из строки запроса HTTP и заголовка. Кроме того, многие запросы к интерфейсам API имеют сравнительно короткий текст. Таким образом, потеря полезной информации от усечения больших фрагментов будет пренебрежимо мала по сравнению с экономией затрат на передачу, обработку и хранение полного текста запроса. И еще одно замечание об обработке текста запроса: в метод As<string>() необходимо передать значение `true`, так как мы можем читать текст запроса, но его должна прочитать еще и серверная часть API. Если передать в этот метод значение true, текст запроса сохраняется в буфере и может быть прочитан повторно. Об этом особенно важно помнить, если ваш API передает файлы большого объема или долгие опросы. В таких случаях лучше вовсе не читать текст запроса.   

### <a name="http-headers"></a>HTTP-заголовки
HTTP-заголовки можно поместить в сообщение в простом формате пар "ключ— значение". Мы решили исключить из обработки некоторые поля с секретной информацией, чтобы избежать утечки учетных данных. Для анализа ключи API и другие учетные данные, скорее всего, не потребуются. Если позднее нам нужно будет проанализировать работу конкретного пользователя или конкретного продукта, мы можем получить нужные данные из объекта `context` и добавить их в сообщение.     

### <a name="message-metadata"></a>Метаданные сообщения
В полном тексте сообщения, которое будет отправлено в концентратор событий, первая строка не является частью сообщения `application/http`. В первой строке содержатся дополнительные метаданные. Они указывают, является ли сообщение запросом или ответом, и содержат идентификатор сообщения, по которому можно сопоставить запросы и ответы. Идентификатор сообщения создается с помощью другой политики, которая выглядит следующим образом:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Мы могли бы создать сообщение с запросом, сохранить его в переменной до получения ответа, а затем отправить запрос и ответ в одном сообщении. Но раздельная отправка запроса и ответа с общим идентификатором для их сопоставления позволяет варьировать размер сообщения. Также мы получаем возможность использовать преимущества нескольких разделов с сохранением порядка сообщений. Кроме того, так мы быстрее увидим поступивший запрос в панели мониторинга журналов. Также возможны ситуации, когда допустимый ответ вообще не отправляется в концентратор событий, например в случае неустранимой ошибки запроса в службе управления API. В этом случае мы по крайней мере имеем в журнале запись о запросе.

Политика отправки HTTP-сообщения с ответом похожа на политику для запроса, поэтому полная конфигурация политики выглядит так:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

Политика `set-variable` создает значение, которое политика `log-to-eventhub` может использовать в разделах `<inbound>` и `<outbound>`.  

## <a name="receiving-events-from-event-hubs"></a>Получение событий от концентраторов событий
События из концентратора событий Azure поступают по [протоколу AMQP](http://www.amqp.org/). Команда служебной шины Microsoft предоставила в открытом доступе клиентские библиотеки, которые упрощают обработку событий. Служба поддерживает два различных подхода: *прямой потребитель* и класс `EventProcessorHost`. Примеры использования этих подходов можно найти в статье [Руководство по программированию концентраторов событий](../event-hubs/event-hubs-programming-guide.md). Вкратце рассмотрим различия между ними. `Direct Consumer` дает вам полный контроль, а `EventProcessorHost` берет часть работы на себя, но делает некоторые предположения о том, как вы обрабатываете события.  

### <a name="eventprocessorhost"></a>EventProcessorHost
В этом примере мы для простоты используем `EventProcessorHost`, хоть это и не лучший выбор в данном случае. `EventProcessorHost` выполняет самую сложную работу по управлению потоками для конкретного класса обработчика событий. Но в нашем случае мы просто преобразуем сообщение в другой формат и передаем его в другую службу, используя асинхронный метод. У нас нет необходимости обновлять общее состояние, и нет риска возникновения проблем с потоками. В большинстве случаев класс `EventProcessorHost` будет оптимальным вариантом, как минимум самым простым в использовании.     

### <a name="ieventprocessor"></a>IEventProcessor
Основной задачей при использовании `EventProcessorHost` является реализация интерфейса `IEventProcessor`, который содержит метод `ProcessEventAsync`. Суть этого метода показана ниже.

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

   foreach (EventData eventData in messages)
   {
       _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

       try
       {
           var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
           await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
       }
       catch (Exception ex)
       {
           _Logger.LogError(ex.Message);
       }
   }
    ... checkpointing code snipped ...
}
```

Список объектов EventData передается в наш метод, где мы последовательно перебираем элементы этого списка. Содержимое каждого метода преобразуется в объект HttpMessage, и этот объект передается в экземпляр IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
Экземпляр `HttpMessage` содержит три фрагмента данных:

```csharp
public class HttpMessage
{
   public Guid MessageId { get; set; }
   public bool IsRequest { get; set; }
   public HttpRequestMessage HttpRequestMessage { get; set; }
   public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

Экземпляр `HttpMessage` содержит идентификатор GUID `MessageId`, который позволяет связать HTTP-запрос с соответствующим ответом, а также логическое значение, которое сообщает, содержит ли этот объект экземпляр HttpRequestMessage и HttpResponseMessage. Я применил встроенные классы HTTP из `System.Net.Http`, что позволило воспользоваться кодом синтаксического анализа `application/http`, который включен в `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Затем экземпляр `HttpMessage` передается в реализацию `IHttpMessageProcessor`, который я создал в качестве интерфейса, чтобы отделить получение и интерпретацию события из концентратора событий Azure от фактической обработки этого события.

## <a name="forwarding-the-http-message"></a>Пересылка сообщения HTTP
Мне показалось интересным использовать в нашем примере отправку HTTP-запроса в [Runscope](http://www.runscope.com). Runscope — это облачная служба, предназначенная для отладки, регистрации и мониторинга HTTP-запросов. У службы есть бесплатный уровень, поэтому ее легко испытать в работе. Мы сможем в реальном времени отслеживать HTTP-запросы, которые проходят через службу управления API.

Реализация `IHttpMessageProcessor` выглядит следующим образом:

```csharp
public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
{
   private HttpClient _HttpClient;
   private ILogger _Logger;
   private string _BucketKey;
   public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
   {
       _HttpClient = httpClient;
       var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
       _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
       _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
       _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
       _Logger = logger;
   }

   public async Task ProcessHttpMessage(HttpMessage message)
   {
       var runscopeMessage = new RunscopeMessage()
       {
           UniqueIdentifier = message.MessageId
       };

       if (message.IsRequest)
       {
           _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
           runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
       }
       else
       {
           _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
           runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
       }

       var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
       messagesLink.BucketKey = _BucketKey;
       messagesLink.RunscopeMessage = runscopeMessage;
       var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
       _Logger.LogDebug("Request sent to Runscope");
   }
}
```

Для этого применена [существующая клиентская библиотека для службы Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha), которая упрощает передачу экземпляров `HttpRequestMessage` и `HttpResponseMessage` в соответствующую службу. Чтобы получить доступ к Runscope API, нужна учетная запись и ключ API. Инструкции по получению ключа API см. в видео [Creating Applications to Access Runscope API](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) (Создание приложений для доступа к API Runscope).

## <a name="complete-sample"></a>Полный пример
[Исходный код](https://github.com/darrelmiller/ApimEventProcessor) и тесты для этого примера доступны на сайте GitHub. Чтобы запустить этот пример, вам потребуются [служба управления API](get-started-create-service-instance.md), [подключенный к ней концентратор событий](api-management-howto-log-event-hubs.md) и [учетная запись хранения](../storage/common/storage-create-storage-account.md).   

Пример представляет собой простое консольное приложение, которое прослушивает события, поступающие от концентратора событий, преобразует их в объекты `HttpRequestMessage` и `HttpResponseMessage`, а затем пересылает их на API-интерфейс Runscope.

На анимированном изображении ниже вы видите запрос к API на портале разработчика, получение, обработку и пересылку сообщений в консольном приложении, а также запрос и ответ, отображающиеся в инспекторе трафика Runscope.

![Демонстрация передачи запроса в Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Сводка
Служба управления API Azure— это идеальное место для фиксации HTTP-трафика, поступающего на API-интерфейсы и в обратном направлении. Концентраторы событий Azure — это высокомасштабируемое недорогое решение, которое собирает информацию о трафике и передает ее в системы дополнительной обработки для регистрации, мониторинга и сложного анализа. Для подключения к сторонним системам мониторинга трафика, например Runscope, потребуется всего несколько десятков строк кода.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительная информация о концентраторах событий Azure
  * [Приступая к работе с концентраторами событий Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Прием сообщений через EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Руководство по программированию концентраторов событий](../event-hubs/event-hubs-programming-guide.md)
* Дополнительные сведения об интеграции службы управления API и концентраторов событий
  * [Как регистрировать события в концентраторах событий Azure в службе управления Azure API](api-management-howto-log-event-hubs.md)
  * [Справочник по сущности "Средство ведения журнала"](https://msdn.microsoft.com/library/azure/mt592020.aspx)
  * [Справочник по политике регистрации в концентраторе событий](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

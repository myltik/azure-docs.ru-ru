---
title: Получение событий от концентраторов событий Azure с помощью Java | Документация Майкрософт
description: Узнайте основные сведения о получении событий от концентраторов событий с помощью Java.
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: bf87bed80c142bce6229ad858a33a1c6ede63a23
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Получение событий от концентраторов событий Azure с помощью Java

Концентраторы событий — это высокомасштабируемая система, способная принимать миллионы событий в секунду, благодаря которой приложения могут обрабатывать и анализировать большие объемы данных от подключенных устройств и приложений. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в [обзоре концентраторов событий][Event Hubs overview].

В этом руководстве показано, как получать события из концентратора событий с помощью консольного приложения, написанного на языке Java.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с данным руководством вам потребуется:

* Среда разработки Java. Для этого руководства предполагается использование среды [Eclipse](https://www.eclipse.org/).
* Активная учетная запись Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure][], прежде чем начинать работу.

Код в этом руководстве основан на [коде EventProcessorSample в GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample). Изучите его, чтобы получить полное представление о рабочем приложении.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Прием сообщений через EventProcessorHost в Java

**EventProcessorHost** — это класс Java, который упрощает прием событий от концентраторов событий путем управления постоянными контрольными точками и параллельно принимает сообщения от этих концентраторов событий. С помощью класса EventProcessorHost можно разделить события между несколькими получателями, даже если они размещены на разных узлах. В этом примере показано, как использовать EventProcessorHost для одного получателя.

### <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Для использования класса EventProcessorHost необходимо настроить [учетную запись хранения Azure][Azure Storage account]:

1. Войдите на [портал Azure][Azure portal] и щелкните **+ Create a resource** (+ Создать ресурс) в левой части экрана.
2. Щелкните **Хранилище**, а затем — **Учетная запись хранения**. В окне **Создание учетной записи хранения** введите имя учетной записи хранения. Заполните остальные поля, выберите нужный регион и нажмите кнопку **Создать**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Выберите созданную учетную запись хранения и нажмите кнопку **Ключи доступа**:
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Скопируйте значение key1 во временную папку, чтобы использовать его позднее при работе с этим руководством.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Создание проекта Java с помощью EventProcessorHost

Клиентская библиотека Java для службы "Концентраторы событий" доступна для использования в проектах из [центрального репозитория Maven][Maven Package]. Ссылаться на нее можно, используя приведенное ниже объявление зависимости в файле проекта Maven. Текущая версия — 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>1.0.0</version>
</dependency>
```

Для различных типов сред сборки можно явно получить последние выпущенные JAR-файлы из [центрального репозитория Maven][Maven Package].  

1. Следующий пример сначала создает новый проект Maven для приложения консоли или оболочки в избранной среде разработки Java. Класс называется `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Создайте новый класс с именем `EventProcessorSample`с помощью следующего кода: Замените значения заполнителей значениями, которые использовались при создании концентратора событий и учетной записи хранения:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Создайте еще один класс с именем `EventProcessor` при помощи следующего кода:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. In a real implementation, this
        // is the place to do initialization so that events can be processed when they arrive, such as opening a database
        // connection.
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. The reason argument indicates whether the shut down
        // is because another host has stolen the lease for this partition or due to error or host shutdown. In a real implementation,
        // this is the place to do cleanup for resources that were opened in onOpen.
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        // It is NOT called for exceptions thrown out of onOpen/onClose/onEvents. EventProcessorHost is responsible for recovering from
        // the error, if possible, or shutting the event processor down if not, in which case there will be a call to onClose. The
        // notification provided to onError is primarily informational.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. The maximum number of events in a batch
        // can be controlled via EventProcessorOptions. Also, if the "invoke processor after receive timeout" option is set to true,
        // this method will be called with null when a receive timeout occurs.
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                // It is important to have a try-catch around the processing of each event. Throwing out of onEvents deprives
                // you of the chance to process any remaining events in the batch. 
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. Checkpointing is usually not a fast operation, so there is a tradeoff
                    // between checkpointing frequently (to minimize the number of events that will be reprocessed after a crash, or
                    // if the partition lease is stolen) and checkpointing infrequently (to reduce the impact on event processing
                    // performance). Checkpointing every five events is an arbitrary choice for this sample.
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

> [!NOTE]
> В данном учебнике используется один экземпляр EventProcessorHost. Чтобы увеличить пропускную способность, рекомендуется запустить несколько экземпляров EventProcessorHost (желательно на отдельных компьютерах).  Это также обеспечивает избыточность. В этом случае различные экземпляры автоматически координируются друг с другом для распределения нагрузки полученных событий. Если каждый из нескольких получателей должен обрабатывать *все* события, то необходимо использовать понятие **ConsumerGroup** . При получении события от разных компьютеров может оказаться полезным указать имена экземпляров EventProcessorHost в компьютерах (или ролях), где они развернуты.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Event Hubs overview](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[бесплатную учетную запись Azure]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
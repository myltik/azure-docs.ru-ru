<properties
	pageTitle="Обработка сообщений, отправляемых с устройства центра IoT в облако (Java) | Microsoft Azure"
	description="Этот учебник по Java поможет изучить полезные схемы обработки сообщений, отправляемых с устройства центра IoT в облако."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# Учебник: как обрабатывать сообщения, отправляемые с устройства центра IoT в облако, с помощью Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## Введение

Центр IoT Azure — полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью приложения. В других руководствах ([Приступая к работе с центром IoT] и [Отправка сообщений с облака на устройства в центре IoT][lnk-c2d]) описаны базовые функции центра IoT по отправке сообщений между устройством и облаком.

Этот учебник основан на коде, показанном в учебнике [Приступая к работе с центром IoT]. В нем демонстрируется два масштабируемых шаблона, которые можно использовать для обработки сообщений, отправляемых с устройства в облако:

- Надежное хранилище сообщений, отправляемых с устройства в облако, в [хранилище BLOB-объектов Azure]. Распространенный сценарий, подразумевающий реализацию "*холодного*" анализа, где телеметрические данные хранятся в больших двоичных объектах, которые используются в качестве входных данных для процедур анализа. Эти процедуры могут управляться такими инструментами, как [фабрика данных Azure] или стек [HDInsight (Hadoop)].

- Надежная обработка *интерактивных* сообщений, отправляемых с устройства в облако. Сообщения, отправляемые с устройства в облако, являются интерактивными, если они немедленно инициируют набор действий в серверной части приложения. Например, устройство может отправить аварийный сигнал, который активирует вставку билета в системе CRM. В отличие от сообщений *точек данных*, которые просто поступают в модуль аналитики. Например, телеметрическое сообщение о температуре с устройства, которое должно быть сохранено для последующего анализа, является сообщением точки данных.

Так как для получения сообщений, отправляемых с устройства в облако, центр IoT предоставляет конечную точку, совместимую с [концентраторами событий][lnk-event-hubs], в этом учебнике используется экземпляр класса [EventProcessorHost]. Этот экземпляр:

* надежно хранит сообщения *точек данных* в хранилище BLOB-объектов Azure;
* перенаправляет *интерактивные* сообщения, отправляемые с устройства в облако, в [очередь служебной шины Azure] для немедленной обработки.

Служебная шина помогает обеспечить надежную обработку интерактивных сообщений, так как она обеспечивает контрольные точки для каждого сообщения и дедупликацию на основе временных интервалов.

> [AZURE.NOTE] Экземпляр **EventProcessorHost** — только один из способов обработки интерактивных сообщений. Другие варианты включают в себя [Azure Service Fabric][lnk-service-fabric] и [Azure Stream Analytics][lnk-stream-analytics].

В конце этого учебника у вас будет три консольных приложения Java:

* **simulated-device**, измененная версия приложения, созданного в учебнике [Приступая к работе с центром IoT]. Приложение отправляет сообщения точек данных с устройства в облако один раз в секунду и интерактивные сообщения с устройства в облако каждые 10 секунд. Это приложение использует протокол AMQPS для обмена данными с центром IoT.
* **process-d2c-messages** использует класс [EventProcessorHost] для получения сообщений от конечной точки, совместимой с концентраторами событий. Затем приложение надежно сохраняет сообщения точек данных в большом двоичном объекте Azure и перенаправляет интерактивные сообщения в очередь служебной шины.
* **process-interactive-messages** удаляет интерактивные сообщения из очереди служебной шины.

> [AZURE.NOTE] Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и JavaScript). Указания по замене виртуального устройства в этом учебнике физическим устройством, а также по подключению устройств к центру IoT Azure в целом см. в [Центре разработчиков для Azure IoT].

Содержимое данного учебника можно применить к другим способам использования сообщений, совместимых с концентраторами событий, например в проектах [HDInsight (Hadoop)]. Дополнительные сведения см. в [руководстве разработчика по центру Azure IoT — в разделе "Отправка сообщений с устройства в облако"].

Для работы с этим учебником требуется:

+ Полная рабочая версия приведена в учебнике [Приступая к работе с центром Azure IoT с использованием Java].

+ Java SE 8. <br/> В статье [Подготовка среды разработки][lnk-dev-setup] описывается, как установить Java для целей этого руководства в ОС Windows или Linux.

+ Maven 3. <br/> В статье [Подготовка среды разработки][lnk-dev-setup] описывается, как установить Maven для целей этого руководства в ОС Windows или Linux.

+ Активная учетная запись Azure. <br/>Если у вас нет подписки Azure, то всего за несколько минут можно создать [бесплатную учетную запись](https://azure.microsoft.com/free/).

Кроме того, у вас должны быть базовые знания о [службе хранилища Azure] и [служебной шине Azure].


## Отправка интерактивных сообщений из имитации устройства

В этом разделе вы измените приложение имитации устройства, созданное в руководстве [Приступая к работе с центром IoT], чтобы отправлять в центр IoT интерактивные сообщения от устройства в облако.

1. Откройте в текстовом редакторе файл simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java. Этот файл содержит код приложения **simulated-device**, созданного при изучении учебника [Приступая к работе с центром IoT].

2. Добавьте приведенный ниже вложенный класс в класс **App**.

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Этот класс похож на класс **MessageSender** в проекте **simulated-device**. Единственные отличия — системное свойство **MessageId** (настраивается сейчас) и пользовательское свойство **messageType**. Этот код сохраняет идентификатор UUID в свойстве **MessageId**. Служебная шина может использовать его для дедупликации получаемых сообщений. В примере используется свойство **messageType**, чтобы отличить интерактивные сообщения от сообщений точки данных. Приложение передает эти сведения в свойствах сообщения, а не в его основной части, чтобы обработчику событий не требовалось десериализовывать сообщение для маршрутизации.

    > [AZURE.NOTE] Очень важно создать **MessageId**, используемый в коде устройства для дедупликации интерактивных сообщений. Прерывистая сетевая связь или другие сбои могут привести к многократной повторной передаче одного сообщения от этого устройства. Вместо идентификатора UUID можно также использовать семантический идентификатор сообщения, например хэш релевантных полей данных этого сообщения.

3. Измените метод **main** для отправки интерактивных сообщений и сообщений точек данных, как показано в следующем фрагменте кода.

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Сохраните и закройте файл simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

    > [AZURE.NOTE] Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политику повтора (например, экспоненциальную задержку), как предлагается в статье MSDN [Transient Fault Handling] (Обработка временного сбоя).

5. Чтобы создать приложение **simulated-device** с помощью Maven, выполните в командной строке в папке simulated-device следующую команду.

    ```
    mvn clean package -DskipTests
    ```

## Обработка сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Java, которое обрабатывает сообщения, передаваемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий] конечную точку, с помощью которой приложение считывает сообщения, отправляемые с устройств в облако. В этом руководстве для обработки таких сообщений в консольном приложении используется класс [EventProcessorHost]. Дополнительные сведения о способах обработки сообщений из концентраторов событий см. в руководстве [Приступая к работе с концентраторами событий].

Основная сложность, с которой вы сталкиваетесь при реализации надежного хранилища сообщений точки данных или перенаправления интерактивных сообщений, заключается в том, что контроль хода выполнения обработки событий основан на потребителе сообщений. Кроме того, чтобы добиться высокой пропускной способности, при считывании из концентраторов событий необходимо установить контрольные точки в больших пакетах. При таком подходе создается возможность повторной обработки большого количества сообщений в случае возникновения сбоя и возвращения к предыдущей контрольной точке. Из этого руководства вы узнаете, как синхронизировать операции записи в службу хранилища Azure и периоды дедупликации служебной шины с помощью контрольных точек **EventProcessorHost**.

Чтобы обеспечить надежную запись сообщений в службу хранилища Azure, в примере используется функция фиксации отдельных блоков [блочных BLOB-объектов][Azure Block Blobs]. Обработчик событий накапливает сообщения в памяти, пока не наступает время создать контрольную точку. Например, после того, как максимальный размер блока в накопленном буфере сообщений достигнет 4 МБ, или после того, как истечет период дедупликации служебной шины. Затем перед установкой контрольной точки в большом двоичном объекте с помощью кода фиксируется новый блок.

Обработчик событий использует смещения сообщений концентраторов событий в качестве идентификаторов блоков. Этот механизм позволяет обработчику событий выполнить проверку дедупликации перед фиксацией нового блока в хранилище, устраняя вероятность сбоя между фиксацией блока и контрольной точкой.

> [AZURE.NOTE] В этом руководстве используется одна учетная запись хранения для записи всех сообщений, полученных из центра IoT. Чтобы определить, требуется ли в вашем решении использовать несколько учетных записей хранения Azure, см. статью [Целевые показатели масштабируемости и производительности службы хранилища Azure].

Чтобы избежать повторений при обработке интерактивных сообщений, в приложении используется функция дедупликации служебной шины. Виртуальное устройство присваивает каждому интерактивному сообщению уникальный идентификатор **MessageId**. Благодаря этому служебная шина предотвращает доставку получателям двух сообщений с одним и тем же **MessageId** в течение указанного периода дедупликации. Такая дедупликация совместно с семантикой завершения каждого сообщения, предоставляемой очередями служебной шины, значительно упрощают реализацию надежной обработки интерактивных сообщений.

Чтобы предотвратить повторную отправку сообщений до или после дедупликации, код синхронизирует механизм контрольных точек **EventProcessorHost** с периодом дедупликации очереди служебной шины. Эта синхронизация достигается путем хотя бы однократного принудительного создания контрольной точки при каждом окончании периода дедупликации (в данном учебнике этот период составляет один час).

> [AZURE.NOTE] В данном руководстве для обработки всех интерактивных сообщений, полученных из центра IoT, используется одна секционированная очередь служебной шины. Дополнительные сведения об использовании очередей служебной шины в соответствии с требованиями к масштабируемости решения см. в [документации по служебной шине Azure].

### Подготовка учетной записи хранения Azure и очереди служебной шины

Чтобы использовать класс [EventProcessorHost], вам понадобится учетная запись хранения Azure, в которую **EventProcessorHost** будет записывать сведения о контрольных точках. Можно использовать существующую учетную запись хранения или создать новую, следуя инструкциям в статье [Об учетных записях хранения Azure]. Запишите строку подключения учетной записи хранения.

> [AZURE.NOTE] Копируя и вставляя строку подключения к учетной записи хранения, будьте внимательны, чтобы не к имени не добавились пробелы.

Требуется также реализовать надежную обработку интерактивных сообщений в очереди служебной шины. Можно программно создать очередь с периодом дедупликации в один час, как описано в статье об [использовании очереди служебной шины][Service Bus queue]. Кроме того, можно воспользоваться [классическим порталом Azure][lnk-classic-portal], где нужно сделать следующее:

1. В левом нижнем углу нажмите кнопку **Создать**. Затем последовательно выберите **Службы приложений** > ** Служебная шина** > **Очередь** > **Настраиваемое создание**. Введите имя **d2ctutorial**, выберите регион, а затем выберите имеющееся пространство имен или создайте новое. Запишите имя пространства имен, оно понадобится позднее в этом учебнике. На следующей странице установите флажок **Включить обнаружение дубликатов** и для параметра **Временное окно журнала обнаружения повторений** установите значение в один час. Чтобы сохранить конфигурацию очереди, щелкните значок галочки в правом нижнем углу.

    ![Создание очереди на портале Azure][30]

2. В списке очередей служебной шины щелкните **d2ctutorial**, а затем нажмите кнопку **Настроить**. Создайте две политики общего доступа: **send** с разрешениями **Отправка** и **listen** с разрешениями **Прослушивание**. Запишите значения **первичного ключа** для обеих политик, они понадобятся позже в этом учебнике. По завершении нажмите кнопку **Сохранить** в нижней части окна.

    ![Настройка очереди на портале Azure][31]

### Создание обработчика событий

В этом разделе вы создадите приложение Java для обработки сообщений из конечной точки, совместимой с концентраторами событий.

Первой задачей является добавление проекта Maven с именем **process-d2c-messages**, который получает сообщения с устройства в облако от конечной точки центра IoT, совместимой с концентраторами событий, и направляет эти сообщения в другие серверные службы.

1. В папке iot-java-get-started, созданной при изучении учебника [Приступая к работе с центром IoT], создайте проект Maven с именем **process-d2c-messages**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке process-d2c-messages.

3. Откройте в текстовом редакторе файл pom.xml из папки process-d2c-messages и добавьте зависимости, приведенные ниже, в узел **dependencies**. Эти зависимости позволяют использовать пакеты azure-eventhubs, azure-eventhubs-eph и azure-servicebus в приложении для взаимодействия с центром IoT и очередью служебной шины.

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Сохраните и закройте файл pom.xml.

Следующей задачей является добавление в проект класса **ErrorNotificationHandler**.

1. С помощью текстового редактора создайте файл process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\ErrorNotificationHandler.java. Добавьте в него приведенный ниже код для отображения сообщений об ошибках из экземпляра **EventProcesssorHost**.

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Сохраните и закройте файл ErrorNotificationHandler.java.

Теперь можно добавить класс, реализующий интерфейс **IEventProcessor**. Класс **EventProcessorHost** вызывается для обработки сообщений из центра IoT, отправляемых с устройства в облако. В этом классе код реализует логику для надежного хранения сообщений в контейнере больших двоичных объектов и перенаправляет интерактивные сообщения в очередь служебной шины.

Метод **OnEvents** задает переменную **latestEventData**, которая отслеживает смещение и порядковый номер последнего сообщения, прочитанного этим обработчиком событий. Помните, что каждый процессор отвечает за один раздел. Затем метод **onEvents** получает пакет сообщений из центра IoT и обрабатывает их следующим образом: интерактивные сообщения отправляются в очередь служебной шины, а сообщения точки данных добавляются в буфер памяти **toAppend**. Если буфер памяти достигает предельного размера блока в 4 МБ или заканчивается период дедупликации (в данном учебнике — через один час после создания последней контрольной точки), то метод активирует создание контрольной точки.

Сначала метод **AppendAndCheckPoint** создает идентификатор **blockId** для блока, добавляемого в большой двоичный объект. В службе хранилища Azure все идентификаторы блоков должны быть одной длины, поэтому смещение дополняется начальными нулями. Затем, если блок с таким идентификатором уже находится в большом двоичном объекте, то метод перезаписывает его текущим содержимым буфера.

> [AZURE.NOTE] В целях упрощения кода в этом руководстве для хранения сообщений используется по одному файлу большого двоичного объекта для каждого раздела. В реальном решении ротация файлов реализуется за счет создания дополнительных файлов после истечения заданного промежутка времени или при достижении определенного размера. Помните, что блочный BLOB-объект Azure может содержать до 195 ГБ данных.

Следующая задача — реализовать интерфейс **IEventProcessor**.

1. С помощью текстового редактора создайте файл process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\EventProcessor.java.

2. Добавьте приведенные ниже определения import и определение класса в файл EventProcessor.java. Класс **EventProcessor** реализует интерфейс **IEventProcessor**, определяющий поведение клиента концентраторов событий.

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Добавьте в класс **EventProcessor** следующие методы для реализации интерфейса **IEventProcessor**.

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Добавьте в класс **EventProcessor** приведенные ниже переменные уровня класса.

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Добавьте в класс **EventProcessor** метод **AppendAndCheckPoint** со следующей сигнатурой.

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Добавьте приведенный ниже код в метод **AppendAndCheckPoint** для получения текущих смещения и порядкового номера сообщения в секции.

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. В методе **AppendAndCheckPoint** используйте значение текущего смещения, чтобы создать экземпляр **BlockEntry** для следующего блока, сохраняемого в большом двоичном объекте.

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. В методе **AppendAndCheckPoint** передайте последний набор сообщений в блочный BLOB-объект и получите текущий список блоков.

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. В методе **AppendAndCheckPoint** либо создайте начальный блок в новом большом двоичном объекте, либо добавьте блок в существующий большой двоичный объект.

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Наконец, в методе **AppendAndCheckPoint** создайте контрольную точку для секции и подготовьте к сохранению следующий блок сообщений.

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. В метод **onEvents** добавьте приведенный ниже код, чтобы получать сообщения из конечной точки центра IoT и пересылать интерактивные сообщения в очередь служебной шины. Затем укажите вызов метода **AppendAndCheckPoint** в случае, если блок заполнен или истекло время ожидания.

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Наконец, в метод **onEvents** добавьте предложение else if для вызова **AppendAndCheckPoint**, если по истечении времени ожидания из центра IoT не поступило сообщений.

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Сохраните изменения в файле EventProcessor.java.

Завершающая задача в проекте **process-d2c-messages** — добавить код для метода **main**, создающего экземпляр **EventProcessorHost**.

1. Откройте в текстовом редакторе файл process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Добавьте в файл следующие инструкции **import**.

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Добавьте приведенную ниже переменную уровня класса в класс **App**. Замените **{yourstorageaccountconnectionstring}** строкой подключения к учетной записи хранения Azure, которую вы записали ранее в разделе [Подготовка учетной записи хранения Azure и очереди служебной шины](#provision-an-azure-storage-account-and-a-service-bus-queue).

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Добавьте приведенные ниже переменные уровня класса в класс **App** и замените **{yourservicebusnamespace}** именем пространства имен служебной шины, а **{yourservicebussendkey}** — своим ключом **send** очереди. Ранее вы записали значения пространства имен и ключа **listen**, когда изучали раздел [Подготовка учетной записи хранения Azure и очереди служебной шины](#provision-an-azure-storage-account-and-a-service-bus-queue).

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Добавьте приведенные ниже переменные уровня класса в класс **App**. Замените **{youreventhubcompatibleendpoint}** именем конечной точки, совместимой с концентратором событий. Имя конечной точки имеет вид **ihs…<пространство\_имен>**, поэтому следует удалить префикс **sb://** и суффикс **.servicebus.windows.net/**. Замените **{youreventhubcompatiblename}** именем, совместимым с концентратором событий. Замените **{youriothubkey}** ключом **iothubowner**. Вы записали эти значения, изучая раздел [Создание центра IoT][lnk-create-an-iot-hub] учебника *Приступая к работе с центром Azure IoT с использованием Java*.

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Измените сигнатуру метода **main**, как показано ниже.

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Добавьте в метод **main** приведенный ниже код для получения ссылки на контейнер больших двоичных объектов, в котором хранятся сообщения.

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Добавьте в метод **main** приведенный ниже код для получения ссылки на службу служебной шины.

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. В методе **main** настройте и создайте экземпляр **EventProcessorHost**. Параметр **SetInvokeProcessorAfterReceiveTimeout** гарантирует, что экземпляр **EventProcessorHost** вызывает метод **onEvents** в интерфейсе **IEventProcessor** даже в том случае, если нет сообщений для обработки. После этого метод **OnEvents** всегда вызывает метод **AppendAndCheckPoint**, если время ожидания истекло.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. В методе **main** зарегистрируйте реализацию **IEventProcessor** в экземпляре **EventProcessorHost**.

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Наконец, добавьте в метод **main** логику для завершения работы экземпляра **EventProcessorHost**.

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Сохраните и закройте файл process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

13. Чтобы создать приложение **process-d2c-messages** с помощью Maven, выполните в командной строке в папке process-d2c-messages следующую команду.

    ```
    mvn clean package -DskipTests
    ```

## Прием интерактивных сообщений

В этом разделе вам предстоит создать консольное приложение Java, которое принимает интерактивные сообщения из очереди служебной шины.

Первой задачей является добавление проекта Maven с именем **process-interactive-messages**, который получает сообщения, отправленные в очередь Service Bus из экземпляров **EventProcessor**.

1. В папке iot-java-get-started, созданной при изучении учебника [Приступая к работе с центром IoT], создайте проект Maven с именем **process-interactive-messages**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке process-interactive-messages.

3. Откройте в текстовом редакторе файл pom.xml из папки process-interactive-messages и добавьте зависимость, приведенную ниже, в узел **dependencies**. Эта зависимость позволяет использовать в приложении пакет azure-servicebus для взаимодействия с очередью служебной шины.

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Сохраните и закройте файл pom.xml.

Следующая задача — добавить код для извлечения сообщений из очереди служебной шины.

1. Откройте в текстовом редакторе файл process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Добавьте в него следующие инструкции `import`.

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Добавьте приведенные ниже переменные уровня класса в класс **App** и замените **{yourservicebusnamespace}** именем пространства имен служебной шины, а **{yourservicebuslistenkey}** — своим ключом **listen** очереди. Ранее вы записали значения пространства имен и ключа **listen**, когда изучали раздел [Подготовка учетной записи хранения Azure и очереди служебной шины](#provision-an-azure-storage-account-and-a-service-bus-queue).

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Добавьте в класс **App** приведенный ниже вложенный класс для получения сообщений из очереди.

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Измените сигнатуру метода **main**, как показано ниже.

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Добавьте в метод **main** приведенный ниже код, запускающий прослушивание новых сообщений.

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Сохраните и закройте файл process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

8. Чтобы создать приложение **process-interactive-messages** с помощью Maven, выполните в командной строке в папке process-interactive-messages следующую команду.

    ```
    mvn clean package -DskipTests
    ```

## Запуск приложений

Теперь все готово к запуску этих трех приложений.

1.	Чтобы запустить приложение **process-interactive-messages**, в командной строке или оболочке перейдите к папке process-interactive-messages и выполните следующую команду.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Запуск приложения process-interactive-messages][processinteractive]

2.	Чтобы запустить приложение **process-d2c-messages**, в командной строке или оболочке перейдите к папке process-d2c-messages и выполните следующую команду.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Запуск приложения process-d2c-messages][processd2c]

3.	Чтобы запустить приложение **simulated-device**, в командной строке или оболочке перейдите к папке simulated-device и выполните следующую команду.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Запуск приложения simulated-device][simulateddevice]

> [AZURE.NOTE] Чтобы увидеть обновления в файле большого двоичного объекта, может потребоваться уменьшить значение константы **MAX\_BLOCK\_SIZE** в классе **StoreEventProcessor** (например, до **1024**). Это удобное изменение, так как на достижение предельного размера блока с данными, отправляемыми имитацией устройства, уходит некоторое время. Если указать меньший размер блока, то создание и обновление большого двоичного объекта выполняется быстрее. Тем не менее, наличие блоков большего размера делает приложение более масштабируемым.

## Дальнейшие действия

В этом руководстве мы рассмотрели надежную обработку сообщений точек данных и интерактивных сообщений, отправляемых с устройства в облако, с помощью класса [EventProcessorHost].

В [руководстве по отправке сообщений из облака на устройства с помощью центра IoT и .Net][lnk-c2d] описано, как отправлять сообщения на устройства из серверной части.

Примеры комплексных решений, в которых используется центр IoT, см. в [документации по Azure IoT Suite][lnk-suite].

Дополнительные сведения о разработке решений с помощью центра IoT см. в [руководстве разработчика по центру IoT].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

.<!-- Links -->

[хранилище BLOB-объектов Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[фабрика данных Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[очередь служебной шины Azure]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[руководстве разработчика по центру Azure IoT — в разделе "Отправка сообщений с устройства в облако"]: iot-hub-devguide.md#d2c

[службе хранилища Azure]: https://azure.microsoft.com/documentation/services/storage/
[документации по служебной шине Azure]: https://azure.microsoft.com/documentation/services/service-bus/
[служебной шине Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[руководстве разработчика по центру IoT]: iot-hub-devguide.md
[Приступая к работе с центром Azure IoT с использованием Java]: iot-hub-java-java-getstarted.md
[Приступая к работе с центром IoT]: iot-hub-java-java-getstarted.md
[Центре разработчиков для Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Об учетных записях хранения Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Приступая к работе с концентраторами событий]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Целевые показатели масштабируемости и производительности службы хранилища Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[концентраторами событий]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Event Hubs Programming Guide]: https://github.com/Azure/azure-event-hubs/blob/master/java/readme.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

<!---HONumber=AcomDC_0928_2016-->
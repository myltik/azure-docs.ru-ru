<properties
   pageTitle="Секционирование служб в Service Fabric | Microsoft Azure"
   description="Описывает процесс выполнения разделения служб в структуре служб"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# Секционирование служб Reliable Services в Service Fabric
В этой статье рассматриваются основные понятия, связанные с секционированием служб Reliable Services в инфраструктуре Service Fabric. Исходный код, который используется в этой статье, также можно найти на [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/AlphabetPartitions).

## Что такое секционирование
Секционирование не является уникальной функцией Service Fabric. На самом деле оно является основным шаблоном для создания масштабируемых служб. В более широком смысле понятие секционирования означает деление состояния (данных) и разбивку на более мелкие доступные единицы для повышения производительности и улучшения масштабируемости. Хорошо известной формой секционирования является [секционирование данных][wikipartition]. Иначе оно называется сегментированием.


### Секционирование служб без отслеживания состояния в Service Fabric
В службах без отслеживания состояния раздел можно описать, как логическую единицу, содержащую один или несколько экземпляров службы. На рис. 1 показана служба без отслеживания состояния с пятью экземплярами, распределенными в кластере, с одним разделом.

![Служба без отслеживания состояния](./media/service-fabric-concepts-partitioning/statelessservice.png)

Есть два типа решений служб без отслеживания состояния. К первому относятся службы, состояние которых сохраняется на внешних ресурсах, например в базе данных SQL Azure (например, веб-сайт, который сохраняет данные и сведения о сеансе). Второй тип служб предназначен только для вычислений (например, калькулятор или создание эскизов изображений). Последние службы не имеют постоянного состояния. Секционирование служб без отслеживания состояния выполняется очень редко. Масштабируемость и доступность обычно достигаются путем добавления дополнительных экземпляров. Секционирование уместно лишь при необходимости выполнения особых запросов маршрутизации. Примером может быть случай, когда пользователи с определенным диапазоном идентификаторов должны обслуживаться только определенным экземпляром службы. Еще один случай, когда может потребоваться секционировать службу без отслеживания состояния: у вас есть по-настоящему секционированный сервер, например сегментированная база данных SQL, и вы хотите управлять записью экземпляров службы в определенные сегменты базы данных или выполнять другие подготовительные задачи в рамках службы без отслеживания состояния, для которых требуются те же сведения о секционировании, которые используются на сервере. Подобные сценарии также можно решить другими способами, не требующими секционирования службы.

Оставшаяся часть этого пошагового руководства посвящена службам с отслеживанием состояния.

### Секционирование служб с отслеживанием состояния в Service Fabric
Service Fabric упрощает процесс разработки масштабируемых служб с отслеживанием состояния, предоставляя лучший способ секционирования состояния (данных). По сути, раздел службы с отслеживанием состояния является единицей масштабирования высокой надежности благодаря [репликам](service-fabric-availability-services.md), которые распространяются и распределяются среди узлов в кластере. Секционирование в контексте служб с отслеживанием состояния Service Fabric — это процесс назначения раздела определенной службы (как уже говорилось ранее, раздел представляет собой набор [реплик](service-fabric-availability-services.md)) ответственной за часть полного состояния службы. Замечательной особенностью Service Fabric является размещение разделов на разных узлах, что позволяет им увеличиваться до предела ресурсов узла. По мере увеличения объема данных разделы увеличиваются, и Service Fabric перераспределяет их между узлами, таким образом обеспечивая беспрерывное эффективное использование ресурсов оборудования.

Рассмотрим это все на примере. Предположим, у нас есть кластер из пяти узлов со службой, настроенной на использование десяти разделов и три целевые реплики. В этом случае Service Fabric распределит и распространит реплики в кластере, и вы в результате будете иметь по две основные [реплики](service-fabric-availability-services.md) на каждом узле. Если необходимо расширить кластер до десяти узлов, Service Fabric распределит основные [реплики](service-fabric-availability-services.md) среди них. Аналогичным образом при сокращении до пяти узлов все реплики будут перераспределены между пятью узлами.

На рис. 2 показано распределение десяти разделов до и после масштабирования в кластере.

![Служба с отслеживанием состояния](./media/service-fabric-concepts-partitioning/scaledcluster.png)

Так мы получили увеличение масштаба, при котором запросы от клиентов распределяются между компьютерами. В результате повышается общая производительность приложения, а конкуренция за доступ к блокам данных уменьшается.

## Планирование секционирования
Перед реализацией службы следует выбрать стратегию секционирования, необходимую для горизонтального масштабирования. Это можно сделать различными способами, но все они сфокусированы на назначении приложения. В этой статье мы обратим внимание на некоторые наиболее важные аспекты.

Сначала следует рассмотреть структуру состояния, которое нужно секционировать.

Ниже приведен простой пример. Нам нужно создать службу для опроса, который будет охватывать всю страну. Можно создать по разделу для каждого города в области и сохранять голос каждого человека в разделе, соответствующем его городу. На рис. 3 приведен набор людей и городов, в которых они находятся.

![Простой раздел](./media/service-fabric-concepts-partitioning/cities.png)

Так как население городов значительно отличается, в итоге может оказаться, что некоторые разделы содержат большое количество данных (например, Сиэтл), а другие — очень мало (например, Кирклэнд). Каковы последствия наличия разделов с неравномерным состоянием?

Внимательнее присмотритесь к примеру выше, и вы увидите, что раздел, который содержит голоса для Сиэтла, получает большую нагрузку, чем раздел для Кирклэнда. По умолчанию Service Fabric гарантирует наличие одинакового количества основных и вторичных реплик на каждом узле. Поэтому в итоге одни узлы могут быть с репликами с большими объемами трафика, а другие — с меньшими. Желательно избегать таких чрезмерно активных и пассивных участков в кластере.

Для этого во время секционирования следует выполнить два действия.

- Старайтесь секционировать состояние так, чтобы оно равномерно распределялось между всеми разделами.
- [Следите за показателями каждой реплики в службе](service-fabric-resource-balancer-dynamic-load-reporting.md). В Service Fabric есть возможность получать отчеты по таким показателям, как объем памяти или число записей, для службы. На основе полученных показателей Service Fabric определяет, какие разделы загружены больше, чем другие. Затем платформа перераспределяет кластер, перемещая реплики на более подходящие узлы.

Иногда неизвестно, какой объем данных будет находиться в определенном разделе. Поэтому рекомендуем выполнить оба действия. Прежде всего, выберите стратегию секционирования, которая будет равномерно распределять данные в разделах, а затем внедрите отчетность загрузки. Первый способ предотвращает ситуации, описанные в примере с опросом, тогда как второй помогает сгладить временную разницу в доступе или загрузке.

Другим аспектом планирования секционирования является выбор правильного количества разделов. С помощью Service Fabric вы можете смело начинать работу с большим количеством разделов, чем требуется для вашего сценария. По сути, задать максимальное количество разделов — это вполне допустимый подход.

В редких случаях вам может понадобится больше разделов, чем выбрано изначально. После секционирования количество разделов нельзя изменить. Зато к ним можно применить дополнительные приемы. Например, вы можете создать новый экземпляр службы такого же типа и реализовать логику на стороне клиента, направляющую запросы к правильному экземпляру службы на основе знаний клиентской стороны, которые должен поддерживать код клиента.

Другим аспектом планирования секционирования являются доступные ресурсы машин. Так как к состоянию необходимо получать доступ и хранить его, существуют следующие ограничения:

- Ограничения пропускной способности сети.
- Ограничения системной памяти.
- Ограничения дискового пространства.

Что произойдет, если во время работы кластера возникнут проблемы ограничения ресурсов? Ответ прост: кластеры можно расширить в соответствии с новыми требованиями.

[Руководство по планированию емкости](service-fabric-capacity-planning.md) поможет определить требуемое количество узлов кластера.

## Секционирование
В этом разделе описывается, как секционировать службу.

Во-первых, в Service Fabric можно выбрать одну из трех возможных схем секционирования.

- Секционирование по диапазонам значений (также известное как UniformInt64Partition).
- Секционирование по именам. Как правило, данные приложений, которые используют эту модель, можно поместить в контейнер в рамках ограниченного набора данных. Некоторые наиболее распространенные примеры полей данных, которые используются в качестве ключей для секционирования по именам, следующие: области, почтовые индексы, группы клиентов и другие структуры бизнеса.
- Одноэлементное секционирование. Одноэлементное секционирование обычно используется, если служба не требует дополнительной маршрутизации. Например, службы без отслеживания состояния по умолчанию используют эту схему секционирования.

Схемы секционирования по именам и одноэлементного секционирования являются особыми формами секционирования по диапазонам. По умолчанию шаблоны Visual Studio для Service Fabric используют секционирование по диапазонам, так как оно является наиболее распространенным и практичным. В оставшейся части этой статьи рассматривается схема секционирования по диапазонам.

### Схема секционирования по диапазонам
Она используется для указания целочисленного диапазона (определенного низким и высоким ключом) и количества разделов (n). Создается n разделов, каждый из которых отвечает за неперекрывающийся поддиапазон всего диапазона ключей раздела. Рассмотрим пример. Схема секционирования по диапазонам с нижним значением ключа 0, верхним значением ключа 99 и количеством разделов, равным 4, создаст четыре раздела, как показано ниже.

![Секционирование по диапазонам](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Наиболее распространенным приемом является создание хэша на основе уникального ключа в наборе данных. Некоторые общие примеры ключей: код идентификации транспортного средства (VIN), идентификатор сотрудника или уникальная строка. В результате использования этого уникального ключа создастся длинный хэш-код, модуль диапазона ключей, который будет использоваться как ключ. Можно указать верхнюю и нижнюю границы допустимого диапазона ключей.


### Выбор хэш-алгоритма
Важной частью хэширования является выбор хэш-алгоритма. Необходимо продумать, следует ли группировать одинаковые ключи рядом друг с другом (хэширование с учетом местоположения) или распределить действия по всем разделам (распределенное хэширование). Последний алгоритм используется чаще.

Хороший хэш-алгоритм распределения имеет следующие характеристики: простое вычисление, небольшое количество конфликтов и равномерное распределение ключей. Примером эффективного хэш-алгоритма является [FNV 1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function).


Хороший ресурс для выбора общего алгоритма хэш-кода — [страница Wikipedia по функциям хэширования](http://en.wikipedia.org/wiki/Hash_function).

## Создание службы с отслеживанием состояния с несколькими разделами
Далее вы создадите свою первую службу Reliable Services с отслеживанием состояния с несколькими разделами. В этом примере мы создадим очень простое приложение, в каждом разделе которого будут храниться фамилии, начинающиеся с определенной буквы.

Прежде чем приступать к написанию кода, следует определиться с разделами и их ключами. Нам потребуется 26 разделов — по одному для каждой буквы алфавита. Но как определить нижнюю и верхнюю границы ключей? Нам буквально нужно иметь по одному разделу на каждую букву. Следовательно, можно использовать 0 в качестве ключа нижней границы и 25 — верхней, так как каждая буква является собственным ключом.


>[AZURE.NOTE]Это упрощенный сценарий, так как на самом деле распределение было бы неравномерным. Фамилии, начинающиеся с буквы S или M встречаются чаще, чем те, которые начинаются с X или Y.


1. Откройте Visual Studio -> Создать файл -> Проект
2. В диалоговом окне «Создание проекта» выберите приложение Service Fabric.
3. Назовите проект AlphabetPartitions.
4. В диалоговом окне "Создание службы" выберите "Служба с отслеживанием состояния" и назовите ее Alphabet.Processing, как показано на рисунке ниже. ![alphabetstateful](./media/service-fabric-concepts-partitioning/alphabetstatefulnew.png)
5. Укажите количество разделов. Откройте в проекте AlphabetPartitions файл ApplicationManifest.xml и задайте параметру Processing\_PartitionCount значение 26, как показано ниже.

    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
    Необходимо также обновить свойства LowKey и HighKey элемента StatefulService, как показано ниже. ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```

6. Чтобы предоставить доступ к службе, откройте конечную точку на порте. Для этого добавьте элемент конечной точки (расположенный в папке PackageRoot) в файл ServiceManifest.xml для службы Alphabet.Processing, как показано ниже.

    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Protocol="http" Type="Internal" />
    ```

    Теперь служба настроена на прослушивание внутренней конечной точки в 26 разделах.

7. Далее нужно переопределить метод `CreateServiceReplicaListeners()` класса Processing.

    >[AZURE.NOTE]Для этого примера предполагается использование простого прослушивателя HttpCommunicationListener. Дополнительные сведения об обмене данными в Reliable Services см. [здесь](service-fabric-reliable-services-communication.md).

8. Рекомендуемый шаблон URL-адреса, который прослушивает реплика, имеет следующий формат: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`. По этому шаблону можно настроить прослушиватель связи для прослушивания на правильных конечных точках. На одном компьютере может размещаться несколько копий этой службы. Следовательно, этот адрес должен быть уникальным для реплики. Именно поэтому в URL-адресе содержится идентификатор раздела и идентификатор реплики. HttpListener может прослушивать несколько адресов на одном порте при условии, что префикс URL-адреса является уникальным. Дополнительный GUID используется в сложных случаях, когда вторичные реплики также прослушивают запросы только для чтения. Если это так, следует убедиться, что новый уникальный адрес используется при переходе от первичной реплики к вторичной для принудительного разрешения адресов клиентами. Знак + здесь используется как адрес, чтобы реплика прослушивала все доступные узлы (IP, FQDM, localhost и т. д.) В приведенном ниже коде показан пример.

    ```CSharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
            return new[] { new ServiceReplicaListener(CreateInternalListener, "Internal", false) };
    }
    private ICommunicationListener CreateInternalListener(StatefulServiceInitializationParameters args)
    {
        EndpointResourceDescription internalEndpoint = args.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");

        string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                this.ServiceInitializationParameters.PartitionId,
                this.ServiceInitializationParameters.ReplicaId,
                Guid.NewGuid());

        string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
        string uriPublished = uriPrefix.Replace("+", nodeIP);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
Также следует отметить, что опубликованный URL-адрес немного отличается от префикса URL-адреса прослушивания. URL-адрес прослушивания назначается HttpListener. Опубликованный URL-адрес — это URL-адрес, опубликованный в службе именования Service Fabric, который используется для обнаружения службы. Клиенты будут запрашивать этот адрес с помощью службы обнаружения. Для возможности подключения получаемые клиентами адреса должны иметь фактический IP-адрес или полное доменное имя узла. Поэтому необходимо заменить + IP-адресом узла или полным доменным именем, как показано выше.
9. Последним шагом является добавление логики обработки к службе, как показано ниже.

    ```CSharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
          string output = null;
          string user = context.Request.QueryString["lastname"].ToString();

          try
          {
              output = await this.AddUserAsync(user);
          }
          catch (Exception ex)
          {
              output = ex.Message;
          }

          using (HttpListenerResponse response = context.Response)
          {
              if (output != null)
              {
                  byte[] outBytes = Encoding.UTF8.GetBytes(output);
                  response.OutputStream.Write(outBytes, 0, outBytes.Length);
              }
          }
      }
      private async Task<string> AddUserAsync(string user)
      {
          IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");

          using (ITransaction tx = this.StateManager.CreateTransaction())
          {
              bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);

              await tx.CommitAsync();

              return String.Format(
                  "User {0} {1}",
                  user,
                  addResult ? "sucessfully added" : "already exists");
          }
      }
    ```

    `ProcessInternalRequest` считывает значения параметра строки запроса, который используется для вызова раздела, и вызывает метод `AddUserAsync` для добавления lastname в надежный словарь `m_name`.

10. Добавим в проект службу без отслеживания состояния, чтобы посмотреть, как можно вызвать определенный раздел. Эта служба выступает в качестве простого веб-интерфейса, который принимает lastname как параметр строки запроса, определяет ключ раздела и отправляет его на обработку службе Alphabet.Processing.
11. В диалоговом окне "Создание службы" выберите "Служба без отслеживания состояния" и назовите ее Alphabet.WebApi, как показано ниже. ![alphabetstateless](./media/service-fabric-concepts-partitioning/alphabetstatelessnew.png)
12. Откройте порт, обновив сведения о конечной точке службы Alphabet.WebApi в файле ServiceManifest.xml, как показано ниже.

    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8090"/>
    ```

13. Необходимо вернуть коллекцию прослушивателей ServiceInstanceListeners. Опять же, можно реализовать простой прослушиватель HttpCommunicationListener.

    ```CSharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
           return new[] {new ServiceInstanceListener(this.CreateInputListener, "Input")};
    }
    private ICommunicationListener CreateInputListener(StatelessServiceInitializationParameters args)
    {
           // Service instance's URL is the node's IP & desired port
           EndpointResourceDescription inputEndpoint = args.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
           string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
           var uriPublished = uriPrefix.Replace("+", m_nodeIP);
           return new HttpCommunicationListener(uriPrefix, uriPublished, ProcessInputRequest);
     }
     ```
14. Теперь нам необходимо реализовать логику обработки. После получения запроса HttpCommunicationListener вызывает `ProcessInputRequest`. Добавляем приведенный ниже код.

    ```CSharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
           String output = null;
           try
           {
               string lastname = context.Request.QueryString["lastname"];
               char firstLetterOfLastName = lastname.First();
               int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A';

               ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
               ResolvedServiceEndpoint ep = partition.GetEndpoint();
               JObject addresses = JObject.Parse(ep.Address);
               string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

               UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
               primaryReplicaUriBuilder.Query = "lastname=" + lastname;

               string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);

               output = String.Format(
               "Result: {0}. Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. Processing service partition ID: {4}. Processing service replica address: {5}",
               result,
               partitionKey,
               firstLetterOfLastName,
               lastname,
               partition.Info.Id,
               primaryReplicaAddress);
    }
    catch (Exception ex) { output = ex.Message; }
    using (var response = context.Response)
    {
               if (output != null)
               {
                   output = output + "added to Partition: " + primaryReplicaAddress;
                   byte[] outBytes = Encoding.UTF8.GetBytes(output);
                   response.OutputStream.Write(outBytes, 0, outBytes.Length);
               }
           }
      }
      ```

    Давайте разберемся в процессе, шаг за шагом. Код считывает первую букву из параметра строки запроса в `lastname` в тип char. Затем он определяет ключ раздела для этой буквы, вычитая шестнадцатеричное значение `A` из шестнадцатеричного значения первой буквы фамилии.

    ```CSharp
    string lastname = context.Request.QueryString["lastname"]; char firstLetterOfLastName = lastname.First(); int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A';
    ```

    Помните, что в этом примере мы используем 26 разделов с одним ключом для каждого раздела. Далее мы получим раздел службы `partition` для этого ключа с помощью метода `ResolveAsync` в объекте `servicePartitionResolver`. `servicePartitionResolver` определяется так:

    ```CSharp
    private static readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```

    В качестве параметров метод `ResolveAsync` принимает универсальный код ресурса (URI) службы, ключ раздела и маркер отмены. URI службы для службы обработки — `fabric:/AlphabetPartitions/Processing`. Далее мы получим конечную точку раздела.

    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```

    И наконец, создаем URL-адрес конечной точки и строку запроса и вызова службы обработки.

    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;

    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```

    После выполнения обработки пишем вывод.

15. Последним шагом будет тестирование службы. Visual Studio использует параметры приложения для локального и облачного развертываний. Для локального тестирования службы с 26 разделами требуется обновить файл `Local.xml` в папке ApplicationParameters проекта AlphabetPartitions, как показано ниже.

    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
  </Parameters>
  ```

16. После развертывания службу и все ее разделы можно проверить в обозревателе Service Fabric Explorer. ![служба](./media/service-fabric-concepts-partitioning/alphabetservicerunning.png)
17. В браузере можно проверить логику секционирования, введя `http://localhost:8090/?lastname=somename`. Вы увидите, что все фамилии, которые начинаются с одинаковой буквы, хранятся в одном разделе. !["Обзор"](./media/service-fabric-concepts-partitioning/alphabetinbrowser.png)

Весь исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/AlphabetPartitions).

## Дальнейшие действия

Сведения о понятиях, принятых для структуры служб, см. в следующих статьях:

- [Доступность служб структуры служб](service-fabric-availability-services.md)

- [Масштабируемость служб структуры служб](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

<!-----HONumber=AcomDC_1125_2015-->
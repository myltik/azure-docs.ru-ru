<properties
   pageTitle="Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight | Microsoft Azure"
   description="Узнайте, как обрабатывать данные службы концентраторов событий с использованием топологии C# Storm, созданной в Visual Studio с помощью средств HDInsight для Visual Studio."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2015"
   ms.author="larryfr"/>

# Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)

Служба концентраторов событий Azure позволяет обрабатывать значительные объемы данных из веб-сайтов, приложений и устройств. Воронка службы концентраторов событий упрощает использование Apache Storm в HDInsight для анализа этих данных в режиме реального времени. Вы также можете записывать данные в службу концентраторов событий из Storm с помощью сита службы концентраторов событий.

В этом документе вы узнаете, как использовать средства HDInsight для Visual Studio, а также воронку и сито службы концентраторов событий для создания двух гибридных топологий C#/Java:

* **EventHubWriter** — случайным образом генерирует данные и записывает их в службу концентраторов событий

* **EventHubReader** — считывает данные из службы концентраторов событий и сохраняет их в табличное хранилище Azure

[AZURE.NOTE]Шаги, описанные в этом документе, можно применять только к кластеру HDInsight под управлением Windows. Java-версию этого проекта, которая работает с кластером под управлением Linux или Windows, можно найти в разделе [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## Предварительные требования

* <a href="../hdinsight-storm-getting-started/" target="_blank">Кластер Apache Storm в HDInsight</a>

* <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Концентратор событий Azure</a>

* <a href="http://azure.microsoft.com/downloads/" target="_blank">Пакет Azure .NET SDK</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Средства HDInsight для Visual Studio</a> (версии 2/17/2015 или более поздней)

## Завершенный проект

Вы можете загрузить полную версию проекта, созданного в этом учебнике, с портала GitHub: [eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid). Но вам нужно будет указать параметры конфигурации, выполнив действия, описанные в этом учебнике.

> [AZURE.NOTE]При использовании завершенного проекта следует воспользоваться **диспетчером пакетов NuGet**, чтобы восстановить пакеты, необходимые для этого решения.

## Воронка и сито службы концентраторов событий

Воронка и сито службы концентраторов событий — это компоненты Java, которые позволяют работать со службой концентраторов событий из Apache Storm. Хотя эти компоненты написаны на языке Java, средства HDInsight для Visual Studio позволяют создавать гибридные топологии, сочетающие в себе компоненты C# и Java.

Воронка и сито распространяются в виде отдельного файла архива Java (JAR) с именем **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

### Скачивание файла JAR

Самая последняя версия файла **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** содержится в проекте<a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">Примеры HDInsight Storm</a> и находится в папке **lib**. Чтобы скачать файл, используйте один из следующих методов.

> [AZURE.NOTE]Воронка и сито были переданы для включения в проект Apache Storm. Дополнительные сведения см. на странице портала GitHub <a href="https://github.com/apache/storm/pull/336/files">STORM 583: первоначальный возврат для концентраторов событий storm</a>.

* **Загрузка ZIP-файла**: на странице <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">Примеры HDInsight Storm</a> нажмите кнопку **Download ZIP** (Загрузить ZIP-файл) на правой панели, чтобы загрузить ZIP-файл с проектом.

	![кнопка загрузки ZIP-файла](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	После загрузки извлеките архив, чтобы поместить файл в каталог **lib**.

* **Клонирование проекта**: если у вас установлен <a href="http://git-scm.com/" target="_blank">Git</a>, используйте следующую команду для локального клонирования репозитория, а затем найдите файл в каталоге the **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Настройка концентраторов Event Hub

Служба концентраторов событий используется в качестве источника данных для этого примера. Чтобы создать новый концентратор Event Hub, выполните следующие действия.

1. На [портале Azure](https://manage.windowsazure.com) выберите **СОЗДАТЬ** > **Service Bus** > **Концентратор событий** > **Настраиваемое создание **.

2. В диалоговом окне **Добавление нового концентратора событий**введите **Имя концентратора событий**, выберите **Регион** для создания в нем концентратора и создайте новое пространство имен или выберите существующее. Щелкните **стрелку** для продолжения.

	![страница мастера 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE]Для уменьшения задержек и затрат в поле **Расположение** следует выбрать то же расположение, в котором находится сервер Storm в HDInsight.

2. В диалоговом окне **Настройка концентраторов событий** введите значения параметров **Количество разделов** и **Хранение сообщений**. В этом примере числу разделов присвойте значение 10, а хранению сообщений — 1. Запомните количество разделов, поскольку это значение понадобится позже.

	![страница мастера 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. После создания концентратора событий выберите пространство имен, выберите **Концентраторы событий**, а затем выберите концентратор событий, который был создан ранее.

4. Выберите **Настроить** и создайте две новые политики доступа, используя следующую информацию.

	<table>
<tr><th>Имя</th><th>Разрешения</th></tr>
<tr><td>Модуль записи</td><td>Отправка</td></tr>
<tr><td>читатель.</td><td>Прослушивание</td></tr>
</table>После создания разрешений выберите значок **Сохранить** в нижней части страницы. Будут созданы политики совместного доступа, которые будут использоваться для отправки (writer) сообщений этому концентратору событий и их прослушивания (reader).

	![политики](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. После сохранения политик используйте **Генератор общего ключа доступа** в нижней части страницы для получения ключа для политик **writer** и **reader**. Сохраните ключ для дальнейшего использования.

## Настройка табличного хранилища

Табличное хранилище будет использоваться для хранения значений, считанных из концентратора событий, поскольку табличное хранилище можно легко просмотреть из среды Visual Studio с помощью **обозревателя серверов** Чтобы создать новое табличное хранилище, выполните следующие действия.

1. На [портале Azure](https://manage.windowsazure.com) выберите **СОЗДАТЬ** > **Службы данных** > **Хранилище** > **Быстрое создание**.

	![быстрое создание хранилища](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Введите **Имя** учетной записи хранения, выберите **Расположение** и установите **флажок** для создания учетной записи хранения.

	> [AZURE.NOTE]Для уменьшения задержек и затрат в поле **Расположение** следует выбрать то же расположение, в котором находится служба концентраторов событий и сервер Storm в HDInsight.

3. После подготовки учетной записи хранения выберите эту учетную запись, а затем используйте ссылку **Упр. ключами доступа** в нижней части страницы, чтобы получить **Имя учетной записи хранения** и **Первичный ключ доступа**. Сохраните эти сведения для использования в дальнейшем.

	![ключи доступа](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Создание EventHubWriter

В этом разделе вы создадите топологию, которая записывает данные в службу концентраторов событий с помощью сита службы концентраторов событий.

1. Если вы еще не установили последнюю версию средств HDInsight для Visual Studio, см. статью <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Начало работы со средствами HDInsight для Visual Studio</a>.

2. Откройте Visual Studio, выберите **Файл** > **Создать**, а затем выберите **Проект**.

3. В диалоговом окне **Новый проект** разверните **Установленные** > **Шаблоны** и выберите **HDInsight**. В списке шаблонов выберите **Приложение Storm**. В нижней части диалогового окна, введите имя приложения **EventHubWriter**.

	![изображение](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. После создания проекта вы получите такие файлы:

	* **Program.cs** — определяет топологию для проекта. Обратите внимание, что по умолчанию создается топология, состоящая из одной воронки и одного сита.

	* **Spout.cs** — пример воронки.

	* **Bolt.cs** — пример сита. Этот файл будет удален, так как вы будете использовать сито службы концентраторов событий для записи в концентратор событий.

### Конфигурация

1. В **обозревателе решений** щелкните правой кнопкой мыши **EventHubWriter** и выберите пункт **Свойства**.

2. В свойствах проекта выберите **Параметры** и выберите **Этот проект не содержит файл параметров по умолчанию. Щелкните здесь, чтобы создать такой файл.**

3. Введите следующие параметры. Используйте сведения для концентратора событий, созданного ранее, в столбце **Значение**.

	<table>
<tr><th style="text-align:left">Имя</th><th style="text-align:left">Тип</th><th style="text-align:left">Область</th></tr>
<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">Приложение</td></tr>
<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">Приложение</td></tr>
<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">Приложение</td></tr>
<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">string</td><td style="text-align:left">Приложение</td></tr>
<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Приложение</td></tr>
</table>

4. Сохраните изменения и закройте страницу **Свойства**.

### Определение топологии

1. В **обозревателе решений** щелкните правой кнопкой мыши **Bolt.cs** и выберите команду **Удалить**. Поскольку вы используете сито службы концентраторов событий Java, этот файл вам не нужен.

2. Откройте файл **Program.cs** и добавьте следующий код непосредственно после строки `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	Первая строка считывает количество разделов из определенных ранее свойств. Вторая строка определяет десериализатор, используемый для десериализации данных JSON, созданных воронкой, в `java.lang.String`, чтобы компоненты Java могли использовать эти данные.

4. Найдите следующий код:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Замените его следующим кодом:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Он создает воронку и использует количество разделов службы концентраторов событий в качестве указания параллелизма для этого компонента. Этот код должен создавать экземпляр воронки для каждого раздела.

	Это также связывает созданный ранее десериализатор с выходным потоком из данного компонента. Это позволяет нижестоящему в потоке компоненту EventHubSpout использовать данные, полученные из воронки C#.

5. Сразу после предыдущего кода добавьте следующий код:

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName,
			"true"));

	Он создает новый конструктор для сита Java, который используется для настройки нового экземпляра сита во время выполнения. В данном случае вы используете <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>, чтобы настроить воронку с использованием добавленных ранее сведений о конфигурации службы концентраторов событий. В частности, этот код используется HDInsight во время выполнения в следующих целях:

	* Создание нового экземпляра **com.microsoft.eventhubs.bolt.EventHubBoltConfig** с помощью указанных вами сведений о службе концентраторов событий.
	* Создание нового экземпляра **com.microsoft.eventhubs.bolt.EventHubBolt** посредством передачи экземпляра **EventHubBoltConfig**.

6. Найдите следующий код:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Замените его следующим кодом:

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Он дает топологии указание использовать **JavaComponentConstructor** из предыдущего шага в качестве сита. В этой топологии данный компонент можно именовать понятным именем EventHubBolt. Указание параллелизма задается равным количеству разделов для концентратора событий и подписывается на данные, созданные воронкой ("Spout").

Работа с **Program.cs** завершена. Топология определена, однако теперь необходимо изменить **Spout.cs** таким образом, чтобы он создавал данные в формате, который может использовать сито службы концентраторов событий.

> [AZURE.NOTE]По умолчанию эта топология создает один рабочий процесс, которого достаточно для примера. Если вы адаптируете эту топологию для рабочего кластера, следует добавить следующее для изменения количества рабочих процессов:

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Изменение воронки

Сито службы концентраторов событий ожидает одно строковое значение, которое перенаправит в концентратор событий. В следующем примере вы измените файл **Spout.cs** по умолчанию, чтобы получить строку JSON.

1. В **обозревателе решений** откройте файл **Spout.cs** и добавьте следующий код в его начало:

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Это упростит работу с данными JSON.
    
    > [AZURE.NOTE]Должен быть уже установлен пакет JSON.NET, поскольку он необходим для платформы SCP.NET, которая используется для топологий C# Storm.

3. Найдите следующий код:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Замените его следующим кодом:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Это изменит определение данных, созданных воронкой для использования **строковых** данных и сериализатора **CustomizedInteropJSONSerializer**, задекларированного в топологии ранее (в файле program.cs).

2. Замените метод **NextTuple** на следующий код:

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Он случайным образом создаст ИД устройства, значение, а затем использует Json.NET для создания объекта JSON с помощью этих значений.

3. Сохраните файл **Spout.cs**.

На данном этапе у вас есть базовая топология, которая будет создавать случайные данные и сохранять их в службе концентраторов событий с помощью сита службы концентраторов концентратора событий. Далее вы создадите модуль чтения.

## Создание EventHubReader

В этом разделе вы создадите топологию, которая считывает данные из службы концентраторов событий с помощью воронки службы концентраторов событий.

2. Откройте Visual Studio, выберите последовательно **Файл** > **Создать** > **Проект**.

3. В диалоговом окне **Новый проект** разверните **Установленные** > **Шаблоны** и выберите **HDInsight**. В списке шаблонов выберите **Приложение Storm**. В нижней части диалогового окна, введите имя приложения **EventHubReader**.

### Конфигурация

1. В **обозревателе решений** щелкните правой кнопкой мыши **EventHubReader** и выберите пункт **Свойства**.

2. В свойствах проекта выберите **Параметры** и выберите **Этот проект не содержит файл параметров по умолчанию. Щелкните здесь, чтобы создать такой файл.**

3. Введите следующие параметры. Используйте сведения для концентратора событий и учетной записи хранения, созданные ранее, в столбце **Значение**.

	<table>
<tr><th style="text-align:left">Имя</th><th style="text-align:left">Тип</th><th style="text-align:left">Область</th></tr>
<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Приложение</th></tr>
<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(строка подключения)</th><th style="text-align:left">Приложение</th></tr>
<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
</table>Для **TableName** введите имя таблицы, в которой должны храниться события.

    Для **StorageConnection** введите значение `DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;`. Замените **myAccount** и **myKey** именем учетной записи хранения и ключом, полученными ранее.

	Эти значения будут использоваться топологией для связи с концентраторами событий и табличным хранилищем.

4. Сохраните изменения и закройте страницу **Свойства**.

### Определение топологии

1. В **обозревателе решений** щелкните правой кнопкой мыши **Spout.cs** и выберите пункт **Удалить**. Поскольку вы используете воронку Java службы концентраторов событий, этот файл вам не нужен.

2. Откройте файл **Program.cs** и добавьте следующий код непосредственно после строки`TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		EventHubSpoutConfig ehConfig = new EventHubSpoutConfig(
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount);

	Количество разделов считывается и назначается локальной переменной. Она будет использоваться несколько раз.

	`EventHubSpoutConfig` определяет конфигурацию для воронки концентратора событий. В этом случае данные конфигурации концентраторов событий, добавленные ранее. Использование воронки концентратора событий Java в фоновом режиме и создание нового экземпляра **com.microsoft.eventhubs.spout.EventHubSpoutConfig** с помощью сведений о службе концентраторов событий.

5. Найдите следующий код:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Замените его следующим кодом:

        topologyBuilder.SetEventHubSpout(
            "EventHubSpout", 
            ehConfig, 
            partitionCount); 

	Он дает топологии указание создать новую воронку концентратора событий и использовать `EventHubSpoutConfig` из предыдущего шага в конфигурации. EventHubSpout задает понятное имя воронки, а `partitionCount` используется для определения подсказки параллелизма. В фоновом режиме создается новый экземпляр компонента Java **com.microsoft.eventhubs.spout.EventHubSpout** на основе предоставленной информации о конфигурации.

2. Добавьте следующий код сразу после предыдущего кода:

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	При этом создается пользовательский сериализатор для сериализации информации, созданной компонентами Java (например, EventHubSpout), в формат JSON, который могут использовать нижестоящие компоненты C#.

3. Найдите следующий код:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Замените его следующим кодом:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount,
            true).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Он дает топологии указание использовать сито (определенное в файле Bolt.cs). Пользовательский сериализатор, определенный ранее, используется здесь, чтобы это сито могло использовать данные, создаваемые вышестоящими компонентами Java. В данном случае это компонент EventHubSpout.

    > [AZURE.IMPORTANT]Последний параметр для SetBolt (со значением `true`) обеспечивает функциональность ACK для этого сита. Это необходимо, так как компонент EventHubSpout ожидает ACK для созданных им данных. Если ACK не будут возвращаться нижестоящими компонентами, воронка прекратит получение после обработки около 1000 сообщения.

На данном этапе работа с **Program.cs** завершена. Топология определена, однако теперь необходимо создать вспомогательный класс для записи данных в табличное хранилище, после чего необходимо изменить файл **Bolt.cs**, чтобы он мог распознать созданные воронкой данные.

> [AZURE.NOTE]По умолчанию эта топология создает один рабочий процесс, которого достаточно для примера. Если вы адаптируете эту топологию для рабочего кластера, следует добавить следующее для изменения количества рабочих процессов:

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Создание вспомогательного класса

При записи данных в табличное хранилище необходимо создать класс, описывающий данные, которые будут записаны.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubReader**, а затем выберите пункт **Добавить**, **Новый класс**. Присвойте новому классу имя **Devices.cs**.

2. Откройте файл **Devices.cs** и замените существующий код этим кодом:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;

		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }

		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	Этот код создаст сущности в табличном хранилище, состоящие из ключа раздела (которому будет присвоен код устройства, считанный из концентратора событий), уникального ключа строки и значения, считываемого из концентратора событий. Каждая сущность также будет иметь метку времени, которая создается автоматически при вставке сущности в таблицу.

### Изменение сита

1. В **обозревателе решений** разверните проект **EventHubReader** и откройте файл **Bolt.cs**. Добавьте следующий код в начало файла.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Это позволит упростить работу с данными JSON из сита и позволит записывать данные в табличное хранилище.

2. Найдите инструкцию `private int count;` и замените ее следующим кодом:

        private CloudTable table;

	Он будет использоваться при подключении к таблице.

4. Найдите следующий код:

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Замените его следующим кодом:

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Он указывает ситу на то, что оно будет получать значение **string** вместо **int** и что данные следует десериализировать с помощью десериализатора **CustomizedInteropJSONDeserialzer**, который задекларирован в топологии ранее (в файле program.cs).

3. Сразу после предыдущего кода добавьте следующий код:

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Он будет выполнять подключение к таблице **events** с помощью предварительно настроенной строки подключения. Если она не существует, то будет создана.

2. Найдите метод **Execute** и замените его следующим кодом:

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
                this.ctx.Ack(tuple);
            }
        }

	Он использует Json.NET для синтаксического анализа данных JSON из воронки, а затем выбирает поля **deviceId** и **deviceValue**. Затем создается новый объект **Device**, при этом во время инициализации используется **deviceId**, чтобы задать ключ раздела для таблицы. После этого присваивается значение **deviceValue** и сущность вставляется в таблицу.

    После вставки сущности в таблицу вызывается `Ack()` для кортежа, чтобы проинформировать воронку об успешно обработанных данных.

    > [AZURE.IMPORTANT]Компонент EventHubSpout ожидает ACK для каждого кортежа от нижестоящих компонентов, как от этого сита, например. Если не получены ACK, EventHubSpout предполагает, что в обработке для кортежа произошел сбой.

На этом этапе у вас есть полная топология, которая будет считывать данные из концентратора событий и сохранять их в таблице **events** табличного хранилища.

## Развертывание топологий

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubReader** и выберите пункт **Отправить в Storm в HDInsight**.

	![отправить в storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. В диалоговом окне **Отправка топологии** выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Пути к файлам Java, элемент**, выберите **...** и укажите каталог, содержащий загруженный ранее файл **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. Теперь нажмите кнопку **Отправить**.

	![изображение диалогового окна отправки](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. После отправки топологии отобразится **средство просмотра топологий Storm**. Выберите топологию **EventHubReader** в левой части диалогового окна, чтобы просмотреть статистику для топологии. Сейчас она должна быть пустой, так как в службу концентраторов событий еще не записано ни одно событие.

	![пример представления хранилища](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubWriter** и выберите пункт **Отправить в Storm в HDInsight**.

2. В диалоговом окне **Отправка топологии** выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Пути к файлам Java**, элемент **...** и укажите каталог, содержащий загруженный ранее файл **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. Теперь нажмите кнопку **Отправить**.

5. После отправки топологии обновите список топологий в **средстве просмотра топологий Storm**, чтобы убедиться, что обе топологии выполняются в кластере.

6. После запуска обеих топологий выберите **обозреватель серверов**, затем разверните узел **Azure** > **Хранилище**, затем выберите учетную запись хранения, созданную раннее. Внутри учетной записи хранения разверните узел **Таблицы**. Наконец, дважды щелкните таблицу **events**, чтобы открыть ее. Вы должны видеть данные, сохраненные в этой таблице из топологии **EventHubReader**.

	* События создаются топологией **EventHubWriter**, которая записывает их в концентратор событий.

	* Затем **EventHubReader** считывает события из службы концентраторов событий и сохраняет их в таблице **events** табличного хранилища.

## Остановка топологий

Чтобы остановить топологии, выберите каждую из них в **средстве просмотра топологий Storm**, а затем выберите элемент **Удалить**.

![изображение уничтожения топологии](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## Примечания

### Контрольные точки

EventHubSpout периодически передает информацию о своем состоянии на узел Zookeeper, который сохраняет текущее смещение для прочитанных сообщений из очереди. Это позволяет компоненту принимать сообщения при сохраненном смещении в следующих сценариях:

* Работа экземпляра компонента завершена аварийно, и он будет перезапущен.

* Расширение или сжатие путем добавления или удаления узлов кластера.

* Топология удалена и перезапущена **под тем же именем**.

Вы также можете экспортировать и импортировать сохраненные контрольные точки в WASB (хранилище Azure в кластере HDInsight.) Сценарии для этого находятся в папке Storm кластера HDInsight: **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE]Номер версии в пути может отличаться, так как версия Storm, установленная в кластере, может в будущем измениться.

Сценарии в этом каталоге:

* **stormmeta\_import.cmd**: импорт всех метаданных Storm из контейнера хранилища кластера по умолчанию в Zookeeper.

* **stormmeta\_export.cmd**: экспорт всех метаданных Storm из Zookeeper в контейнер хранилища кластера по умолчанию.

* **stormmeta\_delete.cmd**: удаление всех метаданных Storm из Zookeeper.

Экспорт и импорт позволяют сохранять данные контрольных точек, если понадобится удалить кластер, но нужно будет возобновить обработку из текущего смещения в концентраторе при повторном развертывании нового кластера в Интернет.

> [AZURE.NOTE]Поскольку данные сохраняются в контейнере хранилища по умолчанию, новый кластер **должен** использовать учетную запись хранения и контейнер предыдущего кластера.

## Сводка

В этом документе рассмотрено использование воронки и сита Java службы концентраторов событий из топологии C# для работы с данными в концентраторе событий Azure. Дополнительные сведения о создании топологий C# см. в следующих статьях.

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)
 

<!---HONumber=Oct15_HO1-->
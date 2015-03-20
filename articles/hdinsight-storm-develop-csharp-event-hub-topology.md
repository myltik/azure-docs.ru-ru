<properties
   pageTitle="Обработка событий из концентратора событий Azure с помощью Storm в HDInsight | Azure"
   description="Узнайте, как обрабатывать данные концентратора событий с использованием топологии Storm C#, созданной в Visual Studio с помощью средств HDInsight для Visual Studio."
   services="hdinsight"
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
   ms.date="03/03/2015"
   ms.author="larryfr"/>

#Обработка событий из концентратора событий Azure с помощью Storm в HDInsight (C#)

Концентратор событий Azure позволяет обрабатывать значительные объемы данных из веб-сайтов, приложений и устройств. Воронка концентратора событий упрощает использование Apache Storm в HDInsight для анализа этих данных в режиме реального времени. Вы также можете записывать данные в концентратор событий из Storm с помощью сита концентратора событий. 

В этом документе вы узнаете, как использовать средства HDInsight для Visual Studio, а также воронку и сито концентратора событий для создания двух гибридных топологий C#/Java:

* **EventHubWriter** - случайным образом формирует данные и записывает их в концентратор событий

* **EventHubReader** - считывает данные из концентратора событий и сохраняет их в табличное хранилище Azure

##Предварительные требования

* <a href="../hdinsight-storm-getting-started/" target="_blank">Кластер Apache Storm в HDInsight</a>

* <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Концентратор событий Azure</a>

* <a href="http://azure.microsoft.com/downloads/" target="_blank">Пакет Azure .NET SDK</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Средства HDInsight для Visual Studio</a> - версии 2/17/2015 или более поздней

##Завершенный проект

Полную версию проекта, созданного в этой статье, можно скачать по адресу [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid). Однако вам все равно потребуется задать параметры конфигурации, выполнив описанные в этом документе действия.

> [AZURE.NOTE] При использовании завершенного проекта следует воспользоваться **диспетчером пакетов NuGet**, чтобы восстановить необходимые для этого решения пакеты.

##Воронка и сито концентратора событий

Воронка и сито концентратора событий - это компоненты Java, которые позволяют легко работать с концентратором событий из Apache Storm. Хотя эти компоненты написаны на языке Java, средства HDInsight для Visual Studio позволяют создавать гибридные топологии, сочетающие в себе компоненты C# и Java.

Воронка и сито распространяются в виде отдельного файла архива (JAR) с именем **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

###Скачивание файла JAR

Самая последняя версия файла **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** включена в состав проекта <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> и находится в папке **lib**. Чтобы скачать файл, используйте один из следующих методов.

> [AZURE.NOTE] Воронка и сито были переданы для включения в проект Apache Storm. Дополнительные сведения см. в описании <a href="https://github.com/apache/storm/pull/336/files">запроса на включение внесенных изменений</a>.

* **Скачайте файл ZIP** - на сайте <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> нажмите кнопку **Download ZIP** (Скачать ZIP), чтобы скачать файл ZIP с проектом.

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	После скачивания можно извлечь архив, чтобы поместить файл в каталог **lib**.

* **Клонируйте проект** - если у вас установлен <a href="http://git-scm.com/" target="_blank">Git</a>, используйте следующую команду для локального клонирования репозитория, а затем найдите файл в каталоге **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

##Настройка концентраторов Event Hub

Концентратор событий Event Hub используется в качестве источника данных для этого примера. Чтобы создать новый концентратор Event Hub, выполните следующие действия.

1. На [портале Azure](https://manage.windowsazure.com) выберите **СОЗДАТЬ | Служебная шина | Концентратор событий | Настраиваемое создание**.

2. В диалоговом окне **Добавить новый концентратор событий** введите **Имя концентратора событий**, выберите **Регион** для создания в нем концентратора, либо создайте новое пространство имен или выберите существующее. После этого щелкните **Стрелку**.

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Для уменьшения задержек и затрат в поле **Расположение** следует выбрать то же расположение, где находится сервер Storm в HDInsight.

2. В диалоговом окне **Настройка концентратора событий** введите значения параметров **Число разделов** и **Хранение сообщений**. В этом примере числу разделов присвойте значение 10, а хранению сообщений - 1. Запомните количество разделов, поскольку это значение понадобится позже.

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. После создания концентратора событий выберите пространство имен, затем выберите **Концентраторы событий**. После этого выберите раннее созданный вами концентратор событий.

4. Выберите **Настроить**, затем создайте две новые политики доступа, используя следующую информацию.

	<table>
	<tr><th>Имя</th><th>Разрешения</th></tr>
	<tr><td>writer</td><td>Отправка</td></tr>
	<tr><td>reader</td><td>Прием</td></tr>
	</table>

	После создания разрешений выберите значок **Сохранить** в нижней части страницы. Это создает политики совместного доступа, которые будет использоваться для отправки (writer) сообщений концентратору и чтения (reader) с него этих сообщений.

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. После сохранения политик используйте клавишу **Генератор ключей совместного доступа** в нижней части страницы для получения ключа для обеих политик: **writer** и **reader**. Сохраните для дальнейшего использования.

##настройка табличного хранилища

Табличное хранилище будет использоваться для хранения значений, считанных из концентратора событий, поскольку табличное хранилище можно легко просмотреть из среды Visual Studio посредством **обозревателя серверов**. Чтобы создать новое табличное хранилище, выполните следующие действия.

1. На [портале Azure](https://manage.windowsazure.com) выберите **СОЗДАТЬ | Службы данных | Хранилище | Быстрое создание**.

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Введите **Имя** учетной записи хранения, выберите **Расположение** и установите **флажок** для создания учетной записи хранения.

	> [AZURE.NOTE] Для уменьшения задержек и затрат в поле **расположение** следует выбрать то же расположение, где находится концентратор событий и сервер Storm в HDInsight.

3. После подготовки учетной записи хранения выберите эту учетную запись, а затем используйте ссылку **Упр. ключами доступа** в нижней части страницы, чтобы получить **Имя учетной записи хранения** и **Первичный ключ доступа**. Сохраните эти сведения для использования в дальнейшем.

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

##Создание EventHubWriter

В этом разделе вы создадите топологию, которая записывает данные в концентратор событий с помощью сита концентратора событий.

1. Если вы еще не установили последнюю версию средств HDInsight для Visual Studio, см. статью <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Приступая к работе со средствами HDInsight для Visual Studio</a>.

2. Откройте Visual Studio, выберите **Файл**, **Создать**, а затем - **Проект**.

3. В диалоговом окне **Новый проект** разверните **Установленные**, **Шаблоны** и выберите **HDInsight**. В списке шаблонов выберите **Приложение Storm**. В нижней части диалогового окна, введите имя приложения **EventHubWriter**.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. После создания проекта у вас должны быть следующие файлы:

	* **Program.cs** - определяет топологию для проекта. Обратите внимание, что по умолчанию создается топология, состоящая из одной воронки и одного сита.

	* **Spout.cs** - пример воронки

	* **Bolt.cs** - пример сита. Он будет удален, так как вы будете использовать сито концентратора событий для записи в концентратор событий.

###Конфигурация

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

4. Сохраните и закройте страницу свойств.

###Определение топологии

1. В **обозревателе решений** щелкните правой кнопкой мыши **Bolt.cs** и выберите пункт **Удалить**. Поскольку вы используете сито концентратора событий Java, этот файл вам не нужен.

2. Откройте **pom.xml** и добавьте следующий код непосредственно после строки `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	Первая строка считывает количество разделов из определенных ранее свойств. Вторая строка определяет десериализатор, используемый для десериализации данных JSON, созданных воронкой, в `java.lang.String`, чтобы сито концентратора событий могло использовать эти данные.

4. Найдите следующий код.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Он создает воронку и использует количество разделов концентратора событий в качестве указания параллелизма для этого компонента. Этот код должен создавать экземпляр воронки для каждого раздела.
	
	Он также объявляет, что выходной поток должен использовать созданный ранее десериализатор.

5. Сразу после предыдущего кода добавьте следующий код.

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

	Он создает новый конструктор для сита Java, который используется для настройки нового экземпляра сита во время выполнения. В данном случае вы применяете <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>, чтобы настроить воронку с использованием добавленных ранее сведений о конфигурации концентратора событий. В частности, этот код используется HDInsight во время выполнения в следующих целях:

	* Создание нового экземпляра **com.microsoft.eventhubs.bolt.EventHubBoltConfig** с помощью указанных вами сведений о концентраторе событий.
	* Создание нового экземпляра **com.microsoft.eventhubs.bolt.EventHubBolt** посредством передачи экземпляра **EventHubBoltConfig**.

6. Найдите следующий код.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Replace it with the following.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Он дает топологии указание использовать **JavaComponentConstructor** из предыдущего шага в качестве сита. В этой топологии на данный компонент можно ссылаться по понятному имени "EventHubBolt". Указание параллелизма задается равным количеству разделов для концентратора событий и подписывается на данные, созданные воронкой ("Spout").

Работа с **Program.cs** завершена. Топология определена, однако теперь необходимо изменить **Spout.cs** таким образом, чтобы он создавал данные в формате, который может использовать сито концентратора событий.

###Изменение воронки

Сито концентратора событий ожидает одно строковое значение, которое перенаправит в концентратор событий. В следующем примере вы измените файл **Spout.cs** по умолчанию, чтобы получить строку JSON.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubWriter** и выберите пункт **Управление пакетами NuGet**. Найдите пакет **Json.Net**, а затем добавьте его в решение. Это позволит легко создавать данные JSON, которые будут отправлены в концентратор событий с помощью сита.

1. Откройте файл **Spout.cs** и добавьте в его начало следующий код.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Это упростит нам работу с данными JSON.

3. Найдите следующий код.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Замените его следующим кодом.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Он изменяет определение данных, созданных воронкой, чтобы использовать данные типа **string** и **настраиваемый сериализатор JSON**.

2. Замените метод **NextTuple** на следующий код:

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Он будет случайным образом создавать код устройства, значение, а затем использовать Json.net для создания объекта JSON с помощью этих значений.

3. Сохраните файл **Spout.cs**.

На данном этапе у вас имеется базовая топология, которая будет создавать случайные данные и сохранять их в концентратор событий с помощью сита концентратора событий. Далее вы создадите модуль чтения.

##Создание EventHubReader

В этом разделе вы создадите топологию, которая считывает данные из концентратора событий с помощью воронки концентратора событий.

2. Откройте Visual Studio, выберите **Файл**, **Создать**, а затем - **Проект**.

3. В диалоговом окне **Новый проект** разверните **Установленные**, **Шаблоны** и выберите **HDInsight**. В списке шаблонов выберите **Приложение Storm**. В нижней части диалогового окна, введите имя приложения **EventHubReader**.

###Конфигурация

1. В **обозревателе решений** щелкните правой кнопкой мыши **EventHubReader** и выберите пункт **Свойства**.

2. В свойствах проекта выберите **Параметры** и выберите **Этот проект не содержит файл параметров по умолчанию. Щелкните здесь, чтобы создать такой файл.**

3. Введите следующие параметры. Используйте сведения для концентратора событий и учетной записи хранения, созданных ранее, в столбце **Значение**.

	<table>
	<tr><th style="text-align:left">Имя</th><th style="text-align:left">Тип</th><th style="text-align:left">Область</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Приложение</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(строка подключения)</th><th style="text-align:left">Приложение</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">Приложение</th></tr>
	</table>

	Для **TableName** введите имя таблицы, в которой должны храниться события.

	Эти значения будут использоваться топологией для связи с концентратором событий и табличным хранилищем.

4. Сохраните и закройте страницу свойств.

###Определение топологии

1. В **обозревателе решений** щелкните правой кнопкой мыши **Spout.cs** и выберите пункт **Удалить**. Поскольку вы используете воронку концентратора событий Java, этот файл вам не нужен.

2. Откройте **pom.xml** и добавьте следующий код непосредственно после строки `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	Считывается количество разделов, и соответствующее значение присваивается локальной переменной, так как будет использоваться несколько раз.

	 `JavaComponentConstructor` определяет, как будет создаваться воронка Java во время выполнения. В данном случае вы применяете <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>, чтобы настроить воронку с использованием добавленных ранее сведений о конфигурации концентратора событий. В частности, этот код используется HDInsight во время выполнения в следующих целях:

	* Создание нового экземпляра **com.microsoft.eventhubs.spout.EventHubSpoutConfig** с помощью указанных вами сведений о концентраторе событий.
	
	* Создание нового экземпляра **com.microsoft.eventhubs.spout.EventHubSpout** посредством передачи экземпляра **EventHubSpoutConfig**.

5. Найдите следующий код.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	Он дает топологии указание использовать **JavaComponentConstructor** из предыдущего шага в качестве воронки и назначает ей имя "EventHubSpout". Он также задает указание параллелизма равным количеству разделов в концентраторе событий. 

2. Добавьте следующий код сразу после предыдущего кода.

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Он создает настраиваемый сериализатор, который будет использоваться для сериализации данных, созданных воронкой Java, в формат JSON.

3. Найдите следующий код.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Замените его следующим кодом.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Он дает топологии указание использовать сито (определенное в файле Bolt.cs). Такие дополнения требуют от топологии использовать настраиваемый сериализатор для данных, используемых этим компонентом, который предоставляется из **EventHubSpout** - воронки Java.

Работа с **Program.cs** завершена. Топология определена, однако теперь необходимо создать вспомогательный класс для записи данных в табличное хранилище, после чего необходимо изменить файл **Bolt.cs**, чтобы он мог распознать созданные воронкой данные.

###Создание вспомогательного класса

При записи данных в табличное хранилище необходимо создать класс, описывающий данные, которые будут записаны.

1. В **обозревателе решений** щелкните правой кнопкой мыши **EventHubReader**, а затем выберите пункт **Добавить**, **Новый класс**. Присвойте новому классу имя **Devices.cs**.

2. Откройте файл **Devices.cs** и замените существующий код следующим кодом.

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

###Изменение сита

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubReader** и выберите пункт **Управление пакетами NuGet**. Найдите пакет **Json.Net**, а затем добавьте его в решение. Это позволит нам легко обрабатывать данные JSON, полученные из воронки. Кроме того, добавьте пакет **хранилища Windows Azure**, который позволит нам выполнять запись в табличное хранилище.

1. Откройте файл **Bolt.cs** и добавьте в его начало следующий код.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Это упростит нам работу с данными JSON из сита и позволит записывать данные в табличное хранилище.

2. Найдите инструкцию `private int count;` и замените ее следующим кодом.

        private CloudTable table;

	Он будет использоваться при подключении к таблице.

4. Найдите следующий код.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Замените его следующим кодом.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Он указывает ситу на то, что оно будет получать значение **string** вместо **int**, а что данные следует десериализировать с помощью **CustomizedInteropJSONDeserialzer**.

3. Сразу после предыдущего кода добавьте следующий код.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Он будет выполнять подключение к таблице **events** с помощью предварительно настроенной строки подключения. Если она не существует, то будет создана.

2. Найдите метод **Execute** и замените его следующим кодом.

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
            }
        }

	Он использует Json.net для синтаксического анализа данных JSON из воронки, а затем выбирает поля **deviceId** и **deviceValue**. Затем создается новый объект **Device**, при этом время инициализации используется **deviceId**, чтобы задать ключ раздела для таблицы. После этого присваивается значение **deviceValue** и, наконец, сущность вставляется в таблицу.

На этом этапе у вас имеется полная топология, которая будет считывать данные из концентратора событий и сохранять их в таблице **events** табличного хранилища.

##Развертывание топологий

1. Откройте решение **EventHubReader**. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubReader** и выберите пункт **Отправить в Storm в HDInsight**.

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. В диалоговом окне **Отправка топологии** выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Пути к файлам Java**, элемент **...** и укажите каталог, содержащий скаченный ранее файл **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. Наконец, выберите **Отправить**.

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. После отправки топологии отображается **средство просмотра топологий Storm**. Выберите топологию **EventHubReader** в левой части диалогового окна, чтобы просмотреть статистику для топологии. В настоящее время все должно быть пусто, так как в концентратор событий еще не было записано ни одного события.

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Откройте решение **EventHubWriter**. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubWriter** и выберите пункт **Отправить в Storm в HDInsight**.

2. В диалоговом окне **Отправка топологии** выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Пути к файлам Java**, элемент **...** и укажите каталог, содержащий скаченный ранее файл **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. Наконец, выберите **Отправить**.

5. После отправки топологии обновите список топологий в **средстве просмотра топологий Storm**, чтобы убедиться, что обе топологии выполняются в кластере.

6. После запуска обеих топологий выберите **Обозреватель серверов**, затем разверните узел **Azure**, **Хранилище** и созданный ранее узел учетной записи хранения. Внутри учетной записи хранения разверните узел **Таблицы**. Наконец, дважды щелкните таблицу **events**, чтобы открыть ее. Вы должны видеть данные, сохраненные в этой таблице из топологии **EventHubReader**.

	* События создаются топологией **EventHubWriter**, которая записывает их в концентратор событий.

	* Затем **EventHubReader** считывает события из концентратора событий и сохраняет их в таблице **events** табличного хранилища.

##Остановка топологий

Чтобы остановить топологии, выберите каждую из них в **средстве просмотра топологий Storm**, а затем выберите элемент **Удалить**.

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##Сводка

В этом документе было рассмотрено использование воронки и сита концентратора событий Java из топологии C# для работы с данными в концентраторе событий Azure. Дополнительные сведения о создании топологий C# см. в следующих статьях.

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/)

* [Примеры HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->

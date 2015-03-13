<properties
   pageTitle="Анализ популярных тем Twitter с помощью Apache Storm в HDInsight | Azure"
   description="Информация об использовании Trident для создания топологии Storm, которая определяет популярные темы по хэш-тегам."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Анализ популярных тем Twitter с помощью Apache Storm в HDInsight

Информация об использовании Trident для создания топологии Storm, которая определяет популярные темы (хэш-теги) в Twitter. 

Trident - это высокоуровневая абстракция, которая предоставляет такие средства, как соединения, статистические выражения, группирования, функций и фильтры. Кроме того, Trident добавляет примитивы для выполнения добавочной обработки с отслеживанием состояния. В этом примере показано, как создать топологию с помощью пользовательской воронки, функции и несколько встроенных функций, предусмотренных в Trident.

> [AZURE.NOTE] Этот пример тесно связан с примером [trident-storm](https://github.com/jalonsoramos/trident-storm) Хуана Алонсо (Juan Alonso).

##Требования

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java и JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Учетная запись разработчика Twitter

##Скачивание проекта

Используйте следующую команду для локального клонирования проекта.

	git clone https://github.com/Blackmist/TwitterTrending

##Топология

Топология для этого примера выглядит следующим образом:

![topology](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Это упрощенное представление топологии - несколько экземпляров компонентов будут распределены между узлами в кластере.

Реализующий топологию код Trident выглядит следующим образом:

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

Он выполняет следующие функции:

1. Создание нового потока из воронки. Воронка извлекает из Twitter твиты, отфильтрованные по определенным ключевым словам (в этом примере это love, music и coffee).

2. Пользовательская функция HashtagExtractor используется для извлечения хэш-тегов из каждого твита. Они отправляются в поток.

3. Затем поток группируется по хэш-тегам и передается агрегатору. Агрегатор создает частоту встречаемости каждого хэш-тега, храня эти данные в памяти. Наконец создается новый поток, содержащий хэш-тег и количество вхождений.

4. Так как нас интересует только наиболее популярные хэш-теги для данного пакета твитов, применяется сборка FirstN, возвращающая только первые 10 значений, определяемые по полю количества вхождений.

> [AZURE.NOTE] Помимо воронки и HashtagExtractor мы используем также встроенные функции Trident.
> 
> Информацию о встроенных операциях см. в описании <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.Trident.Operation.Builtin</a>.
> 
> Информацию о внедрении состояния Trident, кроме MemoryMapState, см. по следующим ссылкам:
> 
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">https://github.com/fhussonnois/storm-trident-elasticsearch</a>
> 
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">https://github.com/kstyrc/trident-redis</a>

###Воронка

Воронка **TwitterSpout** использует <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> для получения твитов из Twitter. Создается фильтр (love, music и coffee), а входящие твиты (состояния), соответствующие фильтру, сохраняются в <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue</a>. Наконец элементы извлекаются из очереди и передаются в топологию.

###HashtagExtractor

Чтобы извлечь хэш-теги, используется <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities,</a> позволяющий получить все хэш-теги, содержащиеся в твитах. Затем они отправляются в поток.

##Включение Twitter

Чтобы зарегистрировать новое приложение Twitter и получить информацию о ключе потребителя и маркере доступа, необходимую для чтения из Twitter, сделайте следующее.

1. Перейдите на страницу <a href="" target="_blank">https://apps.twitter.com/</a> и воспользуйтесь кнопкой **Create new app** (Создать новое приложение). После заполнения формы, оставьте поле **Callback URL** (URL-адрес обратного вызова) пустым.

2. После создания приложения перейдите на вкладку **Keys and Access Tokens** (Ключи и маркеры доступа).

3. Скопируйте **ключ потребителя** и **секрет потребителя**.

4. В нижней части страницы выберите **Create my access token** (Создать маркер доступа), если маркеры не существуют. После создания маркеров скопируйте **маркер доступа** и **секрет маркера доступа**.

5. В клонированном ранее проекте **TwitterSpoutTopology** откройте файл **resources/twitter4j.properties** и добавьте информацию, собранную на предыдущих шагах, а затем сохраните файл.

##Сборка топологии

Используйте следующий код для сборки проекта.

		cd [directoryname]
		mvn compile

##Тестирование топологии

Используйте следующую команду для локального тестирования топологии.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

После запуска топологии вы увидите отладочную информацию, содержащую хэш-теги количество вхождений, отправленные топологией. Результат должен выглядеть аналогично следующему:

	DEBUG: [Quicktellervalentine, 7]
	DEBUG: [GRAMMYs, 7]
	DEBUG: [AskSam, 7]
	DEBUG: [poppunk, 1]
	DEBUG: [rock, 1]
	DEBUG: [punkrock, 1]
	DEBUG: [band, 1]
	DEBUG: [punk, 1]
	DEBUG: [indonesiapunkrock, 1]

##Дальнейшие действия

Теперь, после локального тестирования топологии нужно узнать, как [развернуть эту топологию в Storm в HDInsight](../hdinsight-storm-deploy-monitor-topology/).

Возможно, вас также заинтересуют следующие разделы по Storm:

* [Разработка топологий на Java для Storm в HDInsight с помощью Maven](../hdinsight-storm-develop-java-topology/)

* [Разработка топологий для Storm в HDInsight на C# с помощью Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/)
<!--HONumber=45--> 

---
title: "Определение популярных тем в Twitter с помощью Apache Storm в HDInsight | Документация Майкрософт"
description: "Узнайте, как с помощью Trident можно создать топологию Apache Storm, которая будет определять популярные темы в Twitter на основе хэш-тегов."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 63b280ea-5c07-4dc8-a35f-dccf5a96ba93
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53c18f6bb294c42456a0a4cd3c2a83812e9b13d0


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Определение популярных тем в Twitter с помощью Apache Storm в HDInsight

Узнайте, как с помощью Trident можно создать топологию Storm, которая будет определять популярные темы (хэш-теги) в Twitter.

Trident — это высокоуровневая абстракция, которая предоставляет такие средства, как соединения, функции, фильтры, агрегирование и группирование. Кроме того, Trident добавляет примитивы для выполнения добавочной обработки с отслеживанием состояния. В этом примере показано, как с помощью пользовательской воронки, функции и нескольких предусмотренных в Trident встроенных функций создать топологию.

> [!NOTE]
> Этот пример тесно связан с примером [Trident Storm](https://github.com/jalonsoramos/trident-storm) Хуана Алонсо (Juan Alonso).


## <a name="requirements"></a>Требования
* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java и пакет JDK 1.7</a>
* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>
* <a href="http://git-scm.com/" target="_blank">Git</a>
* Учетная запись разработчика Twitter

## <a name="download-the-project"></a>Скачивание проекта
С помощью приведенного ниже кода клонируйте проект локально.

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="topology"></a>Топология
Топология для этого примера выглядит следующим образом:

![Топология](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> Это очень упрощенная схема топологии. Между узлами в кластере будут распределены несколько экземпляров компонентов.


Реализующий топологию код Trident выглядит следующим образом:

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Этот код делает следующее:

1. Создает новый поток из воронки. Воронка извлекает из Твиттера твиты и фильтрует их по определенным ключевым словам (в нашем примере это love, music и coffee).
2. Пользовательская функция HashtagExtractor извлекает из каждого твита хэш-теги. Они отправляются в поток.
3. Поток группируется по хэш-тегам и передается в агрегатор. Агрегатор определяет количество вхождений каждого хэш-тега. Эти данные сохраняются в памяти. Наконец отправляется новый поток, содержащий хэш-тег и количество вхождений.
4. Так как нас интересуют только наиболее популярные хэш-теги в данном пакете твитов, применяется сборка **FirstN** , которая возвращает только первые 10 значений, определяемые по полю количества вхождений.

> [!NOTE]
> Помимо воронки и HashtagExtractor мы используем также встроенные функции Trident.
> 
> Сведения о встроенных операциях см. в статье <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">Пакет storm.trident.operation.builtin</a>.
> 
> Информацию о внедрении состояния Trident, кроме MemoryMapState, см. по следующим ссылкам:
> 
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Гибкий поиск с помощью Trident Storm</a>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">trident-redis</a>
> 
> 

### <a name="the-spout"></a>Воронка
Для извлечения твитов из Twitter воронка **TwitterSpout** использует библиотеку <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a>. Создается фильтр (в нашем примере это love, music и coffee), и соответствующие ему входящие твиты (статусы) сохраняются в связанной блокирующей очереди. (Дополнительные сведения см. в статье <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Класс LinkedBlockingQueue</a>.) Наконец элементы извлекаются из очереди и передаются в топологию.

### <a name="the-hashtagextractor"></a>HashtagExtractor
Для извлечения хэш-тегов используется метод <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a>. Он позволяет получить все содержащиеся в твитах хэш-теги. Затем они отправляются в поток.

## <a name="enable-twitter"></a>Включение Twitter
Чтобы зарегистрировать новое приложение для Твиттера и получить ключ потребителя и маркер доступа, необходимые для чтения из Твиттера, сделайте вот что.

1. Перейдите на страницу <a href="https://apps.twitter.com" target="_blank">Twitter Apps</a> (Приложения для Twitter) и нажмите кнопку **Create new app** (Создать приложение). После заполнения формы оставьте поле **Callback URL** (URL-адрес обратного вызова) пустым.
2. Когда приложение создано, перейдите на вкладку **Keys and Access Tokens** (Ключи и маркеры доступа).
3. Скопируйте **ключ** и **секрет клиента**.
4. Если у вас нет маркеров, в нижней части страницы выберите **Create my access token** (Создать мой маркер доступа). Создав маркеры, скопируйте **маркер доступа** и **секрет маркера доступа**.
5. В клонированном ранее проекте **TwitterSpoutTopology** откройте файл **resources/twitter4j.properties** и добавьте в него сведения, полученные на предыдущих этапах. После этого сохраните файл.

## <a name="build-the-topology"></a>Сборка топологии
Скомпилируйте проект с помощью следующего кода:

        cd [directoryname]
        mvn compile

## <a name="test-the-topology"></a>Тестирование топологии
Чтобы локально протестировать топологию, используйте следующую команду:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

После запуска топологии вы должны увидеть отладочную информацию, содержащую хэш-теги и количество их вхождений. Эти данные отправляет топология. Результат должен выглядеть аналогично следующему:

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

## <a name="next-steps"></a>Дальнейшие действия
Протестировав топологию локально, вы можете ее развернуть. Сведения о том, как это сделать, см. в статье [Развертывание топологий Apache Storm в HDInsight под управлением Windows и управление ими](hdinsight-storm-deploy-monitor-topology.md).

Возможно, вас также заинтересуют следующие разделы по Storm:

* [Разработка топологий на Java для Storm в HDInsight с помощью Maven](hdinsight-storm-develop-java-topology.md)
* [Разработка топологий для Storm в HDInsight на C# с помощью Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Дополнительные примеры Storm для HDinsight:

* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->



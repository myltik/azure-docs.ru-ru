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
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d588221586f151319436525c5098b0bb2694e5f9
ms.lasthandoff: 04/17/2017


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Определение популярных тем в Twitter с помощью Apache Storm в HDInsight

Узнайте, как с помощью Trident можно создать топологию Storm, которая будет определять популярные темы (хэш-теги) в Twitter.

Trident — это высокоуровневая абстракция, которая предоставляет такие средства, как соединения, функции, фильтры, агрегирование и группирование. Кроме того, Trident добавляет примитивы для выполнения добавочной обработки с отслеживанием состояния. В качестве примера в этом документе приведена топология Trident с пользовательскими spout и функцией. В ней также используется несколько встроенных функций Trident.

## <a name="requirements"></a>Требования

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java и пакет JDK 1.8</a>.

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Учетная запись разработчика Twitter

## <a name="download-the-project"></a>Скачивание проекта

С помощью приведенного ниже кода клонируйте проект локально.

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="understanding-the-topology"></a>Общие сведения о топологии

На следующей схеме показано, как данные перемещаются в топологии.

![Топология](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> Это упрощенное представление топологии. Между узлами в кластере распределено несколько экземпляров компонентов.


Реализующий топологию код Trident выглядит следующим образом:

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Этот код выполняет следующие действия.

1. Создает поток из spout. Воронка извлекает из Твиттера твиты и фильтрует их по определенным ключевым словам (в нашем примере это love, music и coffee).

2. Пользовательская функция HashtagExtractor извлекает из каждого твита хэш-теги. Хэш-теги отправляются в поток.

3. Поток группируется по хэш-тегам и передается в агрегатор. Агрегатор определяет количество вхождений каждого хэш-тега. Эти данные сохраняются в памяти. Наконец отправляется новый поток, содержащий хэш-тег и количество вхождений.

4. Применяется сборка **FirstN** для возврата только первых 10 значений по полю количества.

> [!NOTE]
> Дополнительные сведения о работе с Trident см. в документе [Trident API overview](http://storm.apache.org/releases/current/Trident-API-Overview.html) (Обзор API Trident).

### <a name="the-spout"></a>Воронка

Для извлечения твитов из Twitter воронка **TwitterSpout** использует библиотеку [Twitter4j](http://twitter4j.org/en/). Создается фильтр для слов __love__, **music** и **coffee**. Входящие твиты (состояния), соответствующие фильтру, сохраняются в связанной блокирующей очереди. Наконец элементы извлекаются из очереди и передаются в топологию.

### <a name="the-hashtagextractor"></a>HashtagExtractor

Для извлечения хэш-тегов используется метод [getHashtagEntities](http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--). Он позволяет получить все содержащиеся в твитах хэш-теги. Затем они отправляются в поток.

## <a name="configure-twitter"></a>Настройка Twitter

Чтобы зарегистрировать новое приложение для Твиттера и получить ключ потребителя и маркер доступа, необходимые для чтения из Твиттера, сделайте вот что.

1. Перейдите на страницу [Twitter Apps](https://apps.twitter.com) (Приложения для Twitter) и нажмите кнопку **Create new app** (Создать приложение). После заполнения формы оставьте поле **Callback URL** (URL-адрес обратного вызова) пустым.

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

После запуска топологии вы должны увидеть отладочную информацию, содержащую хэш-теги и количество их вхождений. Эти данные отправляет топология. Результат должен выглядеть следующим образом.

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



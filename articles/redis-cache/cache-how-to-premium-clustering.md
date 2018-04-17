---
title: Как настроить кластеризацию для кэша Redis для Azure уровня "Премиум" | Документация Майкрософт
description: Узнайте, как настроить кластеризацию и управлять ей для экземпляров кэша Redis для Azure уровня Премиум
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: wesmc
ms.openlocfilehash: 4af6545058ab0031d7cd1b38618b6d80204f83b9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-redis-cache"></a>Настройка кластеризации для кэша Redis для Azure уровня Премиум
Кэш Redis для Azure предлагает разные варианты кэша, которые обеспечивают гибкость в выборе размера и функций кэша, включая функции уровня "Премиум", такие как кластеризация, постоянное хранение данных и поддержка виртуальной сети. В этой статье описывается настройка кластеризации в экземпляре кэша Redis для Azure уровня Премиум.

Сведения о других функциях кэша уровня "Премиум" см. в статье [Знакомство с кэшем Redis для Azure уровня Премиум](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Что такое кластер Redis?
Кэш Redis для Azure предлагает кластер Redis в том виде, как это [реализовано в Redis](http://redis.io/topics/cluster-tutorial). При использовании кластера Redis вы получаете такие преимущества: 

* Возможность автоматически разделить набор данных между несколькими узлами. 
* Возможность продолжить работу, когда на подмножестве узлов возникают ошибки или узлы не могут обмениваться данными с остальной частью кластера. 
* Более высокая пропускная способность: пропускная способность линейно увеличивается по мере увеличения числа сегментов. 
* Больший объем памяти: объем памяти линейно увеличивается по мере увеличения числа сегментов.  

Кластеризация не увеличивает число подключений, доступных для кластеризованного кэша. Дополнительные сведения о размере, пропускной способности и полосе пропускания для кэшей уровня "Премиум" см. в разделе [Какое предложение и размер кэша Redis нужно использовать?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

В Azure кластер Redis предоставляется в виде модели основного экземпляра и реплики. У каждого сегмента есть пара основного экземпляра и реплики. Репликацией управляет служба кэша Redis для Azure. 

## <a name="clustering"></a>Кластеризация
Кластер включается во время создания кэша в колонке **Новый кэш Redis** . 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Кластер настраивается в колонке **Кластер Redis** .

![Кластеризация][redis-cache-clustering]

Кластер может включать до 10 сегментов. Щелкните **Включено**, передвиньте ползунок или введите число от 1 до 10 для поля **Число сегментов**, после чего нажмите кнопку **ОК**.

Каждый сегмент представляет собой пару основного экземпляра кэша и реплики кэша, которыми управляет Azure. Общий размер кэша вычисляется путем умножения количества сегментов на размер кэша, выбранный в ценовой категории. 

![Кластеризация][redis-cache-clustering-selected]

Создав кэш, вы подключаетесь к нему, используя как некластеризованный кэш, а Redis распределяет данные между его сегментами. Если диагностика [включена](cache-how-to-monitor.md#enable-cache-diagnostics), то метрики собираются отдельно для каждого сегмента и [отображаются](cache-how-to-monitor.md) в колонке кэша Redis. 

> [!NOTE]
> 
> Существует ряд незначительных отличий, необходимых в клиентском приложении для настройки кластеризации. Дополнительные сведения см. в разделе [Нужно ли вносить изменения в клиентское приложение, чтобы использовать кластеризацию?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

Пример кода по работе с кластеризацией клиента StackExchange.Redis см. в разделе [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) примера [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Изменение размера кластера на работающем кэше категории "Премиум"
Чтобы изменить размер кластера для работающего кэша уровня "Премиум" с включенной кластеризацией, в **меню ресурсов** щелкните **Размер кластера Redis**.

> [!NOTE]
> Хотя кэш Redis для Azure уровня "Премиум" предоставляется в общедоступной версии, сейчас функция изменения размера кластера Redis доступна в режиме предварительной версии.
> 
> 

![Размер кластера Redis][redis-cache-redis-cluster-size]

Чтобы изменить размер кластера, воспользуйтесь ползунком или введите число от 1 до 10 в текстовом поле **Shard count** (Количество сегментов), а затем нажмите кнопку **ОК** для сохранения изменений.

Увеличение размера кластера увеличивает максимальную пропускную способность и размер кэша. Увеличение размера кластера не приводит к увеличению максимального количества . подключений, доступных для клиентов.

> [!NOTE]
> Масштабирование кластера запускает ресурсоемкую команду [MIGRATE](https://redis.io/commands/migrate). Чтобы избежать перегрузки, мы не рекомендуем выполнять эту операцию в рабочие часы. Во время миграции вы заметите резкое увеличение нагрузки на сервер. Масштабирование кластера — это длительный процесс, время выполнения которого зависит от числа ключей и размера значений, связанных с этими ключами.
> 
> 

## <a name="clustering-faq"></a>Часто задаваемые вопросы по кластеризации
В следующем списке приведены ответы на часто задаваемые вопросы о кластеризации кэша Redis для Azure.

* [Нужно ли вносить изменения в клиентское приложение, чтобы использовать кластеризацию?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Распределение ключей в кластере](#how-are-keys-distributed-in-a-cluster)
* [Каков максимальный размер кэша, который можно создать?](#what-is-the-largest-cache-size-i-can-create)
* [Все ли клиенты Redis поддерживают кластеризацию?](#do-all-redis-clients-support-clustering)
* [Как подключиться к кэшу, если кластеризация включена?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Можно ли напрямую подключаться к отдельным сегментам кэша?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Можно ли настроить кластеризацию для ранее созданного кэша?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Можно ли настроить кластеризацию для кэша уровней Базовый и Стандартный?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Можно ли использовать кластеризацию с поставщиками состояний сеансов и кэширования выходных данных ASP.NET Redis?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [При использовании StackExchange.Redis и кластеризации порождаются исключения MOVE. Что делать?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Нужно ли вносить изменения в клиентское приложение, чтобы использовать кластеризацию?
* Если кластеризация включена, доступна только база данных 0. Если клиентское приложение использует несколько баз данных и пытается выполнить чтение или запись в базе данных, отличной от 0, порождается приведенное ниже исключение. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Дополнительные сведения см. в разделе "Implemented subset" (Реализованное подмножество) статьи [Redis Cluster Specification](http://redis.io/topics/cluster-spec#implemented-subset) (Спецификация кластера Redis).
* Если вы используете клиент [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), необходимо установить версию 1.0.481 или более позднюю. Вы можете подключаться к кэшу с помощью тех же [конечных точек, портов и ключей](cache-configure.md#properties) , которые используются для подключения к кэшу с отключенной кластеризацией. Единственное отличие заключается в том, что все операции чтения и записи должны выполняться в базе данных 0.
  
  * Требования других клиентов могут отличаться. Ознакомьтесь с разделом [Все ли клиенты Redis поддерживают кластеризацию?](#do-all-redis-clients-support-clustering)
* Если приложение использует несколько операций с ключом, объединенных в одну команду, все ключи должны быть расположены в одном сегменте. Чтобы найти ключи, расположенные в одном сегменте, см. раздел [Как ключи распределены в кластере?](#how-are-keys-distributed-in-a-cluster)
* Если вы используете поставщик состояний сеансов ASP.NET Redis, вам необходимо установить версию 2.0.1 или более позднюю версию. Ознакомьтесь с разделом [Можно ли использовать кластеризацию с поставщиками состояний сеансов и кэширования выходных данных ASP.NET Redis?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Распределение ключей в кластере
В соответствии с документацией по [модели распределения ключей](http://redis.io/topics/cluster-spec#keys-distribution-model) в Redis пространство ключей разбивается на 16 384 слота. Каждый ключ хэшируется и присваивается одному из этих слотов, распределенных между узлами кластера. С помощью хэш-тегов вы можете указать хэшируемую часть ключа, чтобы убедиться в том, что несколько ключей находятся в одном сегменте.

* Ключи с хэш-тегом. Если любая часть ключа заключена в фигурные скобки — `{` и `}`, — для определения хэш-слота ключа хэшируется только эта часть. Например, три ключа — `{key}1`, `{key}2` и `{key}3` — будут расположены в одном сегменте, так как хэшируется только часть имени `key`. Полный список спецификаций для хэш-тегов ключей см. в разделе [Keys hash tags](http://redis.io/topics/cluster-spec#keys-hash-tags) (Хэш-теги ключей).
* Ключи без хэш-тега. Для хэширования используется полное имя ключа. Это позволяет достичь статистически равномерного распределения по сегментам кэша.

Для оптимальной производительности и пропускной способности рекомендуется равномерно распределять ключи. Если вы используете ключи с хэш-тегом, приложение должно обеспечивать равномерное распределение ключей.

Дополнительные сведения см. в разделах [Keys distribution model](http://redis.io/topics/cluster-spec#keys-distribution-model) (Модель распределения ключей), [Redis Cluster data sharding](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) (Сегментирование данных в кластере Redis) и [Keys hash tags](http://redis.io/topics/cluster-spec#keys-hash-tags) (Хэш-теги ключей).

Пример кода по работе с кластеризацией и поиска ключей в одном сегменте для клиента StackExchange.Redis см. в части [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) примера [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Каков максимальный размер кэша, который можно создать?
Максимальный размер кэша для уровня Премиум — 53 ГБ. Можно создать до 10 сегментов, таким образом общий максимальный размер составит 530 ГБ. Если требуется больший размер, вы можете [отправить запрос на получение дополнительного места](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Подробные сведения см. на странице [Цены на кэш Redis для Azure](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Все ли клиенты Redis поддерживают кластеризацию?
В настоящее время не все клиенты Redis поддерживают кластеризацию. Например, ее не поддерживает StackExchange.Redis. Дополнительные сведения о других клиентах см. в разделе [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (Эксперименты с кластером) [руководства по кластерам Redis](http://redis.io/topics/cluster-tutorial).

> [!NOTE]
> Если вы используете клиент [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), убедитесь, что у вас установлена версия 1.0.481 или более поздняя. Это необходимо для правильного выполнения кластеризации. При возникновении проблем с исключениями MOVE ознакомьтесь с дополнительными сведениями [здесь](#move-exceptions).
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Как подключиться к кэшу, если кластеризация включена?
Вы можете подключаться к кэшу с помощью тех же [конечных точек](cache-configure.md#properties), [портов](cache-configure.md#properties) и [ключей](cache-configure.md#access-keys), которые используются для подключения к кэшу с отключенной кластеризацией. Redis управляет кластеризацией на сервере, поэтому управлять ей из клиента не нужно.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Можно ли напрямую подключаться к отдельным сегментам кэша?
Официально это не поддерживается. Тем не менее каждый сегмент включает пару, которая включает основной кэш и его реплику и называется экземпляром кэша. Вы можете подключиться к этим экземплярам кэша с помощью служебной программы redis-cli из ветви [нестабильных версий](http://redis.io/download) репозитория Redis на портале GitHub. Эта версия реализует базовую поддержку при запуске с параметром `-c`. Дополнительные сведения см. в разделе [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (Эксперименты с кластером) [руководства по кластерам Redis](http://redis.io/topics/cluster-tutorial) на сайте [http://redis.io](http://redis.io).

Для non-ssl используйте следующие команды.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Для SSL замените `1300N` на `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Можно ли настроить кластеризацию для ранее созданного кэша?
В настоящее время включить кластеризацию можно только при создании кэша. Размер кластера вы можете изменить после создания кэша, но нельзя добавить кластеризацию в кэш категории "Премиум" или удалить кластеризацию из него после создания кэша. Кэш категории "Премиум" с включенной кластеризацией и только одним сегментом отличается от кэша категории "Премиум" такого же размера, но без кластеризации.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Можно ли настроить кластеризацию для кэша уровней Базовый и Стандартный?
Кластеризация доступна только для кэша уровня Премиум.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Можно ли использовать кластеризацию с поставщиками состояний сеансов и кэширования выходных данных ASP.NET Redis?
* **Поставщик кэша вывода Redis** — изменения не требуются.
* **Поставщик состояний сеансов Redis** — для кластеризации необходимо использовать [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 или более поздней версии. В противном случае будет порождено исключение. Это критическое изменение. Дополнительные сведения см. на странице [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details) (Подробные сведения о критических изменениях в версии 2.0.0).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>При использовании StackExchange.Redis и кластеризации порождаются исключения MOVE. Что делать?
Если вы применяете StackExchange.Redis и получаете исключения `MOVE` при кластеризации, убедитесь, что вы используете [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) или более позднюю версию. Инструкции по настройке приложений .NET для использования StackExchange.Redis см. в разделе [Настройка клиентов кэша](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Дополнительная информация
Узнайте, как использовать расширенные функции кэша.

* [Знакомство с кэшем Redis для Azure уровня Премиум](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png








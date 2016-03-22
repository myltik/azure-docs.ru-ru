<properties 
	pageTitle="Примеры кэша Redis для Azure" 
	description="Сведения об использовании кэша Redis для Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Примеры кэша Redis для Azure 

Здесь представлен список примеров кэша Redis для Azure, охватывающих такие сценарии, как подключение к кэшу, чтение и запись данных в кэше и использование поставщиков кэша Redis ASP.NET. Некоторые примеры представляют собой скачиваемые проекты, а некоторые содержат пошаговые инструкции и фрагменты кода, но не ссылаются на скачиваемый проект.

## Примеры Hello World

Примеры в этом разделе показывают основные методы подключения к экземпляру кэша Redis для Azure, а также чтения и записи данных в кэше с помощью различных языков и клиентов Redis.

В образце [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) показано, как выполнять различные операции кэша с помощью клиента .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

В этом примере показано, как:

-	использовать различные параметры подключения;
-	читать и записывать объекты из кэша, используя синхронные и асинхронные операции;
-	использовать команды Redis MGET/MSET для возврата значений указанных ключей;
-	выполнять операции транзакций Redis;
-	работать со списками и отсортированными наборами Redis;
-	хранить объекты .NET с помощью сериализаторов JsonConvert;
-	использовать наборы Redis для реализации тегов.
-	Работа с кластером Redis

Дополнительные сведения см. в документации [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) на сайте github. Дополнительные сценарии использования см. в модульных тестах [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests).

Раздел [Использование кэша Redis для Azure с Python](cache-python-get-started.md) показывает, как приступить к работе с кэшем Redis для Azure с помощью Python и клиента [redis-py](https://github.com/andymccurdy/redis-py).

[Пример PHP](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) показывает, как приступить к использованию кэша Redis для Azure с помощью PHP и клиента [predis](https://github.com/nrk/predis).

Раздел [Работа с объектами .NET в кэше](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) показывает один из способов сериализации объектов .NET, чтобы можно было их записывать и считывать из экземпляра кэша Redis для Azure.

## Использование кэша Redis в качестве масштабируемой объединительной панели для ASP.NET SignalR

Пример [Использование кэша Redis в качестве масштабируемой объединительной панели для ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) демонстрирует, как можно использовать кэш Redis для Azure в качестве объединительной панели SignalR. Дополнительные сведения об объединительной панели см. в разделе [Масштабирование SignalR с помощью Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Пример запроса клиента кэша Redis

Этот пример сравнивает производительность при доступе к данным из кэша и при доступе к данным из хранилища сохраняемости. Этот пример содержит два проекта.

-	[Демонстрация повышения производительности путем кэширования данных с помощью кэша Redis](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Инициализация базы данных и кэша для демонстрации](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## Состояние сеанса ASP.NET и кэширование вывода

Пример [Использование кэша Redis для Azure для хранения ASP.NET SessionState и OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) показывает, как использовать кэш Redis для Azure для хранения сеанса ASP.NET и кэша вывода с использованием поставщиков SessionState и OutputCache для Redis.

## Управление кэшем Redis для Azure с помощью MAML

Пример [Управление кэшем Redis для Azure с помощью библиотек управления Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) показывает, как можно использовать библиотеки управления Azure для управления кэшем (создания/обновления/удаления).

## Пример настраиваемого мониторинга

Пример [Доступ к данным мониторинга кэша Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) показывает, как можно получать доступ к данным мониторинга кэша Redis для Azure за пределами портала Azure.

## Приложение в стиле Twitter, написанное с помощью PHP и Redis

Пример [Retwis](https://github.com/SyntaxC4-MSFT/retwis) — это Redis Hello World. Это минимальное приложение для социальных сетей в стиле Twitter, написанное с помощью Redis и PHP, а также с помощью клиента [Predis](https://github.com/nrk/predis). Исходный код задуман очень простым и в то же время таким, который сможет показать разные структуры данных Redis.

## Монитор пропускной способности

Пример [Монитор пропускной способности](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) позволяет наблюдать за пропускной способностью, используемой на клиентском компьютере. Чтобы измерить пропускную способность, запустите пример на клиентском компьютере кэша, выполните вызовы кэша и отследите пропускную способность, передаваемую примером монитора пропускной способности.

<!---HONumber=AcomDC_0309_2016-->
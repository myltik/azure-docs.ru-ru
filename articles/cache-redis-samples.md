<properties 
	pageTitle="Примеры кэша Redis для Azure" 
	description="Сведения об использовании кэша Redis для Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="sdanie"/>

# Примеры кэша Redis для Azure 

Здесь представлен список примеров кэша Redis для Azure, охватывающих такие сценарии, как подключение к кэшу, чтение и запись данных в кэше и использование поставщиков кэша Redis ASP.NET. Некоторые примеры представляют собой скачиваемые проекты, а некоторые содержат пошаговые инструкции и фрагменты кода, но не ссылаются на скачиваемый проект.

## Примеры Hello World

Примеры в этом разделе показывают основные методы подключения к экземпляру кэша Redis для Azure, а также чтения и записи данных в кэше с помощью различных языков и клиентов Redis.

Пример [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) показывает, как осуществлять чтение и запись элементов из кэша с помощью клиента .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

[Использование кэша Redis для Azure с Node.js](cache-nodejs-get-started.md) показывает, как приступить к работе с кэшем Redis для Azure с помощью Node.js и клиента [node_redis](https://github.com/mranney/node_redis).

[Использование кэша Redis для Azure с Java](cache-java-get-started.md) показывает, как приступить к работе с кэшем Redis для Azure с помощью Java и клиента [Jedis](https://github.com/xetorthio/jedis).

[Использование кэша Redis для Azure с Python](cache-python-get-started.md) показывает, как приступить к работе с кэшем Redis для Azure с помощью Python и клиента [redis-py](https://github.com/andymccurdy/redis-py).

[Пример PHP](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) показывает, как приступить к использованию кэша Redis для Azure с помощью PHP и клиента [predis](https://github.com/nrk/predis).

[Работа с объектами .NET в кэше](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) показывает один из способов сериализации объектов .NET, чтобы можно было записывать и считывать их из экземпляра кэша Redis для Azure. 

## Использование кэша Redis в качестве масштабируемой объединительной панели для ASP.NET SignalR

Пример [Использование кэша Redis в качестве масштабируемой объединительной панели для ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) показывает, как можно использовать кэш Redis для Azure в качестве объединительной панели для SignalR. Дополнительные сведения об объединительной панели см. в разделе [Масштабирование SignalR с помощью Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Пример запроса клиента кэша Redis

Этот пример сравнивает производительность при доступе к данным из кэша и при доступе к данным из хранилища сохраняемости. Этот пример содержит два проекта.

-	[Демонстрация того, как кэш Redis может повысить производительность путем кэширования данных](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Инициализация базы данных и кэша для демонстрации](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## Состояние сеанса ASP.NET и кэширование вывода

Пример [Использование кэша Redis для Azure для хранения состояния сеанса ASP.NET и кэша вывода](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) показывает, как использовать кэш Redis для Azure для хранения сеанса ASP.NET и кэша вывода с использованием поставщиков состояния сеанса и кэша вывода для Redis.

## Управление кэшем Redis для Azure с помощью MAML

Пример [Управление кэшем Redis для Azure с использованием библиотек управления Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) показывает, как можно использовать библиотеки управления Azure для управления кэшем (создания, обновления и удаления данных). 

## Пример настраиваемого мониторинга

Пример [Доступ к данным мониторинга кэша Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) показывает, как можно получить доступ к данным мониторинга для кэша Redis для Azure за пределами портала Azure.



<!--HONumber=49-->
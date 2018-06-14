---
title: Примеры кэша Redis для Azure | Документация Майкрософт
description: Сведения об использовании кэша Redis для Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 0b6f89807d3252b750bd5208a7f758a06c9903d6
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909702"
---
# <a name="azure-redis-cache-samples"></a>Примеры кэша Redis для Azure
Здесь представлен список примеров кэша Redis для Azure, охватывающих такие сценарии, как подключение к кэшу, чтение и запись данных в кэше и использование поставщиков кэша Redis ASP.NET. Некоторые примеры представляют собой скачиваемые проекты, а некоторые содержат пошаговые инструкции и фрагменты кода, но не ссылаются на скачиваемый проект.

## <a name="hello-world-samples"></a>Примеры Hello World
Примеры в этом разделе показывают основные методы подключения к экземпляру кэша Redis для Azure, а также чтения и записи данных в кэше с помощью различных языков и клиентов Redis.

В примере [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) показано, как выполнять различные операции кэша с помощью клиента .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

В этом примере показано, как:

* использовать различные параметры подключения;
* читать и записывать объекты из кэша, используя синхронные и асинхронные операции;
* использовать команды Redis MGET/MSET для возврата значений указанных ключей;
* выполнять операции транзакций Redis;
* работать со списками и отсортированными наборами Redis;
* хранить объекты .NET с помощью сериализаторов JsonConvert;
* использовать наборы Redis для реализации тегов.
* Работа с кластером Redis

Дополнительные сведения см. в документации по [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) на сайте GitHub. С дополнительными сценариями использования можно ознакомиться, изучив модульные тесты [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests).

В разделе [Использование кэша Redis для Azure с Python](cache-python-get-started.md) показано, как приступить к работе с кэшем Redis для Azure с помощью Python и клиента [redis-py](https://github.com/andymccurdy/redis-py).

[Работа с объектами .NET в кэше](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) показывает один из способов сериализации объектов .NET, чтобы можно было их записывать и считывать из экземпляра кэша Redis для Azure. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Использование кэша Redis в качестве масштабируемой объединительной панели для ASP.NET SignalR
Пример [использование кэша Redis в качестве масштабируемой объединительной панели SignalR для ASP.NET](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) демонстрирует, как можно использовать кэш Redis для Azure в качестве объединительной панели SignalR. Дополнительные сведения об объединительной панели см. в разделе [Масштабирование SignalR с помощью Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Пример запроса клиента кэша Redis
Этот пример сравнивает производительность при доступе к данным из кэша и при доступе к данным из хранилища сохраняемости. Этот пример содержит два проекта.

* [Демонстрация повышения производительности путем кэширования данных с помощью кэша Redis](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Инициализация базы данных и кэша для демонстрации](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Состояние сеанса ASP.NET и кэширование вывода
Пример [Использование кэша Redis для Azure для хранения ASP.NET SessionState и OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) показывает, как использовать кэш Redis для Azure для хранения сеанса ASP.NET и кэша вывода с использованием поставщиков SessionState и OutputCache для Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Управление кэшем Redis для Azure с помощью MAML
Пример [Управление кэшем Redis для Azure с помощью библиотек управления Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) показывает, как можно использовать библиотеки управления Azure для управления кэшем (создания/обновления/удаления). 

## <a name="custom-monitoring-sample"></a>Пример настраиваемого мониторинга
Пример [Доступ к данным мониторинга кэша Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) показывает, как можно получать доступ к данным мониторинга кэша Redis для Azure за пределами портала Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Приложение в стиле Twitter, написанное с помощью PHP и Redis
Пример [Retwis](https://github.com/SyntaxC4-MSFT/retwis) — это Redis Hello World. Это минимальное приложение для социальных сетей в стиле Twitter, написанное с помощью Redis и PHP, а также с помощью клиента [Predis](https://github.com/nrk/predis) . Исходный код задуман очень простым и в то же время таким, который сможет показать разные структуры данных Redis.

## <a name="bandwidth-monitor"></a>Монитор пропускной способности
Пример [Монитор пропускной способности](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) позволяет наблюдать за пропускной способностью, используемой на клиентском компьютере. Чтобы измерить пропускную способность, запустите пример на клиентском компьютере кэша, выполните вызовы кэша и отследите пропускную способность, передаваемую примером монитора пропускной способности.


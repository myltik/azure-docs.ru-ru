---
title: Использование кэша Redis для Azure с Node.js | Microsoft Docs
description: Начните работу с кэшем Redis для Azure с использованием Node.js и node_redis.
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: v-lincan

ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/16/2016
ms.author: sdanie

---
# Использование кэша Redis для Azure с Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Кэш Redis для Azure дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этой статье показано, как приступить к работе с кэшем Redis для Azure, используя Node.js. Еще один пример использования кэша Redis для Azure с Node.js см. в статье [Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).

## Предварительные требования
Установка [node\_redis](https://github.com/mranney/node_redis).

    npm install redis

В этом руководстве используется [node\_redis](https://github.com/mranney/node_redis). Примеры использования других клиентов Node.js см. в отдельных документах по [клиентам Node.js для Redis](http://redis.io/clients#nodejs).

## Создание кэша Redis в Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Получение имени узла и ключей доступа
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Безопасное подключение к кэшу с помощью SSL
Новейшие сборки [node\_redis](https://github.com/mranney/node_redis) обеспечивают поддержку подключения к кэшу Redis для Azure по протоколу SSL. В приведенном ниже примере показано, как подключиться к кэшу Redis для Azure с помощью конечной точки SSL на порту 6380. Подставьте вместо `<name>` имя своего кэша, а вместо `<key>` — первичный или вторичный ключ, как описано в предыдущем разделе [Получение имени узла и ключей доступа](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## Добавление данных в кэш и их извлечение
В следующем примере показано, как подключиться к экземпляру кэша Redis для Azure, а также как сохранить и извлечь элемент из кэша. Дополнительные примеры использования Redis с клиентом [node\_redis](https://github.com/mranney/node_redis) см. на странице [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Выходные данные:

    OK
    value


## Дальнейшие действия
* [Включите диагностику кэша](cache-how-to-monitor.md#enable-cache-diagnostics), чтобы можно было [наблюдать](cache-how-to-monitor.md) за работоспособностью кэша.
* Прочитайте официальную [документацию Redis](http://redis.io/documentation).

<!---HONumber=AcomDC_0817_2016-->
---
title: "Использование кэша Redis для Azure с Node.js | Документация Майкрософт"
description: "Начните работу с кэшем Redis для Azure с использованием Node.js и node_redis."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
ms.openlocfilehash: f2c448af24e180db58f3ef3d39e90036dda3f7eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Использование кэша Redis для Azure с Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Кэш Redis для Azure дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этой статье показано, как приступить к работе с кэшем Redis для Azure, используя Node.js. 

## <a name="prerequisites"></a>Предварительные требования
Установите [node_redis](https://github.com/mranney/node_redis).

    npm install redis

В этом руководстве используется [node_redis](https://github.com/mranney/node_redis). Примеры использования других клиентов Node.js см. в отдельных документах по [клиентам Node.js для Redis](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Создание кэша Redis в Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Получение имени узла и ключей доступа
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Безопасное подключение к кэшу с помощью SSL
Новейшие сборки [node_redis](https://github.com/mranney/node_redis) обеспечивают поддержку подключения к кэшу Redis для Azure по протоколу SSL. В приведенном ниже примере показано, как подключиться к кэшу Redis для Azure с помощью конечной точки SSL на порту 6380. Подставьте вместо `<name>` имя своего кэша, а вместо `<key>` — первичный или вторичный ключ, как описано в предыдущем разделе [Получение имени узла и ключей доступа](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> Порт без SSL отключен для новых экземпляров кэша Redis для Azure. Если вы используете другой клиент, не поддерживающий SSL, см. сведения в разделе [Порты доступа](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Добавление данных в кэш и их извлечение
В следующем примере показано, как подключиться к экземпляру кэша Redis для Azure, а также как сохранить и извлечь элемент из кэша. Дополнительные примеры использования Redis с клиентом [node_redis](https://github.com/mranney/node_redis) см. на странице [http://redis.js.org/](http://redis.js.org/).

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


## <a name="next-steps"></a>Дальнейшие действия
* [Включите диагностику кэша](cache-how-to-monitor.md#enable-cache-diagnostics), чтобы можно было [наблюдать](cache-how-to-monitor.md) за работоспособностью кэша.
* Прочитайте официальную [документацию Redis](http://redis.io/documentation).


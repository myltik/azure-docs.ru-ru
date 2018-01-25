---
title: "Как использовать кэш Redis для Azure с Java | Документация Майкрософт"
description: "Приступая к работе с кэшем Redis для Azure с использованием Java"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/31/2017
ms.author: wesmc
ms.openlocfilehash: a93dbf78c9cb94f6c20c8569a69e068ceb3b98bc
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Использование кэша Redis для Azure с Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Кэш Redis для Azure дает доступ к выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этом разделе показано, как приступить к работе с кэшем Redis для Azure, используя Java.

## <a name="prerequisites"></a>предварительным требованиям
[Jedis](https://github.com/xetorthio/jedis) — Java-клиент для Redis

В этом учебнике используется Jedis, но можно использовать любой клиент Java из перечисленных на сайте [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Создание кэша Redis в Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Получение имени узла и ключей доступа
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Безопасное подключение к кэшу с помощью SSL
Новейшие сборки [jedis](https://github.com/xetorthio/jedis) обеспечивают поддержку подключения к кэшу Redis для Azure по протоколу SSL. В приведенном ниже примере показано, как подключиться к кэшу Redis для Azure с помощью конечной точки SSL на порту 6380. Подставьте вместо `<name>` имя своего кэша, а вместо `<key>` — первичный или вторичный ключ, как описано в предыдущем разделе [Получение имени узла и ключей доступа](#retrieve-the-host-name-and-access-keys).

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> Порт без SSL отключен для новых экземпляров кэша Redis для Azure. Если вы используете другой клиент, не поддерживающий SSL, см. сведения в разделе [Порты доступа](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Добавление данных в кэш и их извлечение
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Дополнительная информация
* [Включите диагностику кэша](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), чтобы можно было [наблюдать](https://msdn.microsoft.com/library/azure/dn763945.aspx) за работоспособностью кэша.
* Прочитайте официальную [документацию Redis](http://redis.io/documentation).
* Узнайте, [как настроить приложение Spring Initializr для использования кэша Redis](cache-java-spring-boot-initializer-with-redis-cache.md).
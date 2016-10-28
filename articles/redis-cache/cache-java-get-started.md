<properties
   pageTitle="Использование кэша Redis для Azure с Java | Microsoft Azure"
	description="Приступая к работе с кэшем Redis для Azure с использованием Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/24/2016"
	ms.author="sdanie"/>

# Использование кэша Redis для Azure с Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Кэш Redis для Azure дает доступ к выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этом разделе показано, как приступить к работе с кэшем Redis для Azure, используя Java.

## Предварительные требования

[Jedis](https://github.com/xetorthio/jedis) — Java-клиент для Redis

В этом учебнике используется Jedis, но можно использовать любой клиент Java из перечисленных на сайте [http://redis.io/clients](http://redis.io/clients).

## Создание кэша Redis в Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Получение имени узла и ключей доступа

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Включение конечной точки без SSL

Некоторые клиенты Redis не поддерживают SSL. [Все порты, кроме SSL, отключены для новых экземпляров кэша Redis для Azure](cache-configure.md#access-ports) по умолчанию. На момент написания этой статьи клиент [Jedis](https://github.com/xetorthio/jedis) не поддерживает SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## Добавление данных в кэш и их извлечение

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## Дальнейшие действия

- [Включите диагностику кэша](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), чтобы можно было [наблюдать](https://msdn.microsoft.com/library/azure/dn763945.aspx) за работоспособностью кэша.
- Прочитайте официальную [документацию Redis](http://redis.io/documentation).

<!---HONumber=AcomDC_0824_2016-->
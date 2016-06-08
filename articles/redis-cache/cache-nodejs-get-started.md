<properties
	pageTitle="Использование кэша Redis для Azure с Node.js | Microsoft Azure"
	description="Начните работу с кэшем Redis для Azure с использованием Node.js и node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/31/2016"
	ms.author="sdanie"/>

# Использование кэша Redis для Azure с Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Кэш Redis для Azure дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этой статье показано, как приступить к работе с кэшем Redis для Azure, используя Node.js. Еще один пример использования кэша Redis для Azure с Node.js см. в статье [Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Предварительные требования

Установка [node\_redis](https://github.com/mranney/node_redis).

    npm install redis

В этом учебнике используется [node\_redis](https://github.com/mranney/node_redis), но можно использовать любой клиент Node.js из перечисленных на сайте [http://redis.io/clients](http://redis.io/clients).

## Создание кэша Redis в Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Получение имени узла и ключей доступа

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Включение конечной точки без SSL

Некоторые клиенты Redis не поддерживают SSL. [Все порты, кроме SSL, отключены для новых экземпляров кэша Redis для Azure](cache-configure.md#access-ports) по умолчанию. На момент написания этой статьи клиент [node\_redis](https://github.com/mranney/node_redis) не поддерживает SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Добавление данных в кэш и их извлечение

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

- [Включите диагностику кэша](cache-how-to-monitor.md#enable-cache-diagnostics), чтобы можно было [наблюдать](cache-how-to-monitor.md) за работоспособностью кэша.
- Прочитайте официальную [документацию Redis](http://redis.io/documentation).

<!---HONumber=AcomDC_0601_2016-->
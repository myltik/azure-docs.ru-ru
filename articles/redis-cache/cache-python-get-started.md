---
title: Использование кэша Redis для Azure с Python | Microsoft Docs
description: Приступая к работе с кэшем Redis для Azure с использованием Python
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: v-lincan

ms.service: cache
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/16/2016
ms.author: sdanie

---
# Использование кэша Redis для Azure с Python
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

В этой статье показано, как приступить к работе с кэшем Redis для Azure, используя Python.

## Предварительные требования
Установка [redis-py](https://github.com/andymccurdy/redis-py).

## Создание кэша Redis в Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Получение имени узла и ключей доступа
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Включение конечной точки без SSL
Некоторые клиенты Redis не поддерживают SSL. [Все порты, кроме SSL, отключены для новых экземпляров кэша Redis для Azure](cache-configure.md#access-ports) по умолчанию. На момент написания этой статьи клиент [redis-py](https://github.com/andymccurdy/redis-py) не поддерживает SSL.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## Добавление данных в кэш и их извлечение
    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Замените значение `<name>` именем своего кэша, а значение `key` — ключом доступа.

.<!--Image references-->

[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

<!---HONumber=AcomDC_0817_2016-->
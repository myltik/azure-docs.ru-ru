<properties
	pageTitle="Использование кэша Redis для Azure с Node.js | Microsoft Azure"
	description="Начните работу с кэшем Redis для Azure с использованием Node.js и node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="12/03/2015"
	ms.author="sdanie"/>

# Использование кэша Redis для Azure с Node.js

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Кэш Redis для Azure дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этой статье показано, как приступить к работе с кэшем Redis для Azure, используя Node.js. Еще один пример использования кэша Redis для Azure с Node.js см. в статье [Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure][].


## Предварительные требования

Установка [node\_redis](https://github.com/mranney/node_redis).

    npm install redis

В этом учебнике используется [node\_redis](https://github.com/mranney/node_redis), но можно использовать любой клиент Node.js из перечисленных на сайте [http://redis.io/clients](http://redis.io/clients).

## Создание кэша Redis в Azure

На [портале Azure](http://go.microsoft.com/fwlink/?LinkId=398536) последовательно щелкните **Создать** и **Данные+хранилище**, а затем выберите **Кэш Redis**.

  ![][1]

Введите имя узла DNS. Оно будет иметь форму `<name>
  .redis.cache.windows.net`. Щелкните **Создать**.

  ![][2]


  Создав кэш, [перейдите к нему](cache-configure.md#configure-redis-cache-settings) и просмотрите его параметры. Щелкните ссылку в разделе **Ключи** и скопируйте первичный ключ. Он потребуется для проверки подлинности запросов.

  ![][4]


  ## Включение конечной точки без SSL


  Щелкните ссылку в разделе **Порты** и нажмите кнопку **Нет** для параметра «Разрешить доступ только через SSL». Это включит для кэша порт без SSL. Клиент node\_redis в настоящее время не поддерживает SSL.

  ![][3]


  ## Добавление данных в кэш и их извлечение

  var redis = require("redis");

  // Добавьте имя кэша и ключ доступа. var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth\_pass: '<key>' });

	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});

	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});


Выходные данные:

	OK
	bar


## Дальнейшие действия

- [Включите диагностику кэша](cache-how-to-monitor.md#enable-cache-diagnostics), чтобы можно было [наблюдать](cache-how-to-monitor.md) за работоспособностью кэша.
- Прочитайте официальную [документацию Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=AcomDC_1210_2015-->
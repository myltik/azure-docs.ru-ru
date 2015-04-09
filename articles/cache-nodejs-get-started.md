<properties 
	pageTitle="Использование кэша Redis для Azure с Node.js" 
	description="Начните работу с кэшем Redis для Azure с использованием Node.js и node_redis." 
	services="redis-cache" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="required" 
	ms.date="02/23/2015"
	ms.author="mwasson"/>

# Использование кэша Redis для Azure с Node.js 

Кэш Redis для Azure дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этом разделе показано, как приступить к работе с кэшем Redis для Azure с использованием Node.js. Другой пример использования кэша Redis для Azure с Node.js см. в статье [Создание приложения для разговора Node.js с помощью Socket.IO на веб-сайте Azure][].


## Предварительные требования

Установите [node_redis](https://github.com/mranney/node_redis):

    npm install redis

В этом учебнике используется [node_redis](https://github.com/mranney/node_redis), но можно использовать любой клиент Node.js, указанный на сайте [http://redis.io/clients](http://redis.io/clients).

## Создание кэша Redis в Azure

На [портале управления предварительной версии Azure](http://go.microsoft.com/fwlink/?LinkId=398536) нажмите **Создать** и выберите **Кэш Redis**. 

  ![][1]

Введите имя узла DNS. Оно будет иметь форму `<name>.redis.cache.windows.net`. Щелкните **Создать**.

  ![][2]


После создания кэша щелкните его на портале для просмотра параметров кэша. Щелкните ссылку в разделе **Ключи** и скопируйте первичный ключ. Он потребуется для проверки подлинности запросов.

  ![][4]


## Включение конечной точки без SSL


Щелкните ссылку в разделе **Порты** и выберите **Нет** для параметра "Разрешить доступ только через SSL". Это включит для кэша порт без SSL. Клиент node_redis в настоящее время не поддерживает SSL.

  ![][3]


## Добавление данных в кэш и их извлечение

	var redis = require("redis");
	
    // Put in your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });
	
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

- [Включите диагностику кэша](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), чтобы [отслеживать](https://msdn.microsoft.com/library/azure/dn763945.aspx) работоспособность кэша. 
- Ознакомьтесь с официальной [документацией по Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

[Создание приложения для разговора Node.js с помощью Socket.IO на веб-сайте Azure]: web-sites-nodejs-chat-app-socketio.md


<!--HONumber=49-->
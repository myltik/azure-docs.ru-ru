<properties
	pageTitle="Использование кэша Redis для Azure с Python | Microsoft Azure"
	description="Приступая к работе с кэшем Redis для Azure с использованием Python"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="12/03/2015"
	ms.author="sdanie"/>

# Использование кэша Redis для Azure с Python

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

В этой статье показано, как приступить к работе с кэшем Redis для Azure, используя Python.


## Предварительные требования

Установка [redis-py](https://github.com/andymccurdy/redis-py).


## Создание кэша Redis в Azure

На [портале Azure](http://go.microsoft.com/fwlink/?LinkId=398536) последовательно щелкните **Создать** и **Данные+хранилище**, а затем выберите **Кэш Redis**.

  ![][1]

Введите имя узла DNS. Оно будет иметь форму `<name>
  .redis.cache.windows.net`. Щелкните **Создать**.

  ![][2]

  Создав кэш, [перейдите к нему](cache-configure.md#configure-redis-cache-settings) и просмотрите его параметры. Вам потребуются следующие значения.

  - **Имя узла.** Это имя, введенное при создании кэша.
  - **Порт.** Щелкните ссылку в разделе **Порты** для просмотра портов. Используйте порт SSL.
  - **Ключ доступа.** Щелкните ссылку в разделе **Ключи** и скопируйте первичный ключ.

  ## Добавление данных в кэш и их извлечение

  >>> import redis >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net', port=6380, db=0, password='<key>', ssl=True) >>> r.set('foo', 'bar') True >>> r.get('foo') b'bar'

Замените *&lt;name&gt;* именем своего кэша, а *&lt;key&gt;* — ключом доступа.


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=AcomDC_1210_2015-->
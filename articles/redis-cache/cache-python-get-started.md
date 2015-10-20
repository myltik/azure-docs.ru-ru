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
	ms.date="10/05/2015"
	ms.author="sdanie"/>

# Использование кэша Redis для Azure с Python

В этой статье показано, как приступить к работе с кэшем Redis для Azure, используя Python.


## Предварительные требования

Установка [redis-py](https://github.com/andymccurdy/redis-py).


## Создание кэша Redis в Azure

На [портале предварительной версии Azure](http://go.microsoft.com/fwlink/?LinkId=398536) щелкните **Создать**, **Данные + хранилище** и выберите **Кэш Redis**.

  ![][1]

Введите имя узла DNS. Оно будет иметь форму `<name>.redis.cache.windows.net`. Щелкните **Создать**.

  ![][2]

Создав кэш, щелкните его на портале предварительной версии, чтобы просмотреть параметры кэша. Вам потребуются следующие значения.

- **Имя узла.** Это имя, введенное при создании кэша.
- **Порт.** Щелкните ссылку в разделе **Порты** для просмотра портов. Используйте порт SSL.
- **Ключ доступа.** Щелкните ссылку в разделе **Ключи** и скопируйте первичный ключ.

## Добавление данных в кэш и их извлечение

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

Замените *&lt;name&gt;* именем своего кэша, а *&lt;key&gt;* — ключом доступа.


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=Oct15_HO3-->
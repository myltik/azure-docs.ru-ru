<properties 
	pageTitle="Пакет SDK для Node.js DocumentDB | Microsoft Azure" 
	description="Сведения о пакете SDK для Node.js, включая даты выхода, даты выбытия и изменения, внесенные в каждой версии пакета SDK для Node.js DocumentDB." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# Пакет SDK для DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##Пакет SDK для Node.js DocumentDB

<table> <tr><td>**Загрузка**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr> <tr><td>**Участие**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr> <tr><td>**Документация**</td><td>[Справочная документация по пакету SDK для Node.js](http://azure.github.io/azure-documentdb-node/)</td></tr> <tr><td>**Примеры**</td><td>[Примеры кода Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr> <tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для Node.js](documentdb-nodejs-get-started.md)</td></tr> <tr><td>**Поддерживаемая платформа**</td><td>[Node.js версии 0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js версии 0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js версии 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr> </table></br>

## Заметки о выпуске

### <a name="1.4.0"/>1.4.0</a>

- Реализована операция Upsert. Новые методы upsertXXX в documentClient. 

### <a name="1.3.0"/>1.3.0</a>

- Номер версии пропущен для согласованности номера версии с другими пакетами SDK.

### <a name="1.2.2"/>1.2.2</a>

- В новый репозиторий добавляется оболочка для обещаний Split Q.
- Обновлен файл пакетов для реестра npm.

### <a name="1.2.1"/>1.2.1</a>

- Реализована маршрутизация на основе идентификатора.
- Устранена проблема [№ 49](https://github.com/Azure/azure-documentdb-node/issues/49) — текущее свойство конфликтует с методом current().

### <a name="1.2.0"/>1.2.0</a>

- Добавлена поддержка геопространственного индекса.
- Проверка свойств идентификатора для всех ресурсов. Идентификаторы ресурсов не могут содержать символы ?, /, #, &#47;&#47; или заканчиваться пробелом. 
- Добавлен новый заголовок "ход выполнения преобразования индекса" в ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Реализована политика индексации версии 2.

### <a name="1.0.3"/>1.0.3</a>

- Устранена проблема [№ 40](https://github.com/Azure/azure-documentdb-node/issues/40) — реализованы конфигурации eslint и grunt в ядре и пакет SDK для обещаний.

### <a name="1.0.2"/>1.0.2</a>

- Устранена проблема [№ 45](https://github.com/Azure/azure-documentdb-node/issues/45) — оболочка обещаний больше не включает заголовок с ошибкой.

### <a name="1.0.1"/>1.0.1</a>

- Реализована возможность запрашивать конфликты, добавляя методы readConflicts, readConflictAsync и queryConflicts.
- Обновленная документация по API
- Проблема [#41](https://github.com/Azure/azure-documentdb-node/issues/41): ошибка client.createDocumentAsync  

### <a name="1.0.0"/>1.0.0</a>

- Пакет SDK общей доступности

## Даты выпуска и вывода
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии.

Любые запросы в DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [AZURE.WARNING]Все версии пакета SDK для Azure DocumentDB для Node.js версии ниже **1.0.0** будут удалены **29 февраля 2016 года**.

<br/>

| Version (версия) | Дата выпуска | Дата вывода 
| ---	  | ---	         | ---
| [1\.4.0](#1.4.0) | 06 октября 2015 г. |---| [1\.3.0](#1.3.0) | 06 октября 2015 г. |---| [1\.2.2](#1.2.2) | 10 сентября 2015 г. |---| [1\.2.1](#1.2.1) | 15 августа 2015 г. |---| [1\.2.0](#1.2.0) | 05 августа 2015 г. |---| [1\.1.0](#1.1.0) | 09 июля 2015 г. |---| [1\.0.3](#1.0.3) | 04 июня 2015 г. |---| [1\.0.2](#1.0.2) | 23 мая 2015 г. |---| [1\.0.1](#1.0.1) | 15 мая 2015 г. |---| [1\.0.0](#1.0.0) | 08 апреля 2015 г. |---| предв. версия 0.9.4 | 06 апреля 2015 г. | 29 февраля 2016 г. | предв. версия 0.9.3 | 14 января 2015 г. | 29 февраля 2016 г. | предв. версия 0.9.2 | 18 декабря 2014 г. | 29 февраля 2016 г. | предв. версия 0.9.1 | 22 августа 2014 г. | 29 февраля 2016 г. | предв. версия 0.9.0 | 21 августа 2014 г. | 29 февраля 2016 г.


## Часто задаваемые вопросы
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## См. также

Дополнительные сведения о DocumentDB см. на странице документации по [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_1125_2015-->
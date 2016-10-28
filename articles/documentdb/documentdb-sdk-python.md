<properties 
	pageTitle="Пакет SDK и интерфейсы API для DocumentDB на Python | Microsoft Azure" 
	description="Сведения о пакете SDK и интерфейсах API для Python, включая даты выхода и снятия с учета, изменения, внесенные в каждую версию пакета SDK для DocumentDB на Python." 
	services="documentdb" 
	documentationCenter="python" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# Интерфейсы API и пакеты SDK для DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## Пакет SDK и API для DocumentDB на Python

.<table>
<tr><td>**Скачать пакет SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Документация по интерфейсам API**</td><td>[Справка по интерфейсам API для Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Инструкции по установке пакета SDK**</td><td>[Инструкции по установке пакета SDK для Python](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Участие в разработке пакета SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для Python](documentdb-python-application.md)</td></tr>
<tr><td>**Текущая поддерживаемая платформа**</td><td>[Python&#160;2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Заметки о выпуске

### <a name="1.9.0"/>[1\.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Добавлена поддержка политики повтора для отрегулированных запросов. (Отрегулированные запросы порождают исключение слишком высокой частоты запросов с кодом ошибки 429.) По умолчанию при обнаружении кода ошибки 429 DocumentDB повторяет запрос девять раз, используя интервал retryAfter в заголовке ответа. Фиксированный интервал повтора теперь можно задать как часть свойства RetryOptions объекта ConnectionPolicy, если требуется игнорировать интервал retryAfter, возвращаемый сервером между повторными попытками. Теперь DocumentDB ожидает до 30 секунд, пока запрос регулируется (независимо от количества повторных попыток), и возвращает ответ с кодом ошибки 429. Этот интервал также можно переопределить в свойстве RetryOptions объекта ConnectionPolicy.

- Теперь DocumentDB возвращает x-ms-throttle-retry-count и x-ms-throttle-retry-wait-time-ms в заголовке ответа на каждый запрос, чтобы обозначить количество повторных попыток при регулировании и совокупное время ожидания запроса между повторами.

- Удален класс RetryPolicy и соответствующее свойство (retry\_policy), предоставляемое в классе document\_client. Вместо них добавлен класс RetryOptions, предоставляющий свойство RetryOptions класса ConnectionPolicy, с помощью которого можно переопределить некоторые параметры повторных попыток по умолчанию.

### <a name="1.8.0"/>[1\.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Добавлена поддержка учетных записей базы данных в нескольких регионах.

### <a name="1.7.0"/>[1\.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Добавлена поддержка функции срока жизни для документов.

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Исправления ошибок, связанных с секционированием на стороне сервера, которые позволяют использовать специальные знаки в пути partitionkey.

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Реализованы [секционированные коллекции](documentdb-partition-data.md) и [пользовательские уровни производительности](documentdb-performance-levels.md).

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Добавьте сопоставители разделов "Хэш" и "Диапазон" для сегментирования приложений на разделы.

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Реализована операция Upsert. Для поддержки функции Upsert добавлены новые методы UpsertXXX.
- Реализована маршрутизация на основе идентификатора. Отсутствуют изменения общего API-интерфейса. Все изменения касаются внутреннего интерфейса.

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Поддержка геопространственного индекса.
- Проверка свойств идентификатора для всех ресурсов. Идентификаторы ресурсов не могут содержать символы ?, /, #, \\ или заканчиваться пробелом.
- Добавлен новый заголовок "ход выполнения преобразования индекса" в ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Реализована политика индексации версии 2.

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Добавлена поддержка прокси-подключения.

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- Пакет SDK общей доступности.

## Даты выпуска и вывода из эксплуатации
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии.

Любые запросы к DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [AZURE.WARNING]
Поддержка всех версий пакета SDK для Azure DocumentDB для Java версии раньше **1.0.0** будет отменена **29 февраля 2016 года**.

.<br/>

| Version (версия) | Дата выпуска | Дата вывода 
| ---	  | ---	         | ---
| [1\.9.0](#1.9.0) | 7 июля 2016 г. |---
| [1\.8.0](#1.8.0) | 14 июня 2016 г. |---
| [1\.7.0](#1.7.0) | 26 апреля 2016 г. |---
| [1\.6.1](#1.6.1) | 8 апреля 2016 г. |---
| [1\.6.0](#1.6.0) | 29 марта 2016 г. |---
| [1\.5.0](#1.5.0) | 3 января 2016 г. |---
| [1\.4.2](#1.4.2) | 6 октября 2015 г. |---
| [1\.4.1](#1.4.1) | 6 октября 2015 г. |---
| [1\.2.0](#1.2.0) | 6 августа 2015 г. |---
| [1\.1.0](#1.1.0) | 9 июля 2015 г. |---
| [1\.0.1](#1.0.1) | 25 мая 2015 г. |---
| [1\.0.0](#1.0.0) | 7 апреля 2015 г. |---
| 0.9.4-prelease | 14 января 2015 г. | 29 февраля 2016 г. 
| 0.9.3-prelease | 9 декабря 2014 г. | 29 февраля 2016 г. 
| 0.9.2-prelease | 25 ноября 2014 г. | 29 февраля 2016 г. 
| 0.9.1-prelease | 23 сентября 2014 г. | 29 февраля 2016 г. 
| 0.9.0-prelease | 21 августа 2014 г. | 29 февраля 2016 г.

## Часто задаваемые вопросы
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Дополнительные материалы

Дополнительные сведения о DocumentDB см. на странице документации по [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->
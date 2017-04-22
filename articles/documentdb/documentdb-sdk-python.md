---
title: "Пакет SDK и интерфейсы API для Python (Azure DocumentDB) | Документы Майкрософт"
description: "Сведения о пакете SDK и интерфейсах API для Python, включая даты выхода и снятия с учета, изменения, внесенные в каждую версию пакета SDK для DocumentDB на Python."
services: documentdb
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/30/2016
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 4c83bcbf3e5849afd15060947ef5f0faab17ce19
ms.lasthandoff: 03/07/2017


---
# <a name="documentdb-python-sdk-release-notes-and-resources"></a>Пакет SDK для DocumentDB Python: заметки о выпуске и ресурсы
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Скачать пакет SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Инструкции по установке пакета SDK**</td><td>[Инструкции по установке пакета SDK для Python](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Участие в разработке пакета SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Приступая к работе**</td><td>[Приступая к работе с пакетом SDK для Python](documentdb-python-application.md)</td></tr>

<tr><td>**Текущая поддерживаемая платформа**</td><td>[Python 2.7](https://www.python.org/downloads/) и [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске
### <a name="a-name201201httpspypipythonorgpypipydocumentdb201"></a><a name="2.0.1"/>[2.0.1](https://pypi.python.org/pypi/pydocumentdb/2.0.1)
* Внесены редакторские правки в комментарии к документации.

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
* Добавлена поддержка Python 3.5.
* Добавлена поддержка пулов подключений с использованием модуля запросов.
* Добавлена поддержка согласованности сеанса.
* Добавлена поддержка запросов TOP и ORDERBY для секционированных коллекций.

### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
* Добавлена поддержка политики повтора для отрегулированных запросов. (Отрегулированные запросы порождают исключение слишком высокой частоты запросов с кодом ошибки 429.) По умолчанию при обнаружении кода ошибки 429 DocumentDB повторяет запрос девять раз, используя интервал retryAfter в заголовке ответа. Фиксированный интервал повтора теперь можно задать как часть свойства RetryOptions объекта ConnectionPolicy, если требуется игнорировать интервал retryAfter, возвращаемый сервером между повторными попытками. Теперь DocumentDB ожидает до 30 секунд, пока запрос регулируется (независимо от количества повторных попыток), и возвращает ответ с кодом ошибки 429. Этот интервал также можно переопределить в свойстве RetryOptions объекта ConnectionPolicy.
* Теперь DocumentDB возвращает x-ms-throttle-retry-count и x-ms-throttle-retry-wait-time-ms в заголовке ответа на каждый запрос, чтобы обозначить количество повторных попыток при регулировании и совокупное время ожидания запроса между повторами.
* Удален класс RetryPolicy и соответствующее свойство (retry_policy), предоставляемое в классе document_client. Вместо них добавлен класс RetryOptions, предоставляющий свойство RetryOptions класса ConnectionPolicy, с помощью которого можно переопределить некоторые параметры повторных попыток по умолчанию.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
* Добавлена поддержка учетных записей базы данных в нескольких регионах.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
* Добавлена поддержка функции срока жизни для документов.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
* Исправления ошибок, связанных с секционированием на стороне сервера, которые позволяют использовать специальные знаки в пути partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
* Реализованы [секционированные коллекции](documentdb-partition-data.md) и [определяемые пользователем уровни производительности](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
* Добавьте сопоставители разделов "Хэш" и "Диапазон" для сегментирования приложений на разделы.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
* Реализована операция Upsert. Для поддержки функции Upsert добавлены новые методы UpsertXXX.
* Реализована маршрутизация на основе идентификатора. Отсутствуют изменения общего API-интерфейса. Все изменения касаются внутреннего интерфейса.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
* Поддержка геопространственного индекса.
* Проверка свойств идентификатора для всех ресурсов. Идентификаторы ресурсов не могут содержать знаки ?, /, #, \, или заканчиваться пробелом.
* Добавлен новый заголовок "ход выполнения преобразования индекса" в ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
* Реализована политика индексации версии 2.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
* Добавлена поддержка прокси-подключения.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
* Пакет SDK общей доступности.

## <a name="release--retirement-dates"></a>Даты выпуска и вывода из эксплуатации
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии. 

Любые запросы к DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [!WARNING]
> Поддержка всех версий пакета SDK Azure DocumentDB для Python версии ниже **1.0.0** будет прекращена **29 февраля 2016 года**. 
> 
> 

<br/>

| Версия | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [2.0.1](#2.0.1) |30 октября 2016 г. |--- |
| [2.0.0](#2.0.0) |29 сентября 2016 г. |--- |
| [1.9.0](#1.9.0) |7 июля 2016 г. |--- |
| [1.8.0](#1.8.0) |14 июня 2016 г. |--- |
| [1.7.0](#1.7.0) |26 апреля 2016 г. |--- |
| [1.6.1](#1.6.1) |8 апреля 2016 г. |--- |
| [1.6.0](#1.6.0) |29 марта 2016 г. |--- |
| [1.5.0](#1.5.0) |3 января 2016 г. |--- |
| [1.4.2](#1.4.2) |6 октября 2015 г. |--- |
| [1.4.1](#1.4.1) |6 октября 2015 г. |--- |
| [1.2.0](#1.2.0) |6 августа 2015 г. |--- |
| [1.1.0](#1.1.0) |9 июля 2015 г. |--- |
| [1.0.1](#1.0.1) |25 мая 2015 г. |--- |
| [1.0.0](#1.0.0) |7 апреля 2015 г. |--- |
| 0.9.4-prelease |14 января 2015 г. |29 февраля 2016 г. |
| 0.9.3-prelease |9 декабря 2014 г. |29 февраля 2016 г. |
| 0.9.2-prelease |25 ноября 2014 г. |29 февраля 2016 г. |
| 0.9.1-prelease |23 сентября 2014 г. |29 февраля 2016 г. |
| 0.9.0-prelease |21 августа 2014 г. |29 февраля 2016 г. |

## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Дополнительные материалы
Дополнительные сведения о DocumentDB см. на странице документации по службе [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 



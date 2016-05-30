<properties 
	pageTitle="Пакет SDK для DocumentDB Python | Microsoft Azure" 
	description="Сведения о пакете SDK для Python, включая даты выхода, даты вывода из эксплуатации и изменения, внесенные в каждую версию пакета SDK для DocumentDB Python." 
	services="documentdb" 
	documentationCenter="python" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/26/2016" 
	ms.author="rnagpal"/>

# Пакет SDK для DocumentDB

> [AZURE.SELECTOR]
- [Пакет SDK для .NET](documentdb-sdk-dotnet.md)
- [Пакет SDK для Node.js](documentdb-sdk-node.md)
- [Пакет SDK для Java](documentdb-sdk-java.md)
- [Пакет SDK для Python](documentdb-sdk-python.md)

##Пакет SDK для DocumentDB Python

<table>
<tr><td>**Скачивание**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Участие**</td><td>[GitHub] (https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Документация**</td><td>[Справочная документация по пакету SDK для Python] (http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Приступая к работе**</td><td>[Начало работы с пакетом SDK для Python](documentdb-python-application.md)</td></tr>
<tr><td>**Текущая поддерживаемая платформа**</td><td>[Python&#160;2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Заметки о выпуске

### <a name="1.7.0"/>[1\.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Добавлена поддержка функции срока жизни для документов.

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Исправления ошибок, связанных с секционированием на стороне сервера, которые позволяют использовать специальные знаки в пути partitionkey.

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Реализованы [секционированные коллекции](documentdb-partition-data.md) и [определяемые пользователем уровни производительности](documentdb-performance-levels.md). 

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

Любые запросы в DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [AZURE.WARNING]
Поддержка всех версий пакета SDK для Azure DocumentDB для Java версии раньше **1.0.0** будет отменена **29 февраля 2016 года**.

<br/>

| Version (версия) | Дата выпуска | Дата вывода 
| ---	  | ---	         | ---
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

## См. также

Дополнительные сведения о DocumentDB см. на странице документации по [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0518_2016-->
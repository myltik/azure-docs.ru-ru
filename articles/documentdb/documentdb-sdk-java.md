---
title: "Пакет SDK и интерфейсы API Java для DocumentDB | Документация Майкрософт"
description: "Сведения о пакете SDK и интерфейсах API для Java, включая даты выхода и снятия с учета, изменения, внесенные в каждую версию пакета SDK для DocumentDB на Java."
services: documentdb
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/28/2016
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 35a773e5f91490c3d4eb053d71ce1d189ba96872


---
# <a name="documentdb-apis-and-sdks"></a>Интерфейсы API и пакеты SDK для DocumentDB
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

## <a name="documentdb-java-api-and-sdk"></a>Интерфейсы API и пакеты SDK для DocumentDB на Java
<table>

<tr><td>**Скачивание пакета SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API Java](http://azure.github.io/azure-documentdb-java/)</td></tr>

<tr><td>**Участие в разработке пакета SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Приступая к работе**</td><td>[Приступая к работе с пакетом SDK для Java](documentdb-java-application.md)</td></tr>

<tr><td>**Текущая поддерживаемая среда выполнения**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске
### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)
* Добавлена поддержка уровня согласованности BoundedStaleness.
* Добавлена поддержка прямого подключения для операций CRUD с секционированными коллекциями.
* Исправлена ошибка, возникающая при выполнении запросов к базе данных с помощью SQL.
* Исправлена ошибка в кэше сеанса, приводившая к неправильному заданию маркера сеанса.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)
* Добавлена поддержка параллельных запросов между секциями.
* Добавлена поддержка запросов TOP и ORDER BY для секционированных коллекций.
* Добавлена поддержка строгой согласованности.
* Добавлена поддержка запросов по имени при использовании прямого подключения.
* Внесено исправление, обеспечивающее согласованность ActivityId для всех попыток выполнения запроса.
* Исправлена ошибка, связанная с кэшем сеанса и воссозданием коллекции с тем же именем.
* Добавлены типы данных Polygon и LineString и задана политика индексирования коллекций для запросов пространственных геозон.
* Устранены проблемы с JavaDoc для Java 1.8.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
* Исправлена ошибка в PartitionKeyDefinitionMap, из-за которой осуществлялось кэширование коллекций одной секции и не выполнялись дополнительные запросы на получение ключа секции.
* Исправлена ошибка, из-за которой при предоставлении неправильного ключа секции не предпринималась повторная попытка.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
* Добавлена поддержка учетных записей базы данных в нескольких регионах.
* Добавлена поддержка автоматического повтора на отрегулированных запросов с параметрами для настройки максимального количества повторов и максимального время между повторами.  Ознакомьтесь с RetryOptions и ConnectionPolicy.getRetryOptions().
* Не рекомендуется использовать IPartitionResolver на основе пользовательского кода секционирования. Используйте секционированные коллекции, чтобы увеличить возможности хранилища и пропускную способность.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
* Добавлена поддержка политики повтора для регулирования.  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
* Добавлена поддержка срока жизни для документов.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
* Реализованы [секционированные коллекции](documentdb-partition-data.md) и [определяемые пользователем уровни производительности](documentdb-performance-levels.md).

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
* Исправлена ошибка в HashPartitionResolver, чтобы значения создавались с прямым порядком байтов для согласования с другими пакетами SDK.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
* Добавьте сопоставители разделов "Хэш" и "Диапазон" для сегментирования приложений на разделы.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
* Реализована операция Upsert. Для поддержки функции Upsert добавлены новые методы upsertXXX.
* Реализована маршрутизация на основе идентификатора. Отсутствуют изменения общего API-интерфейса. Все изменения касаются внутреннего интерфейса.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Пропущен номер выпуска для согласованности номера версии с другими пакетами SDK.

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
* Поддержка геопространственного индекса
* Проверка свойств идентификатора для всех ресурсов. Идентификаторы ресурсов не могут содержать знаки ?, /, #, \, или заканчиваться пробелом.
* Добавлен новый заголовок "ход выполнения преобразования индекса" в ResourceResponse.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
* Реализована политика индексации версии&2;.

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
* Пакет SDK общей доступности.

## <a name="release--retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии.

Любые запросы к DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [!WARNING]
> Поддержка всех версий пакета SDK Azure DocumentDB для Java версии ниже **1.0.0** будет прекращена **29 февраля 2016 года**.
> 
> 

<br/>

| Версия | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.9.1](#1.9.1) |28 октября 2016 г. |--- |
| [1.9.0](#1.9.0) |3 октября 2016 г. |--- |
| [1.8.1](#1.8.1) |30 июня 2016 г. |--- |
| [1.8.0](#1.8.0) |14 июня 2016 г. |--- |
| [1.7.1](#1.7.1) |30 апреля 2016 г. |--- |
| [1.7.0](#1.7.0) |27 апреля 2016 г. |--- |
| [1.6.0](#1.6.0) |29 марта 2016 г. |--- |
| [1.5.1](#1.5.1) |31 декабря 2015 г. |--- |
| [1.5.0](#1.5.0) |4 декабря 2015 г. |--- |
| [1.4.0](#1.4.0) |5 октября 2015 г. |--- |
| [1.3.0](#1.3.0) |5 октября 2015 г. |--- |
| [1.2.0](#1.2.0) |5 августа 2015 г. |--- |
| [1.1.0](#1.1.0) |9 июля 2015 г. |--- |
| [1.0.1](#1.0.1) |12 мая 2015 г. |--- |
| [1.0.0](#1.0.0) |7 апреля 2015 г. |--- |
| 0.9.5-prelease |9 марта 2015 г. |29 февраля 2016 г. |
| 0.9.4-prelease |17 февраля 2015 г. |29 февраля 2016 г. |
| 0.9.3-prelease |13 января 2015 г. |29 февраля 2016 г. |
| 0.9.2-prelease |19 декабря 2014 г. |29 февраля 2016 г. |
| 0.9.1-prelease |19 декабря 2014 г. |29 февраля 2016 г. |
| 0.9.0-prelease |10 декабря 2014 г. |29 февраля 2016 г. |

## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о DocumentDB см. на странице документации по службе [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).




<!--HONumber=Dec16_HO2-->



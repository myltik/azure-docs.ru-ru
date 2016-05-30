<properties 
	pageTitle="Пакет SDK для DocumentDB для Java | Microsoft Azure" 
	description="Сведения о пакете SDK для Java, включая даты выхода, даты вывода из эксплуатации и изменения, внесенные в каждую версию пакета SDK для DocumentDB Java." 
	services="documentdb" 
	documentationCenter="java" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/02/2016" 
	ms.author="andrl"/>

# Пакет SDK для DocumentDB

> [AZURE.SELECTOR]
- [Пакет SDK для .NET](documentdb-sdk-dotnet.md)
- [Пакет SDK для Node.js](documentdb-sdk-node.md)
- [Пакет SDK для Java](documentdb-sdk-java.md)
- [Пакет SDK для Python](documentdb-sdk-python.md)

##Пакет SDK Java для DocumentDB

<table>
<tr><td>**Скачивание**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**Участие**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Документация**</td><td>[Справочная документация по пакету SDK для Java](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Приступая к работе**</td><td>[Начало работы с пакетом SDK для Java](documentdb-java-application.md)</td></tr>
<tr><td>**Текущая поддерживаемая среда выполнения**</td><td>[JDK&#160;7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## Заметки о выпуске

### <a name="1.7.1"/>[1\.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Добавлена поддержка политики повтора для регулирования.  

### <a name="1.7.0"/>[1\.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Добавлена поддержка срока жизни для документов. 

### <a name="1.6.0"/>[1\.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Реализованы [секционированные коллекции](documentdb-partition-data.md) и [определяемые пользователем уровни производительности](documentdb-performance-levels.md). 

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Исправлена ошибка в HashPartitionResolver, чтобы значения создавались с прямым порядком байтов для согласования с другими пакетами SDK.

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Добавьте сопоставители разделов "Хэш" и "Диапазон" для сегментирования приложений на разделы.

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Реализована операция Upsert. Для поддержки функции Upsert добавлены новые методы upsertXXX.
- Реализована маршрутизация на основе идентификатора. Отсутствуют изменения общего API-интерфейса. Все изменения касаются внутреннего интерфейса.

### <a name="1.3.0"/>1.3.0
- Пропущен номер выпуска для согласованности номера версии с другими пакетами SDK.

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Поддержка геопространственного индекса
- Проверка свойств идентификатора для всех ресурсов. Идентификаторы ресурсов не могут содержать символы ?, /, #, \\ или заканчиваться пробелом.
- Добавлен новый заголовок "ход выполнения преобразования индекса" в ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Реализована политика индексации версии 2.

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- Пакет SDK общей доступности

## Даты выпуска и вывода
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии.

Любые запросы в DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [AZURE.WARNING]
Поддержка всех версий пакета SDK для Azure DocumentDB для Java версии ниже **1.0.0** будет отменена **29 февраля 2016 года**.

<br/>

| Version (версия) | Дата выпуска | Дата вывода 
| ---	  | ---	         | ---
| [1\.7.1](#1.7.1) | 30 апреля 2016 г. |---
| [1\.7.0](#1.7.0) | 27 апреля 2016 г. |---
| [1\.6.0](#1.6.0) | 29 марта 2016 г. |---
| [1\.5.1](#1.5.1) | 31 декабря 2015 г. |---
| [1\.5.0](#1.5.0) | 4 декабря 2015 г. |---
| [1\.4.0](#1.4.0) | 5 октября 2015 г. |---
| [1\.3.0](#1.3.0) | 5 октября 2015 г. |---
| [1\.2.0](#1.2.0) | 5 августа 2015 г. |---
| [1\.1.0](#1.1.0) | 9 июля 2015 г. |---
| [1\.0.1](#1.0.1) | 12 мая 2015 г. |---
| [1\.0.0](#1.0.0) | 7 апреля 2015 г. |---
| 0.9.5-prelease | 9 марта 2015 г. | 29 февраля 2016 г.
| 0.9.4-prelease | 17 февраля 2015 г. | 29 февраля 2016 г.
| 0.9.3-prelease | 13 января 2015 г. | 29 февраля 2016 г.
| 0.9.2-prelease | 19 декабря 2014 г. | 29 февраля 2016 г.
| 0.9.1-prelease | 19 декабря 2014 г. | 29 февраля 2016 г.
| 0.9.0-prelease | 10 декабря 2014 г. | 29 февраля 2016 г.

## Часто задаваемые вопросы
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## См. также

Дополнительные сведения о DocumentDB см. на странице документации по [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0518_2016-->
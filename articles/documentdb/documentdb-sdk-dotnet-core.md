---
title: "Интерфейс API, пакет SDK и ресурсы для .NET Core (Azure DocumentDB) | Документация Майкрософт"
description: "Сведения об API и пакетах SDK для .NET Core, в том числе даты выхода, даты выбытия и изменения, внесенные в каждую версию пакета SDK для DocumentDB .NET Core."
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 492444304c42a270e638050506e04297c8b17e51
ms.lasthandoff: 03/15/2017


---
# <a name="documentdb-net-core-sdk-release-notes-and-resources"></a>Пакет SDK .NET Core для DocumentDB: заметки о выпуске и материалы
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

<tr><td>**Скачивание пакета SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Примеры**</td><td>[Примеры кода для .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для DocumentDB .NET Core](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Учебник по веб-приложениям**</td><td>[Разработка веб-приложений с использованием DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Текущая поддерживаемая платформа**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске

Пакет SDK DocumentDB для .NET Core имеет те же функции, что и последняя версия [пакета SDK для DocumentsDB .NET](documentdb-sdk-dotnet.md).

> [!NOTE] 
> Пакет SDK для DocumentDB .NET Core несовместим с приложениями универсальной платформы Windows (UWP). Чтобы получить пакет SDK для .NET Core, который поддерживает приложения UWP, отправьте сообщение по адресу [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

### <a name="a-name111111httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore111"></a><a name="1.1.1"/>[1.1.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.1)

* Добавлена поддержка LINQ для статистических запросов (COUNT, MIN, MAX, SUM и AVG).
* Исправление ошибки утечки памяти для объекта ConnectionPolicy, вызываемой использованием обработчика событий.
* Исправление ошибки, из-за которой метод UpsertAttachmentAsync не работал, когда использовался тег ETag.
* Исправление ошибки, из-за которой продолжение запросов orderBy между секциями не работало при сортировке по полю строки.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.0)

* Добавлена поддержка статистических запросов (COUNT, MIN, MAX, SUM и AVG). Дополнительные сведения см. в разделе [Статистические функции](documentdb-sql-query.md#Aggregates).
* Минимальная пропускная способность секционированных коллекций снижена с 10 100 ЕЗ/с до 2500 ЕЗ/с.

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.0.0)

Пакет SDK для DocumentDB .NET Core позволяет создавать быстрые кроссплатформенные приложения [ASP.NET Core](https://www.asp.net/core) и [ .NET Core](https://www.microsoft.com/net/core#windows) для Windows, Mac и Linux. Последний выпуск пакета SDK для DocumentDB .NET Core полностью совместим с [Хamarin](https://www.xamarin.com) и используется для создания приложений для iOS, Android и Mono (Linux).  

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

Пакет SDK для предварительной версии DocumentDB .NET Core позволяет создавать быстрые кроссплатформенные приложения [ASP.NET Core](https://www.asp.net/core) и [.NET Core](https://www.microsoft.com/net/core#windows) для среды Windows, Maс и Linux.

Пакет SDK для предварительной версии DocumentDB .NET Core имеет те же функции, что и последняя версия [пакета SDK для DocumentDB .NET](documentdb-sdk-dotnet.md), и поддерживает следующее:
* все [режимы подключения](documentdb-performance-tips.md#networking): режим шлюза, прямые TCP- и HTTP-подключения; 
* все [уровни согласованности](documentdb-consistency-levels.md): строгая, ограниченное устаревание, согласованность сеанса, окончательная;
* [секционированные коллекции](documentdb-partition-data.md); 
* [георепликация данных и межрегиональные учетные записи баз данных](documentdb-distribute-data-globally.md).

Если у вас возникли вопросы об этом пакете SDK, опубликуйте их на форуме сайта [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) или сообщите о проблеме в [репозитории GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Даты выпуска и выбытия

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.1.1](#1.1.1) |14 марта 2017 г. |--- |
| [1.1.0](#1.1.0) |16 февраля 2017 г. |--- |
| [1.0.0](#1.0.0) |21 декабря 2016 г. |--- |
| [0.1.0-preview](#0.1.0-preview) |15 ноября 2016 г. |31 декабря 2016 г. |

## <a name="see-also"></a>См. также
Дополнительные сведения о DocumentDB см. на странице документации по службе [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 



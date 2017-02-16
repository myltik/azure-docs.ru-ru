---
title: "Интерфейсы API и пакеты SDK для DocumentDB .NET Core | Документация Майкрософт"
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
ms.date: 01/12/2017
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: 855fd05d9addc2e57568067e4d434836ceeed570
ms.openlocfilehash: 21a5f321bd1a3b45cd87c2c9274139c47562cee7


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

## <a name="documentdb-net-core-api-and-sdk"></a>API и пакеты SDK для DocumentDB .NET Core
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
> Пакет SDK для DocumentDB .NET Core несовместим с приложениями универсальной платформы Windows (UWP). Чтобы получить предварительную версию пакета SDK для .NET Сore, которая поддерживает приложения UWP, отправьте сообщение по адресу [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

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
| [1.0.0](#1.0.0) |21 декабря 2016 г. |--- |
| [0.1.0-preview](#0.1.0-preview) |15 ноября 2016 г. |31 декабря 2016 г. |

## <a name="see-also"></a>См. также
Дополнительные сведения о DocumentDB см. на странице документации по службе [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 




<!--HONumber=Jan17_HO2-->



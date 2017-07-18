---
title: "Отправка запросов в индекс службы поиска Azure с помощью портала | Документация Майкрософт"
description: "Создайте поисковый запрос в проводнике поиска на портале Azure."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 07/10/2017
ms.author: ashmaka
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: dd68d8ed073bf7b8666ddef35a2f1f84df690b4b
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Отправка запросов в индекс службы поиска Azure с использованием обозревателя поиска на портале Azure
> [!div class="op_single_selector"]
> * [Обзор](search-query-overview.md)
> * [Портал](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

В этой статье показано, как отправлять запросы в индекс службы поиска Azure с помощью **обозревателя поиска** на портале Azure. Используйте обозреватель поиска, чтобы отправить простые строки запросов или расширенные строки запросов Lucene в любой имеющийся индекс в службе.

## <a name="open-the-service-dashboard"></a>Открытие панели мониторинга службы
1. Щелкните **Все ресурсы** на панели переходов слева на [портале Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Выберите службу поиска Azure.

## <a name="select-an-index"></a>Выбор индекса

Выберите индекс, в котором нужно выполнить поиск, на плитке **Индексы**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Открытие обозревателя поиска

Щелкните плитку обозревателя поиска, чтобы открыть панель поиска и область результатов.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Начните поиск.

При использовании обозревателя поиска можно указать [параметры запроса](https://docs.microsoft.com/rest/api/searchservice/Search-Documents), чтобы сформировать запрос.

1. В **строке запроса** введите запрос и нажмите кнопку **Поиск**. 

   Строка запроса автоматически преобразуется в правильный URL-адрес запроса для отправки HTTP-запроса в REST API службы поиска Azure.   
   
   Используйте любой допустимый простой синтаксис запросов или синтаксис расширенных запросов Lucene, чтобы создать запрос. Знак `*` эквивалентен пустому полю поиска или неуказанным условиям поиска, который возвращает все документы в произвольном порядке.

2. В области **результатов** результаты запроса будут представлены в необработанном формате JSON аналогично полезным данным, возвращенным в тексте HTTP-ответа при программной отправке запросов.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о синтаксисе запросов и примеры см. в следующих ресурсах.

 + [Простой синтаксис запросов](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Синтаксис запросов Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Примеры синтаксиса запросов Lucene для создания запросов в службе поиска Azure](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData Expression Syntax for Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Синтаксис выражений OData для службы поиска Azure) 

---
title: "Отправка запросов в индекс службы поиска Azure с помощью портала Azure | Документация Майкрософт"
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
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Отправка запросов в индекс службы поиска Azure с помощью портала Azure
> [!div class="op_single_selector"]
> * [Обзор](search-query-overview.md)
> * [Портал](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

В этом руководстве показано, как отправлять запросы в индекс службы поиска Azure с портала Azure.

Прежде чем приступать к выполнению инструкций из этого руководства, необходимо [создать индекс службы поиска Azure](search-what-is-an-index.md) и [заполнить его данными](search-what-is-data-import.md).

## <a name="i-go-to-your-azure-search-blade"></a>1. Переход к колонке службы поиска Azure
1. Щелкните "Все ресурсы" в меню слева на [портале Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Выберите службу поиска Azure.

## <a name="ii-select-the-index-you-would-like-to-search"></a>2. Выберите индекс, в котором нужно выполнить поиск.
1. Выберите индекс, в котором нужно выполнить поиск, на плитке "Индексы".

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>3. Щелкните плитку "Проводник поиска".
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>3. Начните поиск.
1. Для поиска в индексе службы поиска Azure начните вводить запрос в поле*Строка запроса*, а затем щелкните**Поиск**.
   
   * При использовании проводника поиска можно указать любые [параметры запроса](https://msdn.microsoft.com/library/dn798927.aspx)
2. В разделе*Результаты*результаты запроса будут показаны в необработанном файле JSON, который вы должны получить в тексте HTTP-ответа при отправке поисковых запросов в REST API службы поиска Azure.
3. Строка запроса автоматически преобразуется в правильный URL-адрес запроса для отправки HTTP-запроса в REST API службы поиска Azure.

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->



<properties
    pageTitle="Отправка запросов в индекс службы поиска Azure с помощью портала Azure | Microsoft Azure | Размещенная облачная служба поиска"
    description="Создайте поисковый запрос в проводнике поиска на портале Azure."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"
/>
# Отправка запросов в индекс службы поиска Azure с помощью портала Azure
> [AZURE.SELECTOR]
- [Обзор](search-query-overview.md)
- [Портал](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

В этом руководстве показано, как отправлять запросы в индекс службы поиска Azure с портала Azure.

Прежде чем приступать к выполнению инструкций из этого руководства, необходимо [создать индекс службы поиска Azure](search-what-is-an-index.md) и [заполнить его данными](search-what-is-data-import.md).

## 1\. Переход к колонке службы поиска Azure
1. Щелкните "Все ресурсы" в меню слева на [портале Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Выберите службу поиска Azure.

## 2\. Выберите индекс, в котором нужно выполнить поиск.
1. Выберите индекс, в котором нужно выполнить поиск, на плитке "Индексы".

![](./media/search-explorer/pick-index.png)

## 3\. Щелкните плитку "Проводник поиска".
![](./media/search-explorer/search-explorer-tile.png)

## 3\. Начните поиск.
1. Для поиска в индексе службы поиска Azure начните вводить запрос в поле *Строка запроса*, а затем щелкните **Поиск**.
 * При использовании проводника поиска можно указать любые [параметры запроса](https://msdn.microsoft.com/library/dn798927.aspx).

2. В разделе *Результаты* результаты запроса будут показаны в необработанном файле JSON, который вы должны получить в тексте HTTP-ответа при отправке поисковых запросов в REST API службы поиска Azure.
3. Строка запроса автоматически преобразуется в правильный URL-адрес запроса для отправки HTTP-запроса в REST API службы поиска Azure.

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0601_2016-->
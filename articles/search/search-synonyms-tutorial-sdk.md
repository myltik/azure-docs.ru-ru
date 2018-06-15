---
title: Руководство по синонимам в службе "Поиск Azure" | Документация Майкрософт
description: Добавление функции синонимов в индекс в службе "Поиск Azure".
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 5482185a4a4cc8b76c1094ce12a7ac52985ec57c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182095"
---
# <a name="synonym-c-tutorial-for-azure-search"></a>Руководство по C# для синонимов в службе "Поиск Azure"

Синонимы позволяют расширить запрос с помощью сопоставления семантически эквивалентных терминов с входными терминами. Например, вы можете сопоставить термин "машина" с документами, в которых встречается слово "автомобиль" или "транспортное средство".

В Поиске Azure синонимы определены в *сопоставлении синонимов* с помощью *правил сопоставления*, связывающих эквивалентные термины. Вы можете создать несколько сопоставлений синонимов, разместить их как ресурс служб, доступный для любого индекса, а затем указать ссылку на используемый на уровне поля. Во время выполнения запроса, кроме поиска индекса, Поиск Azure обязательно просмотрит сопоставление синонимов, если один из них указан в каком-либо поле, используемом в этом запросе.

> [!NOTE]
> Сейчас функция синонимов поддерживается только последними версиями API и пакетов SDK (API версии 2017-11-11 и пакетом SDK версии 5.0.0). Портал Azure такую поддержку пока не предоставляет. Если вы считаете, что нам следует добавить поддержку синонимов на портале Azure, оставьте свой отзыв на [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="prerequisites"></a>предварительным требованиям

Ниже приведены предварительные требования, описанные в этом руководстве.

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Служба поиска Azure](search-create-service-portal.md)
* [Библиотека Microsoft.Azure.Search .NET](https://aka.ms/search-sdk)
* [Использование службы поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Обзор

Значение синонимов показано в запросах "до" и "после". В этом руководстве мы используем пример приложения, выполняющий запросы и возвращающий результаты для примера индекса. Пример приложения создает небольшой индекс с именем hotels с двумя документами. Приложение выполняет поисковые запросы с помощью терминов и фраз, которые отсутствуют в индексе, что вызывает функцию синонимов, и поиск выполняется повторно. В примере кода ниже показан общий поток.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
В статье [Использование службы поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) вы ознакомитесь с действиями по созданию и заполнению примера индекса.

## <a name="before-queries"></a>Запросы "до"

С помощью `RunQueriesWithNonExistentTermsInIndex` мы отправляли запросы на поиск таких терминов, как "пять звезд", "Интернет" и "отели среднего класса".
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Ни в одном из двух индексированных документов нет этих выражений, поэтому мы используем следующие выходные данные из первой функции `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Включение поиска синонимов

Чтобы включить поиск синонимов, нам потребуется выполнить два действия. Сначала нам необходимо определить и отправить правила синонимов, а затем настроить поля для их использования. Описание этого процесса вы можете найти в `UploadSynonyms` и `EnableSynonymsInHotelsIndex`.

1. Добавьте сопоставление синонимов в свою службу поиска. С помощью `UploadSynonyms` мы определим четыре правила в сопоставлении синонимов desc-synonymmap и добавим его в службу.
```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
```
Сопоставление синонимов должно соответствовать стандартному формату `solr` с открытым кодом. Сведения об этом формате вы найдете в разделе `Apache Solr synonym format` статьи [Synonyms in Azure Search](search-synonyms.md) (Синонимы в Поиске Azure).

2. Настройте поля, поддерживающие поиск, чтобы использовать сопоставление синонимов в определении индекса. С помощью `EnableSynonymsInHotelsIndex` мы можем включить поиск синонимов для двух полей — `category` и `tags`. Для этого необходимо задать свойство `synonymMaps` для имени добавленного сопоставления синонимов.
```csharp
  Index index = serviceClient.Indexes.Get("hotels");
  index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
  index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

  serviceClient.Indexes.CreateOrUpdate(index);
```
Когда вы добавите сопоставление синонимов, необходимость в перестроении индексов отпадет. Чтобы использовать новое сопоставление синонимов, вы можете добавить это сопоставление в свою службу, а затем изменить существующие определения полей в любом индексе. Добавление новых атрибутов никак не скажется на доступности индексов. То же относится и к отключению поиска синонимов для поля. Вы можете просто задать для пустого списка свойство `synonymMaps`.
```csharp
  index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
```

## <a name="after-queries"></a>Запросы "после"

После добавления сопоставления синонимов и обновления индекса, необходимых для использования этого сопоставления, мы получаем следующие выходные данные, вызвав функцию `RunQueriesWithNonExistentTermsInIndex` второй раз:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
Первый запрос находит документ из правила `five star=>luxury`. Для второго запроса поиск расширяется с помощью термина `internet,wifi`, а для третьего при поиске соответствующих документов используются оба термина запросов — `hotel, motel` и `economy,inexpensive=>budget`.

Добавление синонимов полностью изменяет возможности поиска. При работе с этим руководством исходным запросам не удалось вернуть информативные результаты, несмотря на соответствующие документы в индексе. Включив поиск синонимов, мы можем расширить индекс, чтобы включить распространенные термины, не изменяя при этом базовые данные в индексе.

## <a name="sample-application-source-code"></a>Исходный код образца приложения
Полный исходный код образца приложения, используемого в этом пошаговом руководстве, см. в репозитории [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со сведениями об [использовании синонимов в Поиске Azure](search-synonyms.md).
* Ознакомьтесь с [документацией по API REST для синонимов](https://aka.ms/rgm6rq).
* Изучите справочную информацию о [пакете SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) и [REST API](https://docs.microsoft.com/rest/api/searchservice/).

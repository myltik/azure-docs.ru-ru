---
title: "Архитектуры с несколькими базами данных master с использованием Azure DocumentDB | Документация Майкрософт"
description: "Узнайте, как проектировать архитектуры приложений с локальными операциями чтения и записи в нескольких географических регионах с помощью Azure DocumentDB."
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 788a1b9ef6a470c8f696228fd8fe51052c4f7007
ms.openlocfilehash: 15c5a8be1097253e88af3a9f36b9067f0e2fbba3


---
# <a name="multi-master-database-architectures-with-azure-documentdb"></a>Архитектуры с несколькими базами данных master с использованием Azure DocumentDB
DocumentDB поддерживает готовую [глобальную репликацию](documentdb-distribute-data-globally.md), что позволяет распространять данные в нескольких регионах и обеспечивает доступ с низкой задержкой в любом месте рабочей нагрузки. Эта модель обычно используется для рабочих нагрузок типа "издатель и потребитель", в которых модуль записи находится в одном географическом регионе, а глобально распределенные модули чтения — в других регионах (регионах чтения). 

Поддержку глобальной репликации DocumentDB можно также использовать для создания приложений с глобально распределенными модулями записи и чтения. В этом документе рассматривается шаблон, который позволяет обеспечить локальные операции записи и чтения для распределенных модулей записи с помощью Azure DocumentDB.

## <a name="a-idexamplescenarioacontent-publishing---an-example-scenario"></a><a id="ExampleScenario"></a>Пример сценария публикации содержимого
Рассмотрим реальный сценарий, чтобы показать, как можно использовать шаблоны чтения и записи в глобально распределенных средах с несколькими регионами или несколькими базами данных master с помощью DocumentDB. Рассмотрим платформу публикации содержимого, созданную на базе DocumentDB. Ниже приведены некоторые требования, которым эта платформа должна соответствовать, чтобы обеспечить удобство работы пользователя как с издателями, так и с потребителями.

* Авторы и подписчики разбросаны по всему земному шару. 
* Авторам необходимо публиковать (записывать) статьи в своем локальном (ближайшем) регионе.
* У авторов статей есть читатели и подписчики, разбросанные по всему миру. 
* Подписчики должны получать уведомление при публикации новой статьи.
* Подписчики должны иметь возможность читать статьи в своем локальном регионе. Они также должны иметь возможность добавлять рецензентов этих статей. 
* Кто угодно, включая автора статей, должен иметь возможность просматривать все рецензии, прилагаемые к статьям из локального региона. 

Предполагая участие миллионов потребителей и издателей и наличие миллиардов статей, очень скоро мы столкнемся с проблемами масштабирования и гарантирования локальности доступа. Как с большинством проблем масштабируемости, решение заключается в качественной стратегии секционирования. Давайте рассмотрим, как смоделировать статьи, рецензии и уведомления как документы, настроить учетные записи DocumentDB и реализовать уровень доступа к данным. 

Если вы хотите узнать больше о секционировании и ключах секций, ознакомьтесь с разделом [Секционирование и масштабирование в Azure DocumentDB](documentdb-partition-data.md).

## <a name="a-idmodelingnotificationsamodeling-notifications"></a><a id="ModelingNotifications"></a>Моделирование уведомлений
Уведомления — это веб-каналы данных конкретного пользователя. Следовательно, шаблоны доступа для документов уведомлений всегда находятся в контексте одного пользователя. Например, можно "отправить уведомление пользователю" или "получить все уведомления для заданного пользователя". Поэтому оптимальным выбором типа ключа секционирования будет `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a name="a-idmodelingsubscriptionsamodeling-subscriptions"></a><a id="ModelingSubscriptions"></a>Моделирование подписок
Подписки могут быть созданы для различных критериев, например для определенной интересующей категории статей или определенного издателя. Поэтому `SubscriptionFilter` — удачный выбор для ключа секции.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a name="a-idmodelingarticlesamodeling-articles"></a><a id="ModelingArticles"></a>Моделирование статей
После определения статьи посредством уведомлений последующие запросы обычно основаны на `ArticleId`. Поэтому выбор `ArticleID` в качестве ключа секции обеспечивает наилучшее распространение для хранения статей в коллекции DocumentDB. 

    class Article 
    { 
        // Unique ID for Article public string Id { get; set; }
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a name="a-idmodelingreviewsamodeling-reviews"></a><a id="ModelingReviews"></a>Моделирование рецензий
Как и статьи, рецензии главным образом создаются и читаются в контексте статьи. Если выбрать `ArticleId` в качестве ключа секции, это обеспечит наилучшее распространение рецензий, связанных со статьей, и эффективный доступ к ним. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a name="a-iddataaccessmethodsadata-access-layer-methods"></a><a id="DataAccessMethods"></a>Методы уровня доступа к данным
Теперь давайте рассмотрим основные методы доступа к данных, которые нужно реализовать. Ниже приведен список методов, требуемых для `ContentPublishDatabase`.

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a name="a-idarchitectureadocumentdb-account-configuration"></a><a id="Architecture"></a>Конфигурация учетной записи DocumentDB
Чтобы обеспечить локальные операции чтения и записи, необходимо секционировать данные не только по ключу секции, но и с учетом шаблона географического доступа к регионам. Модель основана на использовании геореплицируемой учетной записи базы данных Azure DocumentDB для каждого региона. Например, ниже приведена конфигурация операций записи в несколько регионов в среде с двумя регионами.

| Имя учетной записи | Регион записи | Регион чтения |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

На следующей схеме показано, как при такой конфигурации выполняются чтение и запись в типичном приложении.

![Архитектура с несколькими базами данных master на основе Azure DocumentDB](./media/documentdb-multi-region-writers/documentdb-multi-master.png)

Ниже приведен фрагмент кода, показывающий, как инициализировать на уровне доступа к данным клиенты, работающие в регионе `West US`.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

В приведенной ранее конфигурации уровень доступа к данным может направлять все операции записи в локальную учетную запись в зависимости от того, где она развернута. При операциях чтения выполняется чтение из обеих учетных записей для получения глобального представления данных. Этот подход можно распространить на любое число регионов. Например, ниже приведена конфигурация с тремя географическими регионами.

| Имя учетной записи | Регион записи | Регион чтения 1 | Регион чтения 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a name="a-iddataaccessimplementationadata-access-layer-implementation"></a><a id="DataAccessImplementation"></a>Реализация уровня доступа к данным
Теперь давайте рассмотрим реализацию уровня доступа к данным (DAL) для приложения с двумя регионами для записи. DAL должен реализовывать следующие действия.

* Создание нескольких экземпляров `DocumentClient` для каждой учетной записи. В конфигурации с двумя регионами у каждого экземпляра DAL имеется по одному `writeClient` и `readClient`. 
* Настройка конечных точек для `writeclient` и `readClient` на основе региона развертывания приложения. Например, DAL, развернутый в `West US`, использует `contentpubdatabase-usa.documents.azure.com` для выполнения операций записи. DAL, развернутый в `NorthEurope`, для операций записи использует `contentpubdatabase-europ.documents.azure.com`.

В представленной ранее конфигурации можно реализовать методы доступа к данным. Операции записи перенаправляют запись к соответствующему `writeClient`.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Для чтения уведомлений и рецензий необходимо считать данные из обоих регионов и объединить результаты, как показано в следующем фрагменте кода.

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Таким образом, выбрав подходящий ключ секционирования и статическое секционирование на основе учетных записей, с помощью Azure DocumentDB можно достичь локальности операций записи и чтения в нескольких регионах.

## <a name="a-idnextstepsanext-steps"></a><a id="NextSteps"></a>Дальнейшие действия
В этой статье на примере сценария публикации содержимого показано, как с помощью DocumentDB использовать шаблоны чтения и записи в глобально распределенных средах с несколькими регионами.

* Узнайте о том, как в DocumentDB поддерживается [глобальное распространение](documentdb-distribute-data-globally.md).
* Узнайте об [автоматической и ручной отработках отказа в Azure DocumentDB](documentdb-regional-failovers.md).
* Узнайте о [глобальной согласованности в DocumentDB](documentdb-consistency-levels.md).
* Изучите разработку с использованием нескольких регионов с помощью [пакета SDK Azure для DocumentDB](documentdb-developing-with-multiple-regions.md).


<!--HONumber=Jan17_HO4-->



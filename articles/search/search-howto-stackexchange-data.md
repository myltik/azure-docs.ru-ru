<properties
	pageTitle="Поиск данных StackExchange с помощью Поиска Azure | Microsoft Azure | Размещенная облачная служба поиска"
	description="Узнайте, как выполнять поиски REST с помощью Поиска Azure, размещенной облачной службы поиска в Microsoft Azure."
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="liamca"/>

# Как искать данные в StackExchange с помощью поиска Azure

Эта статья является пошаговым руководством, в котором рассматривается ряд основных возможностей полнотекстового поиска, предоставляемых [поиском Azure](https://azure.microsoft.com/ru-RU/services/search/). В ней используются данные, к которым сеть сайтов StackExchange [предоставила доступ](https://archive.org/details/stackexchange) по лицензии Creative Commons (с тем условием, чтобы соблюдались правила установления авторства, описанные в [этой статье](http://blog.stackoverflow.com/2009/06/attribution-required/)).

## Приступая к работе

Чтобы рассказать о некоторых возможностях, я создал доступный индекс поиска Azure, который содержит данные из сайта Programmer StackExchange от 14 октября 2015 года. Примечание. Я покажу также, как создать свой собственный индекс поиска Azure с этими данными.

Давайте начнем с простого полнотекстового поискового запроса, когда пользователь вводит слово «azure», чтобы найти все записи в StackExchange, относящиеся к Azure. Посмотрите на результаты, щелкнув эту ссылку:

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

В этом примере мы передаем слово «azure» в качестве параметра поиска и отображаем результаты, полученные в формате JSON. Ниже приведено еще несколько примеров запросов.

-	`Faceting`: когда пользователь выполняет поиск по набору данных, возможность фильтровать данные является очень полезным дополнением. Чтобы реализовать эту возможность, вначале обычно нужно определить набор категорий (аспектов), отображаемых для пользователя. Вот несколько примеров аспектов, которые вы можете использовать.
  -	**Теги**: для большинства вопросов имеются связанные теги, которые позволяют пользователям сужать поиск по определенным категориям.
  -	**Даты**: пользователь может захотеть увидеть только те вопросы, которые были заданы или на которые были даны ответы в течение определенного периода.
  -	**Пользователь**: вы можете пожелать просмотреть или исключить результаты от отдельных пользователей. В этом примере мы выполним поиск по слову «azure», но вернем количество аспектов для коллекции tagsCollection и имен пользователей acceptedAnswerDisplayName.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`: когда пользователь выберет аспект, вам потребуется выполнить новый поиск, но при этом использовать фильтр для ограничения результатов по значению аспекта. Например, выполнить поиск по слову «Azure», но ограничить результаты тегом «архитектура», упорядочив их по столбцу viewCount в порядке убывания, вы можете следующим образом:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Spelling Mistakes`: наша новая функция поддержки [выражений запросов Lucene](https://msdn.microsoft.com/library/mt589323.aspx) (предварительная версия) также позволяет создавать некоторые довольно необычные запросы, например на нечеткое соответствие результатов или ограничение поиска конкретными полями. В этом примере выполняется поиск слова «visualize» в поле названия, но знак ~ указывает на нечеткое соответствие, что допускает включение в результаты слов «visualise» и «visualizing».

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: [профили оценки](https://msdn.microsoft.com/library/azure/dn798928.aspx) очень полезны при настройке результатов, возвращаемых службой поиска. Теперь они позволяют оценивать отдельные поля и слова в режиме реального времени с помощью [выражений запросов Lucene](https://msdn.microsoft.com/library/mt589323.aspx). Например, чтобы выполнить поиск слов «visualize» и «chart» в поле заголовка с предоставлением приоритета слову «chart», мы можем сделать следующее:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  В этом наборе данных имеется множество других полей, которые можно использовать для того, чтобы поднимать нужные результаты для пользователей. Например, можно использовать:

  -	Оценку по **величине** для числовых полей, таких как answerCount, commentCount, favoriteCount и viewCount, чтобы в результатах поиска поднять элементы с высокими значениями в этих полях.
  -	Оценку **актуальности** для полей даты и времени, таких как creationDate и lastActivityDate, чтобы в результатах поиска поднять элементы, созданные недавно или в которых в последнее время наблюдалась активность.
  -	**Значимость полей**, чтобы искомый текст, обнаруженный в теле вопроса, имел приоритет над таким же текстом, обнаруженным в ответе.

Другие возможности, с которыми вы можете поэкспериментировать:

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx): когда пользователи вводят текст в поле поиска, для автозавершения удобнее использовать такие поля, как «Заголовок», «Теги» и «Имя пользователя».  

-	`Recommendations`: зачастую для создания рекомендаций, отображающих похожие вопросы, которые могут быть интересны пользователям, вы будет использовать такие инструменты, как Apache Mahout или машинное обучение Azure. К счастью, в этом наборе данных уже имеются некоторые рекомендации.

Вы можете поэкспериментировать со страницей JSFiddle, используя различные типы запросов. Далее будет подробнее рассказано том, как был создан этот индекс. Для связи со мной вы можете написать в Твиттер (пользователь [@liamca](https://twitter.com/liamca)).

## Как был создан этот индекс поиска Azure

Брент проделал большую работу, показав способы размещения данных в базе данных SQL. Если вы хотите пройти процедуру размещения данных самостоятельно, ознакомьтесь с [этим учебником](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/). В противном случае вы можете пользоваться базой данных SQL Azure, которую я заполнил данными от 15 октября 2015 года, либо пользоваться созданным мной индексом поиска Azure. Подробные сведения описаны далее в разделе «Импорт данных из промежуточной базы данных SQL Azure». Единственное, что я сделал в дополнение к инструкциям Брента, — это создал режим в моей базе данных SQL Azure, который будет использоваться [индексатором поиска Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx) для сканирования и приема данных из группы интересных мне таблиц. Вот как определен этот режим.

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

После этого вы можете использовать [портал Azure](https://portal.azure.com) для импорта данных из приведенного выше режима SQL Azure. Затем на основе схемы полей в этом режиме будет создан индекс поиска Azure на портале. Если вы хотите использовать созданную мной базу данных SQL Azure, воспользуйтесь следующей строкой подключения (только для чтения):

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=Nov15_HO3-->
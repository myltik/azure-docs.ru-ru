<properties 
	pageTitle="Переход c предварительной версии api-version= 2014* на версию api-version=2015*" 
	description="Узнайте о критических изменениях и о том, как осуществить перенос кода, написанного при помощи 2014-07-31-Предварительная или 2014-10-20-Предварительная в Поиске Azure версии api-version=2015-02-28." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

#Переход c предварительной версии api-version= 2014* на версию api-version=2015*#

Поиск Azure — это размещенная облачная служба поиска в Microsoft Azure. Данное руководство предназначено для пользователей, ранее разрабатывавших клиентские приложения с помощью предварительных версий Поиска Azure, которые мигрируют на общедоступную версию 2015-02-28.

Ранее вы, вероятно, использовали одну из этих предварительных версий:

- [2014-07-31-Предварительная](search-api-2014-07-31-preview.md)
- [2014-10-20-Предварительная](search-api-2014-10-20-preview.md)

Теперь, когда Поиск Azure стал общедоступным,  
мы рекомендуем переход на более новые выпуски продукта: 2015-02-28 — официальная версия API общедоступного выпуска Поиска Azure. Документация по данной версии доступна в [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx).

Также мы запускаем следующую предварительную версию [2015-02-28-Предварительная](search-api-2015-02-28-preview.md), представляющую функции, разработка которых еще продолжается. Отзыв о предварительной версии API можно отправить через [форумы службы поиска Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch) или со [страницы обратной связи](http://feedback.azure.com/forums/263029-azure-search).

###Контрольный список для миграции###

- Просмотрите критические изменения с тем, чтобы определить, повлияют ли они на ваше решение.
- Активируйте версию API в `2015-02-28` для заблокированной версии. Данная версия регулируется Соглашением об уровне обслуживания. Это позволяет быстрее решить проблемы, если таковые возникнут.
- Выполните сборку, разверните и протестируйте ее. За исключением критических изменений, соответствие в поведении поиска должно быть 100 %.
- Разверните сборку в рабочей среде.
- Оцените новые возможности для внедрения и дальнейшего использования. Снова активируйте 2015-02-28-Предварительную, если хотите протестировать процессоры естественного языка Microsoft или `morelikethis`.

##Критические изменения в api-version=2015*##

Первоначальный выпуск API включал функцию автозавершения или опережающего ввода с клавиатуры. Будучи в целом полезной, эта функция ограничивалась только префиксными соответствиями, осуществляя поиск по первым знакам поискового запроса, и не отслеживала совпадения по всему полю поиска. Реализовано это было логическим свойством `suggestions`, которое при необходимости задействовать префиксное сопоставление в определенном поле нужно было задавать как `true`.

В настоящее время такая реализация заменяется новой конструкцией `Suggesters`, определяемой в средстве [индекс](https://msdn.microsoft.com/library/azure/dn798941.aspx) и отслеживающей инфиксные и нечеткие соответствия. Как следует из самих названий, инфиксные и нечеткие соответствия обеспечивают гораздо более широкие возможности сопоставления. Инфиксные соответствия включают в себя и префиксные в том смысле, что по-прежнему отслеживаются соответствия в начале слов, но, кроме этого, отслеживаются и соответствия по всей строке.

Мы решили отказаться от предыдущего варианта реализации (свойство логического типа). Это означает, что он не будет доступен ни в одной из версий 2015 года без обратной совместимости. Это сделано для того, чтобы клиенты случайно не применили этот вариант при сборке новых решений. Для того чтобы задействовать функцию опережающего ввода, теперь придется воспользоваться новой конструкцией `Suggesters` при использовании как `2015-02-28`, так и `2015-02-28-Preview`.

##Перенос существующего кода##

Единственное критическое изменение — функция подсказки. Если эта функция не используется, можно активировать `api-version` из`2014-07-31-Preview` или `2014-10-20-Preview` с `2015-02-28` и затем повторно осуществить сборку и развертывание. Приложение будет работать, как и раньше.

Для реализации функции подсказки в пользовательских приложениях следует сделать следующее:

1. Обновите все пакеты NuGet.
1. Активируйте версию API в  
`2015-02-28`. При использовании приведенного ниже примера кода применяется версия API класса **AzureSearchHelper**.
1. Удалите атрибут `Suggestions={true | false}` из схемы JSON, определяющей индекс.
1. Добавьте конструкцию в нижней части индекса для `Suggesters` (как показано в разделе [после](#after)).
1. Убедитесь в возможности публикации в службе (во избежание конфликта имен может потребоваться переименование индекса).
1. Перестройте решение и осуществите развертывание в тестовой среде.
1. Выполните все тестовые случаи, чтобы убедиться в правильности работы решения.
1. Разверните сборку в рабочей среде.

Пример кода с [Adventure Works sample on codeplex](https://azuresearchadventureworksdemo.codeplex.com/) имеет оригинальную реализацию `Suggestions`. Его можно использовать для того, чтобы отработать перенос кода на примере кода.

В следующем разделе будет рассказано о реализации предложений [до](#before) и [после](#after). Можно заменить метод **CreateCatalogIndex()** версией из раздела [после](#after), а затем построить и развернуть решение, чтобы протестировать новые функциональные возможности.

<a name="before"></a>
###До###

Как видите, `Suggestions` являются логическими свойствами, заданными для каждого поля. Удалите все атрибуты `Suggestions`.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###После###

В перенесенном определении схемы отсутствует свойство `Suggestions` и добавлена конструкция `Suggesters` снизу.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##Оценка новых функций и подходов##

После переноса решения и подтверждения правильности его работы можно воспользоваться следующими ссылками для получения сведений о новых возможностях:

- [Azure Search в общем доступе (публикация в блоге)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [ Новые возможности недавних обновлений Azure Search](search-latest-updates.md)
- [Что такое поиск Azure?](search-what-is-azure-search.md)

##Получение справки##

Версия API `2015-02-28` регулируется Соглашением об уровне обслуживания. Чтобы подать запрос в службу поддержки воспользуйтесь информацией и ссылками, размещенными на [странице](../support/options/).

 

<!---HONumber=Nov15_HO2-->
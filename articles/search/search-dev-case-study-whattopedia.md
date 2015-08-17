<properties 
	pageTitle="Пример разработки решения в службе &quot;Поиск Azure&quot;: как компания WhatToPedia создала информационный портал в службе Microsoft Azure" 
	description="Узнайте, как создать информационный портал и поисковую систему, использующую метаданные, с помощью службы поиска Microsoft Azure." 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Пример разработки решения в службе "Поиск Azure"

## Как администраторы сайта [WhatToPedia.com](http://whattopedia.com/) создали информационный портал в Microsoft Azure

 ![][6] & nbsp; & nbsp; & nbsp; <font size="9">Великолепная идея</font>


Наша идея заключается в создании информационного портала, на котором покупатели могут связываться с розничными торговцами, используя широкие возможности поиска, подобно тому, как туристы находят гостиницы, рестораны и развлечения с помощью специальных порталов для путешественников.

Наш портал должен обеспечивать высококачественный поиск данных о розничных торговцев на указанных рынках, помогая находить магазины в определенном регионе, а также предоставляя сведения о всех предоставляемых ими услугами. Поисковая система будет заполнена начальными значениями из набора данных, который со временем будет пополняться сведениями, предоставляемыми розничными торговцами, которые зарегистрировались на сайте. Наш сайт должен регулярно пополняться данными о рекламных акциях, новом товаре, популярных торговых марках, специальных услугах и подобной информацией. После регистрации розничного торговца на сайте в качестве подписчика он самостоятельно предоставляет данные, которые интегрируются в совокупность искомых данных. Мы планируем получать выручку за счет рекламы, а также модели подписки.

Основной моделью взаимодействия с пользователем будет поисковая система, в основе которой лежит облачная платформа. В целях масштабирования и сокращения стоимости почти все наши действия (начиная с администрирования порталом и заканчивая системой управления версиями) будут выполняться через интернет-службы. Нам требуется поисковая система, которая предоставляет готовые к использованию функции, с помощью которой мы сможем быстро создать приложение поиска без необходимости самостоятельного управления поисковой системой.

## Результат

WhatToPedia — это стартап-компания, работающая в сфере информационных услуг. Мы создали сайт [WhatToPedia.com](http://whattopedia.com/), который в настоящее время тестируется на рынках стран Северной Европы. Выпуск полнофункциональной версии намечен на 2 февраля 2015 г. Наши клиенты — в первую очередь традиционные магазины, которые хотят быть представленными в Интернете, не затрачивая при этом большие средства.

Наша задача — привлечь покупателей с помощью широкий возможностей интернет-поиска данных по городу, району, торговым маркам, магазинам или их типам. Привлечение покупателей мотивирует розничных торговцев зарегистрироваться на сайте портала. За подписку взимается невысокая плата.

 ![][7]

После подписки розничный торговец получает собственный профиль (который мы создаем на базе приобретенных данных) и пополняет его дополнительными сведениями об акциях, популярных торговых марках или новых продуктах и услугах. Встроенные возможности, например поддержка различных языков и валют или освобождение от уплаты налогов при покупках, дополнительно привлекают покупателей.

## О нас

Я — Томас Сегато (Thomas Segato), сотрудник подразделения Microsoft Consulting. Я разработал нужное решение в сотрудничестве с Джеспером Боеллингом (Jesper Boelling), ведущим разработчиком ПО компании WhatToPedia.

WhatToPedia — это стартап-компания, которая тестирует свой новый портал в Швеции, где большинство 60 000 розничных торговцев являются традиционными предприятиями малого и среднего размера. Поскольку мы знаем, что средний европейский покупатель говорит на нескольких языках и использует несколько валют, мы разработали решения с поддержкой нескольких языков. Служба "Поиск Azure", поддерживающая несколько языков, оказалась именно тем, что нам нужно.

Она определила дальнейшее развитие нашего проекта. До появления службы "Поиск Azure" мы потратили много усилий на разработку все собственной поисковой системы. Интернет-служба "Поиск Azure" освободила наше решение от ряда технических и административных нагрузок, что позволило нам быстро выйти на рынок с надежной поисковой системой.

## Как мы это сделали

Мы хотели создать комплексную инфраструктуру на основе исключительно облачных служб. В качестве стратегического партнера платформы мы выбрали корпорацию Майкрософт, так как она обеспечивала необходимые услуги (в областях совместной работы и разработки) и гибкие возможности масштабирования по требованию, и все это по невысокой цене.
 
### Высокоуровневые компоненты

Мы создали компанию, а не просто сайт. Для этого нам потребовался широкий спектр средств и приложений. Решения Visual Studio и Visual Studio Online использовались для разработки, служба Online Team Foundation Service (TFS) — для управления версиями и методом Scrum, Office 365 — для общения и совместной работы, а Microsoft Azure — для всех операций, связанных с сайтом и хранилищем. Интерфейс IDE, доступный в Visual Studio, обеспечил непосредственную подготовку службы Azure к работе, а интеграция с TFS Online позволила дополнительно повысить производительность.

На следующей схеме показаны высокоуровневые компоненты, используемые в инфраструктуре WhatToPedia.

   ![][8]

### Как мы используем службу Microsoft Azure

Зеленые прямоугольники на схеме выше показывают, что решение WhatToPedia создано на основе следующих служб и компонентов:

- [Поиск Azure;](http://azure.microsoft.com/services/search/)
- [веб-сайты Azure, разработанные с использованием платформы MVC 4;](http://azure.microsoft.com/services/websites/)
- [веб-задания Azure для запланированных задач;](../websites-webjobs-resources.md)
- [база данных SQL Azure;](http://azure.microsoft.com/services/sql-database/)
- [хранилище BLOB-объектов Azure;](http://azure.microsoft.com/services/storage/)
- [почтовая служба SendGrid.](http://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Решение в первую очередь предназначено для поиска и работы с данными. Ниже показан поток данных от поставщика услуг розничной торговли к конечному потребителю.

  ![][9]

Основные данные хранятся у розничного торговца, а данные бухгалтерской программы — в базе данных SQL Azure. Она состоит из исходного набора данных, а также относящихся к розничному торговку данных, которые добавляются с течением времени. Мы используем веб-задания Azure для отправки обновлений из базы данных SQL в совокупность искомых данных в службе "Поиск Azure".

### Уровень представления данных

Портал — это веб-сайт Azure, реализованный с помощью платформ MVC 4 и [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Мы выбрали платформу MVC, так как она обеспечивает более понятную обработку данных в формате HTML по сравнению с разработкой на основе форм ASP.NET. Чтобы не создавать приложения для нескольких устройств и обеспечить поддержку различных мобильных платформ, мы выбрали платформу Twitter Bootstrap.

### Проверка подлинности, разрешения и конфиденциальные данные

Покупатели просматривают содержимое сайта анонимно. Таким образом, им не нужно входить на сайт. Кроме того, мы не храним какие-либо данные о потребителях.

Розничные торговцы — совсем другая история. Здесь хранятся общедоступные сведения о профиле и выставлении счетов, а также мультимедийное содержимое, которые торговцы хотят опубликовать на сайте. Каждый розничный торговец, который зарегистрировался на сайте, получает имя для входа, используемое для проверки подлинности пользователя, прежде чем он сможет вносить обновления в профиль подписчика. Мы безопасно храним все данные подписчиков в базе данных SQL Azure и хранилище BLOB-объектов Azure. Мы выбрали модель проверки подлинности на основе форм .NET. Мы выбрали этот подход благодаря его простоте; мы не нуждались в ролях, поддержке пользовательского интерфейса и дополнительных функциях, используемых в других моделях.

Чтобы розничные торговцы могли просматривать только собственные данные, каждому торговцу присваивается идентификатор, который впоследствии используется для всех операций чтения и записи с использованием данных конкретного торговца. Благодаря этому нам не нужно предоставлять разрешения на доступ к базе данных отдельным розничным продавцам. Все розничные торговцы взаимодействуют с системой под одной ролью базы данных. При этом для изоляции данных используется идентификатор торговца.

Поскольку задача нашего сайта — обслуживание нижних звеньев производственно-сбытовой цепочки (обеспечение деятельности розничных торговцев, стимулирование рекламы и подписки), мы можем провести границу для обработки покупок в Интернете. Поэтому на сайте нет корзины, что упрощает требования к безопасности.

Чтобы упростить работу, мы также воспользовались услугами сторонних поставщиков для выставления счетов и расчетов с поставщиками. Сведения о платежах клиентов передаются напрямую стороннему поставщику ([SveaWebPay](http://www.sveawebpay.se/)), что снижает риски, связывание с хранением и защитой конфиденциальных данных в наших хранилищах.

### Поисковая система

Сердце нашего решения — поисковая система, созданная на основе службы "Поиск Azure". Изначально мы создали пользовательскую поисковую систему, но в процессе разработки поняли, что она слишком сложная. Поэтому мы решили рассмотреть другие возможности.

Особую важность для нас представляли следующие основные функции.

- Фильтры
- Фасетная навигация
- Повышение количества результатов поиска
- Подкачка с помощью технологии AJAX

В Интернете мы нашли [видео с конференции TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410), в котором подробно рассматривалась служба "Поиск Azure", к которой мы и решили обратиться.

После просмотра видео мы все были готовы создать прототип на основании увиденного. У нас уже была модель данных, созданная на базе платформы MVC, поэтому мы быстро создали прототип: данные содержали условия, поддерживающие поиск, и у нас уже имелись разработанные требования к сортировке, фасетной навигации и фильтрации данных.

Вот так мы создали прототипа.

**Настройка службы "Поиск Azure"**

1. Мы вошли на портал Azure и подписались на службу поиска. Мы использовали общедоступную версию (бесплатную с нашей подпиской).
2. Мы создали индекс. В прототипе мы использовали пользовательский интерфейс портала, чтобы определить поля поиска и создать профили оценки. Наш профиль оценки базировался на данных о местоположении: "страна" | "город" | "адрес" (см. статью "Добавление оценки профилей").
3. Мы скопировали URL-адрес службы и ключ API администратора в наши файлы конфигурации. Этот ключ находится на странице службы поиска на портале и используется для проверки подлинности в службе.
	
**Разработка задания индексатора поиска — консоль Windows**

1. Мы считали сведения о всех розничных торговцев из базы данных.
2. Мы вызвали интерфейс API службы "Поиск Azure", чтобы последовательно передать данные о торговцах (см. статью по следующей ссылке: http://msdn.microsoft.com/library/azure/dn798930.aspx)).
3. В базе данных мы задали свойство, требующее добавочного индексирования розничных торговцев. Для этого мы добавили поле "indexer" (индексатор), в котором планировалось хранить состояния индексов всех профилей (вне зависимости от того, индексированы ли они). 

Фрагмент кода, создающий задание индексатора, приведен в приложении.

**Разработка веб-портал поиска — MVC**

1. Затем мы вызвали службу "Поиск Azure", чтобы получить все документы, найденные в результате поиска (см. статью по следующей ссылке: http://msdn.microsoft.com/library/azure/dn798927.aspx).
2. После этого мы извлекли следующие данные после ответа службы поиска (с помощью json.net: http://james.newtonking.com/json).
   - Результаты
   - Аспекты
   - Количество результатов
   - Мы разработали пользовательский интерфейс для отображения результатов поиска, аспектов и количественных показателей (у нас уже были эти данные).

Вот код, который мы использовали для получения результатов с помощью службы "Поиск Azure":

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Повышение эффективности поиска по местоположению**

Возможно, самым важным требованием для проверки прототипа являлось добавление в поисковый запрос ключевого слова, указывающего на местоположение. Нам было крайне важно, чтобы при вводе в поисковый запрос названия города розничные торговцы, представленные в этом городе, получали бы более высокий приоритет, чем торговцы, которые указали это же ключевое слово в своем описании. Поэтому в профиле оценки для поля City (Город) был задан более высокий приоритет, чем для других полей.

**Поддержка нескольких языков**

Нам требовалось отображать правильные результаты поиска на нужном языке, а также предоставить возможность поиска тех же результатов на разных языках. Нужно было решить две стороны этой проблемы:

- поиск слов на нескольких языках;
- отображение результатов поиска на правильном языке.

Что касается представления данных, мы добавили документ для каждого языка с локализованным текстом и свойством определенного языка. Когда пользователь вводит условие поиска, для фильтрации данных на выбранном им языке используется выражение `$filter`.

Каждый документ имеет скрытое свойство "cities" (города), базирующееся на типе сбора данных. В этом свойстве хранятся названия городов на всех языках, что позволяет пользователю искать данные на нескольких языках.

###Хранилище данных

Все данные (профиль, сведения о подписке и бухгалтерском учете) хранятся в базе данных SQL. Все мультимедийные файлы (включая предоставленные розничным торговцем изображения и видеозаписи) хранятся в хранилище BLOB-объектов Azure. Отдельное хранилище BLOB-объектов позволяет избежать нагрузок, связанных с отправкой файлов. Файлы никогда не смешиваются с содержимым веб-сайта, поэтому нам не нужно перестраивать сайт при каждом добавлении файлов.

Важное преимущество — возможность совместного использования хранилища данных для разработки несколькими разработчиками. Одно из требований проекта WhatToPedia заключалось в возможности создать среду разработки (включая данные о розничных торговцах, изображения и видео) в течение 15 минут. Благодаря получению последних данных из службы TFS Online, выполнению сценария SQL и запуску задания импорта вся среда успешно развертывается за считанные минуты. Кроме того, повышается эффективность промежуточного хранения.

###Веб-задания

С помощью веб-заданий Azure мы обновляем данные в индексе. Благодаря созданию задания индексатора мы смогли с легкостью интегрировать индексирование в наше решение. Единственное изменение, которое мы внесли в код для поддержки задания индексатора, заключалось том, что мы добавили поле `Indexed` в модель данных, чтобы указывать состояние индекса. При каждом добавлении или обновлении нового профиля для поля `Indexed` задается значение false. Это же происходит, если розничный торговец изменяет данные своего профиля на портале.

Задание выполняет поиск всех строк, для поля `Indexed` которых указано значение false. При обнаружении строки документ публикуется в службе "Поиск Azure", после чего для поля `Indexed` задается значение true. Нам не пришлось разрабатывать отдельные решения по добавлению и обновлению данных, так как служба "Поиск Azure" отлично справляется со всеми этими операциями. При добавлении уже имеющегося документа служба автоматически обновляет его.

Все веб-задания разработаны в качестве консольных приложений, которые можно передать на веб-сайты Azure как ZIP-файлы для последующей распаковки и планирования.

Задание выполняется каждые 5 минут в качестве запланированной веб-задачи. По нашим подсчетам, служба отправляет 3000 документов в течение около трех минут, что полностью соответствует поставленным требованиям.

> [AZURE.NOTE]В службе "Поиск Azure" недавно появился такой компонент, как индексатор прототипа. Он появился слишком поздно, поэтому мы не смогли внедрить его в первой версии решения, но этот компонент решает ту же проблему, что и использованное нами задание индексатора, то есть автоматизирует операции загрузки данных.


###Стратегия резервного копирования

Мы разработали стратегию многоуровневого резервного копирования, которая обеспечивает восстановление данных в различных сценариях, начиная с катастрофического сбоя и заканчивая восстановление отдельных транзакций. В защите нуждаются ресурсы трех типов: содержимое веб-сайта, данные подписчиков и файлы мультимедиа.

Во-первых, исходный код веб-сайта хранится в службе в TFS Online, поэтому мы всегда сможем повторно опубликовать сайт из этой службы, если он выйдет из строя.

Наиболее уязвимый ресурс — данные подписчиков в базе данных SQL Azure. Для их резервного копирования мы используем встроенный компонент (см. статью [Резервное копирование и восстановление баз данных SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)). Раз в неделю выполняется резервное копирование всей базы данных, один раз в неделю — разностное резервное копирование базы данных, а каждые 5 минут сохраняются резервные копии журналов транзакций. Учитывая текущий и прогнозируемый объем наших томов данных, это решение в полной мере справится с поставленными задачами.

В-третьих, изображения и видеофайлы хранятся в хранилище BLOB-объектов Azure. Мы все еще разрабатываем конечный план резервного копирования этих данных, рассматривая в качестве возможного решения средство Cloudberry Explorer for Azure. В настоящее время мы копируем изображения и видео в другие расположения с помощью веб-заданий.

##Чему мы научились

Мы с легкостью провели эксперимент, так как у нас уже были данные. В течение считанных часов мы разработали прототип, поддерживающий аспекты, счетчики, подкачку, упорядоченные профили и результаты поиска. Результаты поиска были отличались такой точностью, что мы решили удалить некоторые фильтры, которые отображались конечному потребителю.

Больше всего нас удивило то, как быстро мы научились использовать Поиск Azure и что получили взамен. По сути, мы провели эксперимент за несколько часов (см. примечание ниже). Мы написали вместо 500 строк кода всего 3 строки кода в приложении переднего плана и добавили новое веб-задание, что позволили улучшить результаты поиска.

Ранее наш код реализовал подкачку, счетчики и другие компоненты, традиционно используемые при поиске. Служба "Поиск Azure" обеспечивает все необходимые компоненты (поиск, аспекты, подкачка, счетчики), которые нам приходилось самостоятельно разрабатывать. Она также повышает эффективность поиска и поддерживает фасетную навигацию — возможности, отсутствующие в нашем оригинальном решении.

Самая большая трудность во время реализации решения состояла в том, что оно являлось предварительной версией, что усложняло поиск сведений и отзывов. В конце-концов мы увидели, что служба "Поиск Azure" довольно проста в использовании благодаря поддержке интерфейса API REST и формата данных JSON. Мы могли вызвать платформу непосредственно при помощи подключаемых модулей с открытым исходным кодом, таки как JQuery JSON.Net, а также использовать средства типа Fiddler для оперативного проведения экспериментов и отладки.

> [AZURE.NOTE]Эта служба не только подготавливала данные, но помогла нам, разработчикам прототипа, понять принципы работы технологии поиска, что позволило повысить производительность и эффективно использовать встроенные компоненты. Если в поиск следует включить конструктор поисковых запросов, возможности фасетной навигации, фильтры и другие элементы, создание прототипа может занять больше времени.

###Управление аспектами на странице представления данных поиска

Во время эксперимента мы поняли, что следует заранее тщательно разрабатывать аспекты. После загрузки большого количества данных в решение мы видели, что объем аспектов слишком велик для представления пользователям.

Мы решили эту проблему, задав ограничение в параметре count аспекта. Параметр count налагает жесткое ограничение на количество аспектов, возвращаемых пользователю. По [этой ссылке](search-faceted-navigation.md) вы можете ознакомиться с обсуждением параметра count.

###Веб-задания для планирования задач

Нас приятно удивили не только возможности службы "Поиск Azure". Мы обнаружили, что веб-задания позволяют значительно эффективнее автоматизировать операции по загрузке данных в службу "Поиск Azure" по сравнению с методом, который мы использовали ранее. Он заключался в том, что мы пользовались выделенной виртуальной машиной с планировщиком Windows, который выполнял запланированные задачи по обновлению индекса поиска. Веб-задания было проще настраивать и легче отлаживать. Кроме того, использовать их было значительно дешевле, чем платить за выделенную виртуальную машину.

###Средство обновления изображений Azure BLOB Storage Explorer

Мы обнаружили, что средство [Azure BLOB Storage Explorer](https://azurestorageexplorer.codeplex.com/) (доступное на сайте CodePlex) позволяет эффективно обновлять изображения и видео на сайте. Мы используем его в качестве средства разработчика для ручного обновления изображений и видеозаписей, которые составляют часть нашего основного сайта. Это средство отличается большей гибкостью по сравнению с развертыванием изменений на портале, устраняя необходимость тестовой итерации при каждом обновлении изображения.

##Краткое заключение

Спасибо прекрасным сотрудникам WhatToPedia, которые разрешили нам поделиться их историей!

Надеемся, этот пример окажется для вас полезным. Если вы решите воспользоваться службой "Поиск Azure", рекомендую ознакомиться со следующими ресурсами:

- [форум MSDN, посвященный службе "Поиск Azure";](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow (выполните поиск по соответствующему тегу);](http://stackoverflow.com/questions/tagged/azure-search)
- [страница документации на сайте Azure.com;](http://azure.microsoft.com/documentation/services/search/)
- [документация по Поиску Azure на сайте MSDN.](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##Приложение. Веб-задание индексатора поиска

С помощью следующего кода создается индексатор, упомянутый в разделе о построении прототипа.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=August15_HO6-->
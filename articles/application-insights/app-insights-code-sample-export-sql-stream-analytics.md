<properties 
	pageTitle="Пошаговое руководство. Экспорт телеметрии в базу данных SQL из Application Insights" 
	description="Напишите код анализа телеметрии в Application Insights с помощью функции непрерывного экспорта." 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="awills"/>
 
# Пошаговое руководство. Экспорт в SQL из Application Insights с использованием Stream Analytics

В этой статье показано, как переместить данные телеметрии из [Visual Studio Application Insights][start] в базу данных SQL Azure, используя [непрерывный экспорт][export] и [Azure Stream Analytics](http://azure.microsoft.com/services/stream-analytics/).

Непрерывный экспорт позволяет переместить данные телеметрии в службу хранилища Azure в формате JSON. Мы выполним анализ объектов JSON, используя Azure Stream Analytics, и создадим строки в таблице базы данных.

(В общих чертах непрерывный экспорт представляет собой способ для самостоятельного анализа телеметрии, которую приложения отправляют в Application Insights. Этот пример кода можно адаптировать для выполнения других задач с экспортированной телеметрией, таких как агрегирование данных и их публикация в наборе визуализаций PowerBI.)

Сначала предположим, что у вас уже есть приложение, мониторинг которого нужно выполнить.


В этом примере мы используем данные просмотров страницы, но этот же шаблон можно легко использовать и для других типов данных, таких как пользовательские события и исключения.


## Добавление пакета SDK для Application Insights

Чтобы выполнить мониторинг приложения, [добавьте пакет SDK для Application Insights][start] в приложение. Для различных платформ, IDE и языков разработано множество различных пакетов SDK и вспомогательных инструментов. Вы можете выполнять мониторинг веб-страниц, веб-серверов Java или ASP.NET и мобильных устройств нескольких типов. Все пакеты SDK отправляют данные телеметрии на [портал Application Insights][portal], где вы можете использовать эффективные инструменты анализа и диагностики, а также экспортировать данные в хранилище.

Чтобы начать работу:

1. Получите [учетную запись в Microsoft Azure](http://azure.microsoft.com/pricing/).
2. На [портале Azure][portal] добавьте новый ресурс Application Insights для своего приложения:

    ![Выберите "Создать", "Службы для разработчиков", Application Insights и тип приложения.](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (Тип вашего приложения и подписка могут отличаться.)
3. Откройте "Быстрый запуск", чтобы узнать, как установить пакет SDK для приложения необходимого типа.

    ![Выберите "Быстрый запуск" и следуйте указаниям.](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    Если в списке отсутствует необходимый тип приложения, просмотрите страницу [Начало работы][start].

4. В этом примере мы выполним мониторинг веб-приложения, поэтому будем использовать инструменты Azure в Visual Studio для установки пакета SDK. Зададим имя ресурса Application Insights:

    ![В обозревателе решений щелкните проект правой кнопкой мыши и выберите пункт "Добавить Application Insights". На вкладке "Отправить телеметрию на" выберите "Создать новый ресурс" или "Использовать существующий".](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. Опубликуйте свое приложение и просмотрите данные телеметрии, появившиеся в ресурсе Application Insights.


## Создание хранилища в Azure

В результате непрерывного экспорта происходит передача данных в учетную запись хранилища Azure, поэтому необходимо сначала создать хранилище.

1. Создайте «классическую» учетную запись хранения в подписке на [портале Azure][portal].

    ![На портале Azure выберите «Создать», «Данные», «Хранилище». Выберите «Классический» и щелкните «Создать». Введите имя хранилища.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Создание контейнера

    ![В новом хранилище выберите «Контейнеры», щелкните элемент «Контейнеры», а затем — «Добавить».](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Скопируйте ключ доступа к хранилищу.

    Он вскоре потребуется для настройки входных данных для службы Stream Analytics.

    ![В хранилище откройте «Параметры», «Ключи» и сделайте копию первичного ключа доступа.](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Запуск непрерывного экспорта в службе хранилища Azure

1. На портале Azure перейдите к ресурсу Application Insights, созданному для приложения.

    ![Выберите «Обзор», «Application Insights», а затем выберите свое приложение.](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Создайте непрерывный экспорт.

    ![Выберите «Параметры», «Непрерывный экспорт», «Добавить».](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Выберите учетную запись хранения, созданную ранее:

    ![Установите место назначения экспорта.](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Задайте необходимые типы событий:

    ![Выберите типы событий.](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


3. Пусть данные накопятся. Предоставьте пользователям возможность поработать с приложением на протяжении некоторого времени. После получения данных телеметрии в [обозревателе метрик](app-insights-metrics-explorer.md) отобразятся статистические диаграммы, а в разделе [Поиск по журналу диагностики](app-insights-diagnostic-search.md) – отдельные события.

    Данные также будут экспортированы в хранилище.

4. Проверьте экспортированные данные на портале (нажмите **Обзор**, выберите учетную запись хранения и щелкните **Контейнеры**) или в Visual Studio. В Visual Studio откройте меню **"Вид" или "Обозреватель облаков"** и выберите элемент "Azure" или "Служба хранилища". (Если этой команды нет в меню, установите пакет SDK Azure: откройте диалоговое окно "Создание проекта", разверните узел "Visual C#/облако" и выберите "Получить Microsoft Azure SDK для .NET".)

    ![В Visual Studio откройте "Обозреватель сервера", "Azure", "Хранилище".](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

    Запишите общую часть имени пути, которое образовано от имени приложения и ключа инструментирования.

События записываются в JSON-файлы больших двоичных объектов. Каждый файл может содержать одно или несколько событий. Поэтому нам нужна возможность считывать данные событий и отфильтровывать необходимые поля. С данными можно выполнять любые действия, но сейчас мы будем использовать Stream Analytics для перемещения данных в базу данных SQL. Это облегчит выполнение многих любопытных запросов.

## Создание базы данных SQL Azure

И снова начнем с использованием вашей подписки на [портале Azure][portal]. Создайте базу данных (и новый сервер, если его еще нет), куда будут записываться данные.

!["Создать", "Данные", SQL.](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Убедитесь, что на сервере базы данных разрешен доступ к службам Azure:


!["Обзор", "Серверы", ваш сервер, "Параметры", "Брандмауэр", "Разрешить доступ к Azure".](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Создание таблицы в базе данных SQL Azure

Подключитесь к базе данных, созданной в предыдущем разделе, с помощью желаемого инструмента управления. В этом пошаговом руководстве мы используем [инструменты управления SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Создайте новый запрос и выполните следующий сценарий T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

В этом примере мы используем данные из представлений страниц. Для просмотра других доступных данных проверьте выходные данные JSON и ознакомьтесь с разделом [Экспорт модели данных](app-insights-export-data-model.md).

## Создание экземпляра Azure Stream Analytics

В [классическом портале Azure](https://manage.windowsazure.com/) выберите службу Azure Stream Analytics и создайте новое задание Stream Analytics:


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

При создании нового задания разверните информацию о нем:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Установка расположения большого двоичного объекта

Задайте расположение для приема входных данных из большого двоичного объекта непрерывного экспорта:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Теперь потребуется первичный ключ доступа из вашей учетной записи хранения, указанной ранее. Задайте его в качестве ключа учетной записи хранения.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Установка шаблона префикса пути 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Задайте в поле "Формат даты" значение в формате **ГГГГ-ММ-ДД** (с **дефисами**).

Шаблон префикса пути указывает, как Stream Analytics находит входные файлы в хранилище. Вам необходимо настроить это поле в соответствии с тем, как функция непрерывного экспорта сохраняет данные. Задайте следующее значение:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

В данном примере:

* `webapplication27` — имя ресурса Application Insights (**только строчные буквы**). 
* `1234...` — ключ инструментирования ресурса Application Insights с **удаленными дефисами**. 
* `PageViews` – тип данных, которые необходимо проанализировать. Доступные типы зависят от фильтра, установленного в функции непрерывного экспорта. Изучите экспортированные данные, чтобы увидеть другие доступные типы, а также ознакомьтесь с [моделью экспорта данных](app-insights-export-data-model.md).
* `/{date}/{time}` – шаблон, записанный буквально.

Чтобы получить имя и ключ инструментирования Application Insights, откройте раздел "Основные компоненты" на странице обзора или вкладку "Параметры".

#### Завершение начальной настройки

Подтвердите формат сериализации:

![Подтвердите выбор и закройте мастер.](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Закройте мастер и дождитесь завершения установки.

>[AZURE.TIP]Используйте функцию Sample для проверки правильности входного пути. В случае сбоя убедитесь, что в хранилище для выбранного диапазона времени есть данные. Измените определение ввода и убедитесь, что учетная запись хранения, префикс пути и формат даты указаны правильно.

## Настройка запроса

Откройте раздел запроса:

![В Stream Analytics выберите "Запрос".](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Замените запрос по умолчанию следующим:

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Обратите внимание, что первые несколько свойств соответствуют данным просмотров страницы. При экспорте телеметрии других типов будут получены другие свойства. См. [подробный справочник по модели данных для типов и значений свойств.](app-insights-export-data-model.md)

## Настройка выходных данных в базе данных

Выберите SQL в качестве типа выходных данных.

![В Stream Analytics выберите "Выходные данные".](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Укажите базу данных SQL.


![Введите информацию о базе данных.](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Закройте окно мастера и дождитесь появления уведомления о том, что выходные данные были настроены.

## Начало обработки

Запустите задание на панели действий:

![В Stream Analytics щелкните "Запустить".](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

Вы можете выбрать тип обработки данных – обработка текущих данных или данных, полученных ранее. Последний вариант удобен, если непрерывный экспорт уже выполняется в течение некоторого времени.


![В Stream Analytics щелкните "Запустить".](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Через несколько минут вернитесь к инструментам управления SQL Server и просмотрите передачу данных. Например, используйте запрос наподобие этого:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## Связанные статьи

* [Экспорт в SQL с использованием рабочей роли](app-insights-code-sample-export-telemetry-sql-database.md)
* [Подробный справочник по модели данных типов и значений свойств.](app-insights-export-data-model.md)
* [Непрерывный экспорт в Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=Oct15_HO2-->
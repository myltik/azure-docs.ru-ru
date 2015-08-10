<properties 
	pageTitle="Просмотр данных Application Insights в Power BI" 
	description="Использование Power BI для мониторинга производительности и использования приложения." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="awills"/>
 
# Представления данных Application Insights в Power BI

[Microsoft Power BI](https://powerbi.microsoft.com/) представляет данные в виде разнообразных информативных визуальных элементов, а также позволяет объединить информацию из нескольких источников. Вы можете выполнять потоковую передачу данных телеметрии о производительности и использовании веб-приложений или приложений для устройства из Application Insights в Power BI.

![Пример представления Power BI с данными об использовании, полученными из Application Insights](./media/app-insights-export-power-bi/010.png)

В этой статье будет показано, как экспортировать данные из Application Insights и использовать Stream Analytics для перемещения данных в Power BI. [Stream Analytics](http://azure.microsoft.com/services/stream-analytics/) — это служба Azure, которая будет использоваться как адаптер.

![Пример представления Power BI с данными об использовании, полученными из Application Insights](./media/app-insights-export-power-bi/020.png)

## Видео

Ноам Бен Зив (Noam Ben Zeev) показывает то, о чем идет речь в этой статье.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Мониторинг приложения с помощью Application Insights

Если вы еще не пробовали это, начните сейчас. Application Insights может осуществлять мониторинг любого устройства или веб-приложения на разных платформах, включая Windows, iOS, Android, J2EE и др. [Начало работы](app-insights-get-started.md).

## Создание хранилища в Azure

В результате непрерывного экспорта происходит передача данных в учетную запись хранилища Azure, поэтому необходимо сначала создать хранилище.

1. Создайте учетную запись хранения в подписке на [портале Azure](https://portal.azure.com).

    ![На портале Azure выберите «Создать», «Данные», «Хранилище».](./media/app-insights-export-power-bi/030.png)

2. Создание контейнера

    ![В новом хранилище выберите «Контейнеры», а затем «Добавить».](./media/app-insights-export-power-bi/040.png)

3. Скопируйте ключ доступа к хранилищу.

    Он вскоре потребуется для настройки входных данных для службы Stream Analytics.

    ![В хранилище откройте «Параметры», «Ключи» и сделайте копию первичного ключа доступа.](./media/app-insights-export-power-bi/045.png)

## Запуск непрерывного экспорта в службе хранилища Azure

[Непрерывный экспорт](app-insights-export-telemetry.md) перемещает данные из Application Insights в хранилище Azure.

1. На портале Azure перейдите к ресурсу Application Insights, созданному для приложения.

    ![Выберите «Обзор», «Application Insights», а затем выберите свое приложение.](./media/app-insights-export-power-bi/050.png)

2. Создайте непрерывный экспорт.

    ![Выберите «Параметры», «Непрерывный экспорт», «Добавить».](./media/app-insights-export-power-bi/060.png)


    Выберите учетную запись хранения, созданную ранее:

    ![Установите место назначения экспорта.](./media/app-insights-export-power-bi/070.png)
    
    Задайте необходимые типы событий:

    ![Выберите типы событий.](./media/app-insights-export-power-bi/080.png)

После завершения предоставьте пользователям возможность попользоваться приложением на протяжении некоторого времени. После получения данных телеметрии в [обозревателе метрик](app-insights-metrics-explorer.md) отобразятся статистические диаграммы, а в разделе [Поиск по журналу диагностики](app-insights-diagnostic-search.md) – отдельные события.

Данные также будут экспортированы в хранилище.

## Создание экземпляра Azure Stream Analytics

В [классическом портале Azure](https://manage.windowsazure.com/) выберите службу Azure Stream Analytics и создайте новое задание Stream Analytics:


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

При создании нового задания разверните информацию о нем:

![](./media/app-insights-export-power-bi/110.png)


#### Установка расположения большого двоичного объекта

Задайте расположение для приема входных данных из большого двоичного объекта непрерывного экспорта:

![](./media/app-insights-export-power-bi/120.png)

Теперь потребуется первичный ключ доступа из вашей учетной записи хранения, указанной ранее. Задайте его в качестве ключа учетной записи хранения.

![](./media/app-insights-export-power-bi/130.png)

#### Установка шаблона префикса пути 

![](./media/app-insights-export-power-bi/140.png)

Задайте в поле «Формат даты» значение в формате ГГГГ-ММ-ДД (с дефисами).

Шаблон префикса пути указывает, как Stream Analytics находит входные файлы в хранилище. Необходимо настроить это поле в соответствии с тем, как функция непрерывного экспорта сохраняет данные. Задайте следующее значение:

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

В данном примере:

* `webapplication27` – имя ресурса Application Insights. 
* `1000...` – ключ инструментирования ресурса Application Insights. 
* `PageViews` – тип данных, которые необходимо проанализировать. Доступные типы зависят от фильтра, установленного в функции непрерывного экспорта. Изучите экспортированные данные, чтобы увидеть другие доступные типы.
* `/{date}/{time}` – шаблон, записанный буквально.

Чтобы получить имя и ключ инструментирования Application Insights, откройте раздел «Основные компоненты» на странице обзора или вкладку «Параметры».

#### Завершение начальной настройки

Подтвердите формат сериализации:

![Подтвердите выбор и закройте мастер.](./media/app-insights-export-power-bi/150.png)

Закройте мастер и дождитесь завершения установки.

## Определение выходных данных

Теперь выберите задание и задайте выходные данные.

![Выбор нового канала и элементов «Выходные данные», «Добавить», «Power BI».](./media/app-insights-export-power-bi/160.png)

Предоставьте для Stream Analytics доступ к ресурсу Power BI, после чего создайте имя для выходных данных, а также для целевого набора данных и таблицы Power BI.

![Создание трех имен.](./media/app-insights-export-power-bi/170.png)

## Определение запроса

Запрос определяет преобразование входных данных в выходные.

![Выбор задания и элемента «Запрос». Вставка следующего примера.](./media/app-insights-export-power-bi/180.png)

Вставьте следующий запрос.

```SQL

    SELECT
      flat.ArrayValue.name
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input — это псевдоним, присвоенный входным данным потока.
* pbi-output — это определенный псевдоним выходных данных.
* Мы используем GetElements, так как имя события находится во вложенном массиве JSON. Затем элемент Select выбирает имя события, а также количество экземпляров с этим именем за определенный период времени. Предложение Group By группирует элементы по периодам времени продолжительностью 1 минута.

## Выполнение задания

Дату запуска задания можно выбрать в прошлом.

![Выбор задания и элемента «Запрос». Вставка следующего примера.](./media/app-insights-export-power-bi/190.png)

Подождите, пока задание находится в статусе выполнения.

## Просмотр результатов в Power BI

Откройте Power BI, а затем выберите набор данных и таблицу, определенные как выходные данные задания Stream Analytics.

![Выбор набора данных и полей в Power BI.](./media/app-insights-export-power-bi/200.png)

Теперь этот набор данных можно использовать в отчетах и панелях мониторинга в [Power BI](https://powerbi.microsoft.com).


![Выбор набора данных и полей в Power BI.](./media/app-insights-export-power-bi/210.png)

## Видео

Ноам Бен Зив (Noam Ben Zeev) показывает, как выполнить экспорт в Power BI.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Связанные материалы

* [непрерывный экспорт.](app-insights-export-telemetry.md)
* [Application Insights](app-insights-overview.md)

<!---HONumber=July15_HO5-->
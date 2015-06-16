<properties 
	pageTitle="Мониторинг доступности и скорости реагирования любого веб-сайта" 
	description="Настройка веб-тестов в Application Insights. Получение оповещений, когда веб-сайт становится недоступным или медленно реагирует на запросы." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-27" 
	ms.author="awills"/>
 
# Наблюдение за доступностью и скоростью реагирования веб-сайта

После развертывания своего веб-приложения вы можете настроить веб-тесты для наблюдения за его доступностью и скоростью реагирования. Application Insights отправляет веб-запросы через одинаковые промежутки времени из разных точек по всему миру, и если приложение реагирует медленно или не реагирует вообще, будет отправлять вам оповещения.

![Web test example](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Веб-тесты можно настроить для любой конечной точки HTTP, достижимой из общедоступного Интернета.

*Это веб-сайт Azure? Просто [создайте веб-тест в модуле веб-сайта ][azurewebtest].*


1. [Создать новый ресурс?](#create)
1. [Настройка веб-теста](#setup)
1. [Просмотр результатов](#monitor)
2. [При возникновении сбоев...](#failures)
2. [Многошаговые веб-тесты](#multistep)
1. [Изменение или отключение теста](#edit)


 [Видео](#video)
 [Дальнейшие действия](#next)

## Настройка веб-теста

### <a name="create"></a>1. Создать новый ресурс?

Пропустите этот шаг, если вы уже [установили запуск ресурса][Application Insights] для этого приложения и хотите, чтобы данные о доступности отображались в этом же месте.

Зарегистрируйтесь в [Microsoft Azure](http://azure.com), перейдите на [портал предварительной версии](https://portal.azure.com) и создайте новый ресурс Application Insights. 

![New > Application Insights](./media/app-insights-monitor-web-app-availability/appinsights-11newApp.png)

### <a name="setup"></a>2. Создание веб-теста

В модуле обзора вашего приложения щелкните плитку веб-тестов. 

![Click the empty availability test](./media/app-insights-monitor-web-app-availability/appinsights-12avail.png)

*Веб-тесты уже получены? Щелкните плитку веб-тестов и выберите "Добавить веб-тест".*

Задайте сведения о тесте.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/appinsights-13availChoices.png)

- **URL-адрес** должен быть видимым из общедоступного Интернета. Он может содержать строку запроса, поэтому вы, например, сможете немного поупражняться в работе с базой данных. Если URL-адрес указывает на перенаправление, мы будем переходить по нему до 10 раз.

- **Тестовые расположения** - это места, из которых наши серверы отправляют веб-запросы на ваш URL-адрес. Выберите два или три, чтобы можно было различать проблемы веб-сайта и сетевые проблемы. Более трех расположений выбрать нельзя.

- **Критерии успешного завершения**.
    **Коды возврата HTTP:**: обычно 200. 

    **Строка соответствия** контента, например "Добро пожаловать!". Проверим, что она содержится в каждом ответе. Это должна быть строка обычного текста без подстановочных знаков. Не забывайте, что если контент страницы изменяется, необходимо обновить эту строку.

- **Оповещения**. По умолчанию оповещения отправляются при наличии повторяющихся сбоев в течение 15 минут. Но для настройки чувствительности это значение можно изменить; можно также изменять адреса электронной почты, на которые отправляются оповещения.

#### Тестирование дополнительных URL-адресов

Вы можете добавить тесты для любого необходимого количества URL-адресов. Например, при тестировании домашней страницы можно также проверить, запущена ли база данных, путем тестирования URL-адреса поиска.

![On the web tests blade, choose Add](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)


### <a name="monitor"></a>3. Просмотр отчетов о доступности

После 1-2 минут нажмите кнопку "Обновить" в модуле обзора. (В данной версии он не обновляется автоматически.)

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/appinsights-14availSummary.png)

Диаграмма в модуле обзора содержит результаты всех веб-тестов данного приложения.

#### Компоненты страницы

Процесс тестирования включает запрос изображений, таблиц стилей и скриптов, а также других статических компонентов.  

Записанное время ответа - это время, затраченное на загрузку всех компонентов.

Если не удается загрузить какой-либо компонент, тест будет помечен, как сбойный.

## <a name="failures"></a>При возникновении сбоев...

Для просмотра отдельных результатов по каждому тесту щелкните модуль веб-тестов.

Откройте конкретный веб-тест.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-15webTestList.png)

Прокрутите вниз до пункта **Неудачные тесты** и выберите результат.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-17-availViewDetails.png)

Результат показывает причину неудачи.

![Webtest run result](./media/app-insights-monitor-web-app-availability/appinsights-18-availDetails.png)

Чтобы получить дополнительные сведения, загрузите файл результатов и проинспектируйте его в Visual Studio.



##<a name="multistep"></a>Многошаговые веб-тесты

Вы можете контролировать сценарий, который содержит последовательность URL-адресов. Например, в случае наблюдения за интернет-магазином вы можете проверить, что добавление товаров в корзину работает исправно. 

Для создания многошагового теста необходимо записать сценарий с помощью Visual Studio и затем отправить запись в Application Insights. Application Insights будет периодически воспроизводить сценарий и проверять ответы.

#### 1. Запись сценария

Для записи веб-сеанса используйте Visual Studio Ultimate.

1. Создайте проект веб-теста производительности.
    ![In Visual Studio, create a new project from the Web Performance and Load Test template.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. Откройте файл webtest и начните запись.
    ![Open the .webtest file and click Record.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Выполните действия пользователя, которые необходимо имитировать для теста: откройте веб-сайт, добавьте товар в корзину и т. д. Затем остановите тест. 
    ![The web test recorder runs in Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)
    Не делайте слишком длинный сценарий. Ограничение - 100 шагов или 2 минуты.
4. Запустите тест в Visual Studio и убедитесь, что он работает.
    Средство выполнения веб-тестов откроет веб-браузер и повторит записанные действия. Убедитесь, что тест работает правильно. 
    ![In Visual Studio, open the .webtest file and click Run.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)
 

(Не вставляйте циклы в код веб-теста.)

#### 2. Загрузка веб-теста в Application Insights

Создайте новый веб-тест на портале Application Insights.

![On the web tests blade, choose Add.](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)

Выберите многошаговый тест и загрузите файл .webtest.

![Select multi-step webtest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

Просмотр результатов теста и всех ошибок выполняется так же, как и для тестов с одним URL. 

Распространенной причиной ошибок является слишком большое время выполнения теста. Тест должен выполняться не более двух минут.


### Вставка времени и случайных чисел в многошаговый тест

Предположим, что вы тестируете инструмент, который получает зависящие от времени данные (например запасы) от внешнего источника. При записи веб-теста необходимо использовать определенные значения времени, но они должны быть заданы как параметры теста StartTime и EndTime.

![A web test with parameters.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

При запуске теста параметру EndTime следует всегда присваивать текущее время, а параметру StartTime - время за 15 минут до текущего.

Подключаемые модули веб-теста позволяют сделать это.

1. Добавьте подключаемые модули веб-теста для всех необходимых значений переменных параметров. На панели инструментов теста выберите **Добавить подключаемый модуль веб-теста**.

    ![Choose Add Web Test Plugin and select a type.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    В этом примере используется два экземпляра подключаемого модуля даты и времени. Один экземпляр соответствует значению времени "15 минут назад", а другой - "сейчас". 

2. Откройте свойства для каждого подключаемого модуля. Присвойте ему имя и задайте использование текущего времени. Для одного из экземпляров задайте для параметра "Добавление минут" значение "-15".

    ![Set name, Use Current Time, and Add Minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. В веб-параметрах теста используйте {{имя подключаемого модуля}} для ссылки на имя подключаемого модуля.

    ![In the test parameter, use {{plug-in name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Теперь можно загрузить тест на портал. Он будет использовать динамические значения при каждом тестировании.

## <a name="edit"></a> Изменение или отключение теста

Откройте отдельный тест, чтобы изменить или отключить его.

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/appinsights-19-availEdit.png)

Отключение веб-тестов может потребоваться при выполнении обслуживания в службе.

## <a name="video"></a>Видео

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Дальнейшие действия

[Поиск по журналам диагностики][диагностика]

[Устранение неполадок][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




[azurewebtest]: ../insights-create-web-tests/

<!--HONumber=46--> 
 
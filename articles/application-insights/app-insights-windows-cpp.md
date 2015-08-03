<properties pageTitle="Application Insights для приложений C++" description="Анализ данных использования и производительности приложения C++ с помощью Application Insights." services="application-insights" documentationCenter="cpp" authors="crystk" manager="jakubo""/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# Application Insights для приложений C++

Visual Studio Application Insights позволяет отслеживать мобильные приложения для оценки частоты использования, характера событий и сбоев.

## Требования

Вам потребуется следующее:

* подписка на [Microsoft Azure](http://azure.com). Учетная запись Майкрософт для входа, которая уже может быть у вас для Windows, XBox Live или других облачных служб Майкрософт.
* Visual Studio 2015 или более поздняя версия.

## Создание ресурса Application Insights

На [портале Azure][portal] создайте новый ресурс Application Insights. Выберите Windows Phone или Магазин Windows.

![Последовательно выберите «Создать», «Службы для разработчиков», «Application Insights»](./media/app-insights-windows-cpp/01-universal.png)

В отобразившейся колонке будут содержатся данные о производительности и использовании приложения. Чтобы вернуться к ней после следующего входа в Azure, найдите соответствующую плитку на начальном экране. Или щелкните «Обзор», чтобы найти ее.

####  Сделайте копию ключа инструментирования.

Ключ идентифицирует ресурс, и вы установите его в пакет SDK для направления данных ресурсу.

![Нажмите «Свойства», выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a>Установка пакета SDK в приложении


1. В Visual Studio отредактируйте пакеты NuGet вашего проекта классического приложения.

    ![Щелкните проект правой кнопкой мыши и выберите пункт «Управление пакетами Nuget»](./media/app-insights-windows-cpp/03-nuget.png)

2. Установите пакет SDK для Application Insights для приложений C++.

    ![Укажите **Включить предварительный выпуск** и найдите Application Insights.](./media/app-insights-windows-cpp/04-nuget.png)

3. В параметрах проектов для выпуска и отладки:
  - добавьте $(SolutionDir)packages\ApplicationInsights-CPP.1.0.0-Beta\src\inc в свойства проекта, выбрав элементы «Каталоги VC++» -> «Каталоги включения»;
  - добавьте $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP в свойства проекта, выбрав элементы «Каталоги VC++» -> «Каталоги библиотек».

4. Добавьте файл ApplicationInsights.winmd в качестве ссылки на проект из $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\ApplicationInsights.
5. Добавьте файл AppInsights_Win10-UAP.dll из $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP. Перейдите к свойствам и установите значение «ДА» для содержимого. Таким образом, DLL-файл будет скопирован в каталог сборки.


#### Обновление до будущих версий пакета SDK

После выпуска нового [пакета SDK](app-insights-release-notes-windows-cpp.md):

* В диспетчере пакетов NuGet найдите установленный пакет SDK и выберите «Обновить».
* Повторите шаги установки, используя номер новой версии.

## Использование пакета SDK

Инициализируйте пакет SDK и начните отслеживать телеметрию.

1. В App.xaml.h: 
  - добавьте `ApplicationInsights::CX::SessionTracking^ m_session;`;
2. В App.xaml.cpp:
  - добавьте `using namespace ApplicationInsights::CX;`;

  - В App:App()
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - После создания корневого кадра (обычно в конце App::OnLaunched) инициализируйте m_session:
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. Чтобы использовать отслеживание в другом месте в приложении, можно объявить экземпляр клиента телеметрии.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> Запуск проекта

Для формирования телеметрии запустите приложение. Приложение можно запустить в режиме отладки на компьютере, на котором ведется разработка, или опубликовать и позволить пользователям запустить его.

## Просмотр данных в Application Insights

Вернитесь к http://portal.azure.com и перейдите к ресурсу Application Insights.

Нажмите кнопку «Поиск», чтобы открыть [Поиск по журналу диагностики][diagnostic], — здесь события появляются в первую очередь. Если ничего не отображается, подождите одну-две минуты и щелкните «Обновить».

![Щелкните «Поиск по журналу диагностики»](./media/app-insights-windows-cpp/21-search.png)

По мере использования приложения данные будут отображаться в колонке обзора.

![Колонка «Обзор»](./media/app-insights-windows-cpp/22-oview.png)

Щелкните любую диаграмму, чтобы получить более подробную информацию. Например, в случае сбоя:

![Щелкните диаграмму сбоев](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>Дальнейшие действия

[Отслеживание использования приложения][track]

[Использование API для отправки пользовательских событий и метрик][api]

[Поиск по журналу диагностики][diagnostic]

[Обозреватель метрик][metrics]

[Устранение неполадок][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=July15_HO4-->
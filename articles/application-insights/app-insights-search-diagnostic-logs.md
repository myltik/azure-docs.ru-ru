<properties 
	pageTitle="Поиск по журналам диагностики" 
	description="Поиск журналов, созданных с помощью Trace, Log4Net или NLog." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2015" 
	ms.author="awills"/>
 
# Поиск по журналам диагностики в Application Insights

Одним из наиболее традиционных методов отладки является вставка строк программного кода, которые генерируют записи в журнале трассировки. [Application Insights][start] позволяет записывать журналы вашего веб-сервера, а также выполнять в них поиск и фильтрацию. Если вы уже используете log4Net, NLog или System.Diagnostics.Trace, вы можете записать эти журналы с помощью нашего адаптера. Кроме того, можно использовать методы TrackTrace и TrackException, встроенные в Application Insights SDK.

Результаты поиска могут также включать представления обычных страниц и запросы событий, которые используются для построения отчетов об [использовании][usage] и [производительности][perf], а также все [пользовательские вызовы TrackEvent][track], которые вы записали.


2. [Установка адаптера для вашей платформы ведения журнала](#capture)
+ [Вставка вызовов журнала диагностики](#pepper)
+ [Исключения](#exceptions)
+ [Просмотр данных журнала](#view)
+ [Поиск по данным журнала](#search)
+ [Устранение неполадок](#questions)
+ [Дальнейшие действия](#next)



## <a name="capture"></a> Установка адаптера для вашей платформы ведения журнала

Если [Application Insights еще не установлен в вашем проекте][start], установите его сейчас.

Если планируется использовать встроенные вызовы Application Insights SDK Track *(), адаптер не требуется - [перейдите к следующему разделу.](#pepper).

Для поиска по журналам, созданным с помощью log4Net, NLog или System.Diagnostics.Trace, необходимо установить соответствующий адаптер.

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект. 
2. В обозревателе решений щелкните правой кнопкой мыши ваш проект и выберите **Управление пакетами NuGet**.
3. Выберите "В сети" > "Все", затем выберите **Включить предварительный выпуск** и выполните поиск по запросу Microsoft.ApplicationInsights.

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)

4. Выберите соответствующий пакет из списка.
  + Microsoft.ApplicationInsights.TraceListener (для захвата вызовов System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Пакет NuGet устанавливает необходимые сборки, а также вносит изменения в файл web.config или app.config.

## <a name="pepper"></a>3. Вставка вызовов журнала диагностики

Вставьте вызовы журналов событий с помощью предпочитаемой платформы ведения журнала. 

Например, если используется Application Insights SDK, можно вставить:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

если же используется System.Diagnostics.Trace:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

если вы предпочитаете log4net или NLog:

    logger.Warn("Slow response - database01");

запустите приложение в режиме отладки или разверните его на своем веб-сервере.

### <a name="exceptions"></a>Исключения

Чтобы отправить исключения в журнал, необходимо сделать следующее.

JavaScript на стороне клиента

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C# на стороне сервера

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB на стороне сервера

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Свойства и значения параметров не являются обязательными, но они полезны для фильтрации и добавления дополнительной информации. Например, если у вас есть приложение, которое запускает несколько игр, вы можете просматривать все отчеты об исключениях для каждой конкретной игры. Вы можете добавить в каждый словарь столько элементов, сколько вам нужно.

## <a name="view"></a>4. Просмотр данных журнала


1. В Application Insights откройте поиск по журналу диагностики.

    ![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-30openDiagnostics.png)
   
2. Установите фильтр для типов событий, которые вы хотите просмотреть.

    ![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-331filterTrace.png)


Ниже приведены типы событий.

* **Отслеживание** - поиск по журналам диагностики, созданным на вашем веб-сервере. Сюда входят вызовы log4Net, NLog, System.Diagnostic.Trace и ApplicationInsights TrackTrace.
* **Запрос** - HTTP-запросы на поиск, полученные серверным компонентом вашего веб-приложения, включая запросы страниц, запросы данных, изображений и т. д. Отображаемые события являются телеметрией, отправляемой сервером Application Insights SDK, которая используется для создания отчета о количестве запросов.
* **Просмотр страниц** - поиск событий просмотра страниц. Эти события отправляются веб-клиентом и используются для создания отчетов о просмотре страниц. (Если здесь ничего не отображается, необходимо установить [наблюдение за веб-клиентами][usage].)
* **Настраиваемое событие** - если вы вставили вызовы в TrackEvent() и TrackMetric() для [наблюдения за использованием][track], то здесь вы сможете осуществлять их поиск.

Выберите любой журнал для просмотра содержащихся в нем данных. 

![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-32detail.png)

Для фильтрации данных внутри элементов можно использовать простые строки (без подстановочных знаков).

Доступные поля зависят от платформы ведения журнала и от параметров, использованных в вызове.


## <a name="search"></a>5. Поиск данных

Задайте диапазон времени и выполните поиск терминов. Чем меньше диапазон, тем быстрее выполняется поиск. 

![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-311search.png)

Обратите внимание, что поиск ведется по терминам, а не по подстрокам. Слова - это последовательности букв и цифр с определенными знаками пунктуации, например, "." и "_". Например: 

<table>
  <tr><th>термин</th><th>НЕ совпадает с</th><th>, но эти термины совпадают</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

Вот доступные поисковые выражения:

<table>
                    <tr>
                      <th>
                        <p>Пример запроса</p>
                      </th>
                      <th>
                        <p>Результат</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">slow</span>
                        </p>
                      </td>
                      <td>
                        <p>Поиск всех элементов в диапазоне дат, поля которых включают слово "slow"</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">database??</span>
                        </p>
                      </td>
                      <td>
                        <p>Совпадения - database01, databaseAB, ...</p>
                        <p>? нельзя использовать в начале поискового запроса.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">database*</span>
                        </p>
                      </td>
                      <td>
                        <p>Совпадения - database, database01, databaseNNNN</p>
                        <p>* нельзя использовать в начале поискового запроса</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">яблоко AND банан</span>
                        </p>
                      </td>
                      <td>
                        <p>Поиск событий, содержащих оба этих слова. Используйте AND, а не and.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">яблоко OR банан</span>
                        </p>
                        <p>
                          <span class="code">яблоко банан</span>
                        </p>
                      </td>
                      <td>
                        <p>Поиск событий, содержащих любое из этих слов. Используйте OR, а не or.</p>
                        <p>Краткая форма.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">яблоко NOT банан</span>
                        </p>
                        <p>
                          <span class="code">яблоко -банан</span>
                        </p>
                      </td>
                      <td>
                        <p>Находит события, содержащие только одно из этих слов, но не содержащие второе.</p>
                        <p>Краткая форма.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>ябл* AND банан NOT (виноград OR груша)</p>
                        <p>
                          <span class="code">ябл* AND банан -(виноград груша)</span>
                        </p>
                      </td>
                      <td>
                        <p>Логические операторы и скобки</p>
                        <p>Более краткая форма</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:slow</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>Совпадение с указанным полем. По умолчанию поиск ведется по всем полям. Чтобы посмотреть, какие поля доступны, выберите событие и просмотрите его сведения.</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Вопросы и ответы

### <a name="emptykey"></a>Появляется сообщение об ошибке "ключ инструментирования не может быть пустым"

Вероятно, установка пакета Nuget адаптера ведения журнала была выполнена без установки Application Insights.

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Появится диалоговое окно с предложением войти в Azure и создать новый ресурс Application Insights или повторно использовать уже существующий. Это должно исправить проблему.

### <a name="limits"></a>Какой объем данных сохраняется?

До 500 событий в секунду от каждого приложения. События сохраняются в течение семи дней.

### <a name="cani"></a>Можно ли...

- настроить оповещения для событий и исключений?
- экспортировать журналы для дальнейшего анализа?
- выполнять поиск по определенным свойствам?

Пока нет, но все эти функции находятся в разработке.

## <a name="add"></a>Дальнейшие действия

* [Настройка тестов доступности и скорости реагирования][availability]
* [Устранение неполадок][qna]





[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=46--> 
 
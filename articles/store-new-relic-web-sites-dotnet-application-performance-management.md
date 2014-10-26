<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Websites" authors="new relic" solutions="" manager="dwrede" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new relic"></tags>

# Инструмент управления производительностью приложений New Relic для веб-сайтов Azure

В этом руководстве показано, как добавить соответствующий мировым стандартам инструмент мониторинга производительности New Relic на веб-сайт Azure. Вы узнаете, как быстро и просто добавить его в приложение, а также найдете описание некоторых функций New Relic. Дополнительные сведения об использовании New Relic см. в разделе [Использование New Relic][Использование New Relic]

## Что такое New Relic?

New Relic — это инструмент для разработчиков, который осуществляет мониторинг рабочих приложений и предоставляет подробные данные об их производительности и надежности. Таким образом, он позволяет быстрее выявлять и диагностировать возникающие проблемы с производительностью, а также предоставляет в ваше распоряжение данные, необходимые для решения этих проблем.

Инструмент New Relic отслеживает время загрузки и пропускную способность веб-транзакций как через сервер, так и через браузеры пользователей. Он позволяет увидеть, как долго вы пользуетесь базой данных, анализирует медленные запросы и веб-запросы, обеспечивает мониторинг бесперебойной работы и отображает соответствующие предупреждения, отслеживает исключения в приложениях, а также поддерживает множество других функций.

## Специальные цены на New Relic в Магазине Azure

New Relic Standard для пользователей Azure предлагается бесплатно. New Relic Pro предлагается в виде нескольких различных пакетов в зависимости от используемого режима веб-сайта и размера экземпляра при использовании зарезервированного режима.

Информацию по ценам см. в разделе [Страница New Relic в Магазине Azure][Страница New Relic в Магазине Azure].

<div class="dev-callout"> 
<strong>Примечание.</strong>
<p>Версия, предлагаемая по указанной цене, поддерживает до 10&nbsp;вычислительных операций. Если количество вычислительных операций превышает 10, стоимость корпоративного лицензирования вы можете узнать непосредственно в компании New Relic (sales@newrelic.com).</p>
</div>

Клиенты Azure получают пробную двухнедельную подписку на New Relic Pro при развертывании агента New Relic.

## Подписка на New Relic с использованием Магазина Azure

New Relic тесно интегрируется с веб-ролями, рабочими ролями и веб-сайтами Azure.

Чтобы подписаться на New Relic непосредственно из Магазина Azure выполните четыре простых шага, как указано далее.

### Шаг 1. Регистрация через Магазин Azure

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  В нижней части портала управления щелкните **Создать**.
3.  Нажмите кнопку **Магазин**.
4.  В диалоговом окне **Выберите надстройку** выберите **New Relic** и щелкните **Далее**.
5.  В диалоговом окне **Настроить надстройку** выберите нужный план New Relic.
6.  Введите имя службы New Relic, которое будет отображаться в параметрах Azure,
    или используйте значение по умолчанию **NewRelic**. Это имя должно быть уникальным
    в списке элементов Магазина Azure, на которые вы подписаны..
7.  Укажите регион, например **Запад США**.
8.  Нажмите кнопку **Далее**.
9.  В диалоговом окне **Просмотреть приобретение** просмотрите информацию о плане и ценах,
    а также ознакомьтесь с условиями. Если вы согласны с данными условиями, щелкните **Купить**.
10. После нажатия кнопки **Купить** начнется процесс создания учетной записи New Relic. Состояние можно отслеживать на портале управления Azure.
11. Чтобы получить лицензионный ключ New Relic, выберите созданную надстройку и щелкните **Сведения о подключении**.
12. Скопируйте лицензионный ключ, который появится на экране. Его нужно будет ввести при установке пакета Nuget New Relic.

### Шаг 2. Установка пакета New Relic

Агент веб-сайтов New Relic распространяется в виде пакета NuGet, который можно добавить на веб-сайт с помощью Visual Studio или WebMatrix. Если вы ранее не пользовались WebMatrix или Visual Studio для веб-сайта Azure, см. раздел [Развертывание веб-приложения ASP.NET на веб-сайте Azure с помощью Visual Studio][Развертывание веб-приложения ASP.NET на веб-сайте Azure с помощью Visual Studio] или [Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix][Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix].

Выполните следующие действия для используемой среды разработки:

**Visual Studio**

1.  Откройте решение веб-сайта Visual Studio.

2.  Откройте консоль диспетчера пакетов, выбрав **Средства -\> Диспетчер пакетов библиотеки -\> Консоль диспетчера пакетов**. Установите свой проект как проект по умолчанию в верхней части окна консоли диспетчера пакетов.

    ![Консоль диспетчера пакетов][Консоль диспетчера пакетов]

3.  В командной строке диспетчера пакетов введите следующую команду для установки пакета:

        Install-Package NewRelic.Azure.WebSites

4.  При появлении соответствующего запроса введите лицензионный ключ, полученный в Магазине Azure.

    ![введите лицензионный ключ][введите лицензионный ключ]

<!--5. Optional: At the application name prompt, enter your app's name as it will    appear in New Relic's dashboard. Or, use your solution name as the default.      ![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1.  Откройте веб-сайт с помощью WebMatrix.

2.  На вкладке **Домашняя страница** ленты выберите **NuGet**.

    ![кнопка NuGet на вкладке "Главная"][кнопка NuGet на вкладке "Главная"]

3.  В коллекции NuGet Gallery установите **Официальный источник пакета NuGet** в качестве источника и выполните поиск NewRelic.Azure.WebSites.

    ![поиск NewRelic.Azure.WebSites в галерее NuGet][поиск NewRelic.Azure.WebSites в галерее NuGet]

4.  Выберите **New Relic для веб-сайтов Azure** и щелкните **Установить**.

5.  После установки пакета веб-сайт будет содержать папку **newrelic**. Разверните эту папку и откройте файл **newrelic.config**. В этом файле укажите вместо значения **REPLACE\_WITH\_LICENSE\_KEY** лицензионный ключ, полученный из Магазина Azure.

    ![развернутая папка newrelic с выбранным элементом newrelic.conf][развернутая папка newrelic с выбранным элементом newrelic.conf]

    После добавления данных о лицензионном ключе сохраните изменения в файле **newrelic.config**.

### Шаг 3. Настройка веб-сайта и публикация приложения

Пакет New Relic, добавленный в приложение на предыдущем шаге, настраивается в соответствии с **параметрами приложения**, добавляемыми на веб-сайт Azure. Выполните следующие действия, чтобы добавить эти параметры.

1.  Войдите на [портал управления Azure][портал управления Azure] и перейдите к своему веб-сайту.

2.  На веб-сайте выберите **Настройка**. В разделе **Аналитические средства для разработчиков** выберите **Надстройка** или **Настраиваемый**. Эти методы выводят одинаковые данные, однако требуют немного разные входные данные. Значение **Надстройка** перечисляет текущие лицензии New Relic и позволяет выбрать одну из них, а значение **Настраиваемый** требует указать лицензионный ключ вручную.

    При выборе значения **Надстройка** используйте поле **Выберите надстройку** для своей лицензии New Relic.

    ![Изображение полей надстройки][Изображение полей надстройки]

    При выборе значения **Настраиваемый** используйте New Relic в качестве **поставщика** и введите лицензию в поле **Ключ поставщика**.

    ![Изображение настраиваемых полей][Изображение настраиваемых полей]

3.  Указав номер лицензии в поле **Аналитические средства для разработчиков**, нажмите кнопку **Сохранить**. После выполнения операции сохранения в раздел **Параметры приложения** этой страницы будут добавлены следующие значения для поддержки New Relic:

    <table border="1">
    <thead>
    <tr>
    <td>
    Ключ

    </td>
    <td>
    Значение

    </td>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td>
    COR\\\_ENABLE\\\_PROFILING

    </td>
    <td>
    1

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER

    </td>
    <td>
    {71DA0A04-7777-4EC6-9643-7D28B46A8A41}

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER\\\_PATH

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic\\NewRelic.Profiler.dll

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_HOME

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_LICENSEKEY

    </td>
    <td>
    Ваш лицензионный ключ

    </td>
    </tr>
    </tbody>
    </table>

    <div class="dev-callout"> 
<strong>Примечание.</strong> 
<p>Для вступления новых <strong>параметров приложения</strong> в силу может потребоваться до 30 секунд. Чтобы сразу же принудительно применить параметры, перезапустите веб-сайт.</p> 
</div>

4.  Опубликуйте приложение с помощью Visual Studio или WebMatrix.

### Шаг 4. Проверка производительности приложения в New Relic

Для просмотра панели мониторинга New Relic:

1.  На портале Azure нажмите кнопку **Управление**.
2.  Выполните вход, используя адрес электронной почты и пароль для New Relic.
3.  В строке меню New Relic выберите **Приложения -\> (имя приложения)**.

    Автоматически отобразится панель мониторинга **Мониторинг \> Обзор**.

    ![Панель мониторинга New Relic][Панель мониторинга New Relic]

    После выбора приложения из списка в меню **Приложения** на панели мониторинга **Обзор** будут показаны сведения о текущем сервере приложений, а также о браузере.

### <span id="using-new-relic"></span></a>Использование New Relic

После выбора приложения из списка в меню "Приложения" на панели мониторинга "Обзор" будут показаны сведения о текущем сервере приложений, а также о браузере. Для переключения между двумя представлениями щелкните кнопку **Сервер приложений** или **Браузер**.

В дополнение к функциям [стандартного пользовательского интерфейса New Relic][стандартного пользовательского интерфейса New Relic] и [детализации панели мониторинга][детализации панели мониторинга] на панели мониторинга "Обзор приложений" также доступны дополнительные функции.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Если вы хотите...</strong></th>
<th align="left"><strong>Выполните следующие действия...</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Показать сведения о панели мониторинга для выбранного сервера приложений или браузера</td>
<td align="left">Нажмите кнопку <strong>Сервер приложений</strong> или <strong>Браузер</strong>.</td>
</tr>
<tr class="even">
<td align="left">Просмотр пороговых значений оценки <a href="https://newrelic.com/docs/site/apdex">Apdex</a> по приложению.</td>
<td align="left">Выберите значок оценки Apdex <strong>?<strong>.</strong></strong></td>
</tr>
<tr class="odd">
<td align="left">Просмотр сведений Apdex по всему миру</td>
<td align="left">В разделе «Обзор» представления <strong>Браузер</strong> выберите любую точку на карте Global Apdex.<br /><strong>Совет.</strong> Чтобы напрямую перейти к панели мониторинга <a href="https://newrelic.com/docs/site/geography">География</a> для выбранного приложения, щелкните заголовок <strong>Global Apdex</strong> или выберите любую точку на карте Global Apdex.</td>
</tr>
<tr class="even">
<td align="left">Просмотр панели мониторинга <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard">Веб-транзакции</a></td>
<td align="left">На панели мониторинга «Обзор приложений» выберите таблицу «Веб-транзакции». Либо щелкните название транзакции, чтобы просмотреть информацию об определенной веб-транзакции (включая <a href="https://newrelic.com/docs/site/key-transactions">Ключевые транзакции</a>).</td>
</tr>
<tr class="odd">
<td align="left">Просмотр панели мониторинга <a href="https://newrelic.com/docs/site/errors">Ошибки</a></td>
<td align="left">На панели мониторинга «Обзор приложений» щелкните заголовок диаграммы «Частота ошибок».<br /><strong>Совет.</strong> Панель мониторинга также можно открыть, выбрав <strong>Приложения -&gt; (ваше приложение) &gt; События &gt; Ошибки</strong>.</td>
</tr>
<tr class="even">
<td align="left">Просмотр сведений о сервере приложений</td>
<td align="left"><p>Выполните любое из следующих действий:</p>
<p></p>
<ul>
<li>Переключитесь между табличным представлением узлов и представлением отдельных показателей по каждому узлу.</li>
<li>Щелкните имя одного из серверов.</li>
<li>Выберите оценку Apdex для одного из серверов.</li>
<li>Выберите &quot;Использование процессора&quot; или &quot;Память&quot; одного из серверов.</li>
</ul>
</p>
</p></td>
</tr>
</tbody>
</table>

Ниже приведен пример панели мониторинга "Обзор приложений" при выборе представления "Браузер".

![Консоль диспетчера пакетов][1]

## Дальнейшие действия

Для получения дополнительных сведений используйте следующие ресурсы:

-   [Установка агента .NET для веб-сайта Azure][Установка агента .NET для веб-сайта Azure]: процедуры установки агента .NET для New Relic.
-   [Пользовательский интерфейс New Relic][Пользовательский интерфейс New Relic]:
    обзор пользовательского интерфейса New Relic, настройка прав пользователей и профилей с использованием стандартных функций и подробных данных панели мониторинга.
-   [Обзор приложений][Обзор приложений]: возможности и функции при использовании панели мониторинга «Обзор приложений» в New Relic.
-   [Apdex][Apdex]: обзор способов, используемых Apdex для измерения степени удовлетворенности пользователей приложением.
-   [Мониторинг реального пользователя][Мониторинг реального пользователя]: обзор способов, используемых RUM для подробного анализа времени,
    необходимого пользовательским браузерам для загрузки веб-страниц, а также получения данных о регионе пребывания пользователей и используемых браузерах.
-   [Поиск справки][Поиск справки]: ресурсы, доступные через интерактивный центр справки New Relic.

  [Использование New Relic]: #using-new-relic
  [Страница New Relic в Магазине Azure]: http://www.windowsazure.com/ru-ru/gallery/store/new-relic/new-relic/
  [портал управления Azure]: https://manage.windowsazure.com
  [Развертывание веб-приложения ASP.NET на веб-сайте Azure с помощью Visual Studio]: http://www.windowsazure.com/ru-ru/develop/net/tutorials/get-started/
  [Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix]: http://www.windowsazure.com/ru-ru/develop/net/tutorials/website-with-webmatrix/
  [Консоль диспетчера пакетов]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [введите лицензионный ключ]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
  [кнопка NuGet на вкладке "Главная"]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
  [поиск NewRelic.Azure.WebSites в галерее NuGet]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png
  [развернутая папка newrelic с выбранным элементом newrelic.conf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
  [Изображение полей надстройки]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
  [Изображение настраиваемых полей]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
  [Панель мониторинга New Relic]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png
  [стандартного пользовательского интерфейса New Relic]: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [детализации панели мониторинга]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [География]: https://newrelic.com/docs/site/geography
  [Веб-транзакции]: https://docs.newrelic.com/docs/applications-menu/transactions-dashboard
  [Ключевые транзакции]: https://newrelic.com/docs/site/key-transactions
  [Ошибки]: https://newrelic.com/docs/site/errors
  [1]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png
  [Установка агента .NET для веб-сайта Azure]: https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install
  [Пользовательский интерфейс New Relic]: https://newrelic.com/docs/site/the-new-relic-ui
  [Обзор приложений]: https://newrelic.com/docs/site/applications-overview
  [Мониторинг реального пользователя]: https://newrelic.com/docs/features/real-user-monitoring
  [Поиск справки]: https://newrelic.com/docs/site/finding-help

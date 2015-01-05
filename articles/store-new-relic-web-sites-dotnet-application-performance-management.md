<properties urlDisplayName="New Relic App Performance Management" pageTitle="Инструмент управления производительностью приложений New Relic для Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Websites" authors="new relic" solutions="" manager="carolz" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/25/2014" ms.author="stepsic" />



#Инструмент управления производительностью приложений New Relic для веб-сайтов Azure

В этом руководстве показано, как добавить соответствующий мировым стандартам инструмент мониторинга производительности New Relic на веб-сайт Azure. Вы узнаете, как быстро и просто добавить его в приложение, а также найдете описание некоторых функций New Relic. Дополнительную информацию об использовании New Relic см. в разделе [Использование New Relic](#using-new-relic).

Что такое New Relic?
--

New Relic - это инструмент для разработчиков, который осуществляет мониторинг рабочих приложений и предоставляет подробные данные об их производительности и надежности. Таким образом,он позволяет быстрее выявлять и диагностировать возникающие проблемы с производительностью, а также предоставляет в ваше распоряжение данные, необходимые для решения этих проблем.

Инструмент New Relic отслеживает время загрузки и пропускную способность веб-транзакций как через сервер, так и через браузеры пользователей. Он позволяет увидеть, как долго вы пользуетесь базой данных, анализирует медленные запросы и веб-запросы, обеспечивает мониторинг бесперебойной работы и отображает соответствующие предупреждения, отслеживает исключения в приложениях, а также поддерживает множество других функций.

Специальные цены на New Relic в Магазине Azure
--

New Relic Standard для пользователей Azure предлагается бесплатно.
New Relic Pro предоставляется в виде нескольких разных пакетов в зависимости от используемого режима веб-сайта и размера экземпляра при использовании режима с резервированием.

Информацию по ценам см. в разделе [Страница New Relic в Магазине Azure](http://www.windowsazure.com/ru-ru/gallery/store/new-relic/new-relic/).

<div class="dev-callout"> 
<strong>Примечание.</strong>
<p>Версия, предлагаемая по указанной цене, поддерживает до 10 вычислительных операций. Если количество вычислительных операций превышает 10, стоимость корпоративного лицензирования вы можете узнать непосредственно в компании New Relic (sales@newrelic.com).</p>
</div>

Клиенты Azure получают пробную двухнедельную подписку на New Relic Pro при развертывании агента New Relic.

Подписка на New Relic с использованием Магазина Azure
--

New Relic тесно интегрируется с веб-ролями, рабочими ролями и веб-сайтами Azure.

Чтобы подписаться на New Relic непосредственно из Магазина Azure выполните четыре простых шага, как указано далее.

### Шаг 1. Регистрация через Магазин Azure

1. Войдите на [портал управления Azure](https://manage.windowsazure.com).
2. В нижней части портала управления щелкните **Создать**.
3. Нажмите кнопку **Магазин**.
4. В диалоговом окне **Выберите надстройку** выберите **New Relic** и щелкните **Далее**.
5. В диалоговом окне **Настроить надстройку** выберите требуемый план New Relic.
7. Введите имя службы New Relic, которое будет отображаться в параметрах Azure, или используйте значение по умолчанию **NewRelic**. Это имя должно быть уникальным в списке элементов Магазина Azure, на которые вы подписаны.
8. Выберите значение для региона, например **Запад США**.
9. Нажмите кнопку **Далее**.
10. В диалоговом окне **Просмотреть приобретение** просмотрите сведения о плане и ценах, а также ознакомьтесь с условиями. Если вы согласны с данными условиями, щелкните **Купить**.
11. После нажатия кнопки **Купить** начнется процесс создания учетной записи New Relic. Состояние можно отслеживать на портале управления Azure.
12. Чтобы получить лицензионный ключ New Relic, выберите созданную надстройку и щелкните **Сведения о подключении**. 
13. Скопируйте лицензионный ключ, который появится на экране. Его нужно будет ввести при установке пакета Nuget New Relic.

### Шаг 2. Установка пакета New Relic

Агент веб-сайтов New Relic распространяется в виде пакета NuGet, который можно добавить на веб-сайт с помощью Visual Studio или WebMatrix. Если вы ранее не пользовались WebMatrix или Visual Studio для веб-сайта Azure, см. раздел [Развертывание веб-приложения ASP.NET на веб-сайте Azure с помощью Visual Studio][vswebsite] или [Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix][webmatrixwebsite].

Выполните следующие действия для используемой среды разработки:

**Visual Studio**

1. Откройте решение веб-сайта Visual Studio.

2. Откройте консоль диспетчера пакетов, выбрав **Инструменты > Диспетчер пакетов библиотеки > Консоль диспетчера пакетов**. Установите проект как проект по умолчанию в верхней части окна консоли диспетчера пакетов.

	![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. В командной строке диспетчера пакетов введите следующую команду для установки пакета:

		Install-Package NewRelic.Azure.WebSites

4. При появлении соответствующего запроса введите лицензионный ключ, полученный в Магазине Azure.

	![enter license key][vslicensekey]

<!--5. Optional: At the application name prompt, enter your app's name as it will
   appear in New Relic's dashboard. Or, use your solution name as the default.

	![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. Откройте веб-сайт с помощью WebMatrix.

2. На вкладке **Главная** на ленте выберите **NuGet**.

	![nuget buton on home tab][wmnugetbutton]

3. В коллекции NuGet установите **Официальный источник пакета NuGet** в качестве источника и выполните поиск NewRelic.Azure.WebSites.

	![nuget gallery searching for NewRelic.Azure.WebSites][wmnugetgallery]

4. Выберите **New Relic для веб-сайтов Azure** и щелкните **Установить**.

5. После установки пакета веб-сайт будет содержать папку с именем **newrelic**. Разверните эту папку и откройте файл **newrelic.config**. В этом файле замените значение **REPLACE\_WITH\_LICENSE_KEY** лицензионным ключом, который вы получили из магазина Azure.

	![newrelic folder expanded with newrelic.conf selected][newrelicconf]

	После добавления данных о лицензионном ключе сохраните изменения в файле **newrelic.config**.

### Шаг 3. Настройка веб-сайта и публикация приложения.

Пакет New Relic, добавленный в приложение на предыдущем шаге, настраивается в соответствии с **параметрами приложения**, добавляемыми на веб-сайт Azure. Выполните следующие действия, чтобы добавить эти параметры.

1. Войдите на [портал управления Azure](https://manage.windowsazure.com) и перейдите на свой веб-сайт.

2. На веб-сайте выберите **Настройка**. В разделе **Аналитические средства для разработчиков** выберите **Надстройка** или **Настраиваемый**. Эти методы выводят одинаковые данные, однако требуют немного разные входные данные. Значение **Надстройка** перечисляет текущие лицензии New Relic и позволяет выбрать одну из них, а значение **Настраиваемый** требует указать лицензионный ключ вручную.

	При выборе значения **Надстройка** используйте поле **Выберите надстройку** для своей лицензии New Relic.

	![Image of the add-on fields][add-on]

	Если вы выбрали значение **Настраиваемый**, используйте New Relic в качестве **поставщика** и введите лицензию в поле **Ключ поставщика**.

	![Image of the custom fields][custom]

3. После указания лицензии в поле **Аналитические средства для разработчиков** нажмите кнопку **Сохранить**. После выполнения операции сохранения в раздел **Параметры приложения** этой страницы будут добавлены следующие значения для поддержки New Relic:

	<table border="1">
	<thead>
	<tr>
	<td>Ключ</td>
	<td>Значение</td>
	</tr>
	</thead>
	<tbody>
	<tr>
	<td>COR\_ENABLE\_PROFILING</td><td>1</td>
	</tr>
	<tr>
	<td>COR\_PROFILER</td><td>{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
	</tr>
	<tr>
	<td>COR\_PROFILER\_PATH</td><td>C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
	</tr>
	<tr>
	<td>NEWRELIC\_HOME</td><td>C:\Home\site\wwwroot\newrelic</td>
	</tr>
	<tr>
	<td>NEWRELIC\_LICENSEKEY</td><td>Ваш лицензионный ключ</td>
	</tr>
	</tbody>
	</table><br/>

	<div class="dev-callout"> 
	<strong>Примечание.</strong> 
	<p>Для вступления новых <strong>параметров приложения</strong> в силу может потребоваться до 30 секунд. Чтобы сразу же принудительно применить параметры, перезапустите веб-сайт.</p> 
	</div>


4. Опубликуйте приложение с помощью Visual Studio или WebMatrix.

### Шаг 4. Проверка производительности приложения в New Relic.

Для просмотра панели мониторинга New Relic:

1. На портале Azure нажмите кнопку **Управление**.
2. Войдите, используя адрес электронной почты и пароль для New Relic.
3. В строке меню New Relic выберите **Приложения > (имя приложения)**.

	Автоматически отобразится панель мониторинга **Мониторинг > Обзор**.

	![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

	После выбора приложения из списка в меню **Приложения** на панели мониторинга **Обзор** будут показаны сведения о текущем сервере приложений, а также о браузере.

### <a id="using-new-relic"></a>Использование New Relic

После выбора приложения из списка в меню "Приложения" на панели мониторинга "Обзор" будут показаны сведения о текущем сервере приложений, а также о браузере. Для переключения между двумя представлениями нажмите кнопку **Сервер приложений** или **Браузер**.

В дополнение к <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">стандартному пользовательскому интерфейсу New Relic</a> и <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">функциям детализации панели мониторинга</a> панель "Обзор приложений" имеет дополнительные функции.

<table border="1">
  <thead>
    <tr>
      <th><b>Если вы хотите...</b></th>
      <th><b>Выполните следующие действия...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Показать сведения о панели мониторинга для выбранного сервера приложений или браузера</td>
       <td>Нажмите кнопку <b>Сервер приложений</b> или <b>Браузер</b> .</td>
    </tr>
     <tr>
       <td>Просмотреть пороговые значения оценки <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> по приложению</td>
       <td>Выберите значок оценки Apdex <b>?<b> .</b></b></td>
    </tr>
    <tr>
       <td>Просмотр сведений Apdex по всему миру</td>
       <td>В меню "Обзор" в представлении <b>Браузер</b> выберите любую точку на карте Global Apdex.<br /><b>Подсказка.</b> Чтобы перейти непосредственно к панели мониторинга <a href="https://newrelic.com/docs/site/geography" target="_blank">География</a>, щелкните заголовок <b>Global Apdex</b> или щелкните любую точку на карте Global Apdex.</td>
    </tr>
    <tr>
       <td>Просмотреть <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">панель мониторинга</a> Веб-транзакции</td>
       <td>На панели мониторинга "Обзор приложений" выберите таблицу "Веб-транзакции". Либо щелкните название транзакции, чтобы просмотреть информацию об определенной веб-транзакции (включая <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Ключевые транзакции</a>).</td>
    </tr>
    <tr>
       <td>Просмотреть <a href="https://newrelic.com/docs/site/errors" target="_blank">панель мониторинга</a> Ошибки</td>
       <td>На панели мониторинга "Обзор приложений" щелкните заголовок диаграммы "Частота ошибок".<br /><b>Подсказка.</b> Также вы можете просмотреть панель мониторинга "Ошибки", выбрав <b>Приложения</b> > (ваше приложение) > События > Ошибки.</td>
    </tr>
    <tr>
       <td>Просмотреть информацию о сервере приложений</td>
       <td><p>Выполните любое из следующих действий:<p>
        <ul>
          <li>Переключитесь между табличным представлением узлов и представлением отдельных показателей по каждому узлу.</li>
          <li>Щелкните имя одного из серверов.</li>
          <li>Выберите оценку Apdex для одного из серверов.</li>
          <li>Выберите "Использование процессора" или "Память" одного из серверов.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Ниже приведен пример панели мониторинга "Обзор приложений" при выборе представления "Браузер".

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## Дальнейшие действия

Для получения дополнительных сведений используйте следующие ресурсы:

 * [Установка агента .NET для веб-сайта Azure](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): процедуры установки агента .NET для New Relic. 
 * [Пользовательский интерфейс New Relic](https://newrelic.com/docs/site/the-new-relic-ui): 
Обзор пользовательского интерфейса New Relic, настройка прав пользователей и профилей с использованием стандартных функций и подробных сведений панели мониторинга
 * [Обзор приложений](https://newrelic.com/docs/site/applications-overview): возможности и функции при использовании панели мониторинга "Обзор приложений" в New Relic.
 * [Apdex](https://newrelic.com/docs/site/apdex): обзор способов, используемых Apdex для измерения степени удовлетворенности пользователей приложением.
 * [Мониторинг реального пользователя](https://newrelic.com/docs/features/real-user-monitoring): обзор способов, используемых RUM для детализации информации, такой как время, потраченное 
браузерами пользователей на загрузку веб-страниц, откуда они приходят и какие браузеры используют.
 * [Поиск справки](https://newrelic.com/docs/site/finding-help): ресурсы, доступные через интерактивный центр справки New Relic.


[webmatrixwebsite]: http://www.windowsazure.com/ru-ru/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://www.windowsazure.com/ru-ru/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png

<!--HONumber=35.1-->

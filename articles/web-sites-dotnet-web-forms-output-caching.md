<properties linkid="video-center-detail" urlDisplayName="Сведения" pageTitle="Сведения о Video Center" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Использование кэширования выводимых данных веб-форм ASP.NET с помощью веб-сайтов Azure" authors="jroth" solutions="" manager="" editor="" />



# Использование кэширования выводимых данных веб-форм ASP.NET с помощью веб-сайтов Azure

В этом разделе объясняется, как использовать службу кэша Azure (предварительная версия) для поддержки кэширования вывода страниц ASP.NET для веб-форм ASP.NET. Кэширование вывода страницы представляет собой оптимизацию, которая непосредственно возвращает кэшированную отрисованную страницу в течение определенного времени. Это удобно в ситуациях, когда доступ к странице осуществляется чаще ее изменения. Следует отметить, что кэширование вывода страниц не поддерживается для приложений ASP.NET MVC.

Служба кэша (предварительная версия) предоставляет распределенную службу кэширования, которая является внешней по отношению к веб-сайту. Благодаря этому все экземпляры веб-сайта получают доступ к одной кэшированной отрисовке страницы.

Основные шаги по использованию службы кэша (предварительная версия) для кэширования вывода страниц включают:

* [Создание кэша.](#createcache)
* [Настройка проекта ASP.NET на использование кэша Azure.](#configureproject)
* [Изменение файла web.config.](#configurewebconfig)
* [Использование кэширования вывода для временного возврата кэшированных версий страницы.](#useoutputcaching)

<h2><a id="createcache"></a>Создание кэша</h2>
1. В нижней части портала управления Azure щелкните значок **Создать**.

	![NewIcon][NewIcon]

2. Выберите **Службы данных**, **Кэш**, а затем щелкните **Быстрое создание**.

	![NewCacheDialog][NewCacheDialog]

3. Введите уникальное имя кэша в текстовом поле **Конечная точка**. Затем выберите соответствующие значения для других свойств кэша и нажмите кнопку **Создать кэш**.

4. Выберите значок **Кэш** на портале управления для просмотра всех конечных точек службы кэша.

	![CacheIcon][CacheIcon]

5. Затем можно выбрать одну из конечных точек службы кэша для просмотра ее свойств. В следующих разделах будут использоваться параметры из вкладки **Панель мониторинга** для настройки кэширования в проекте ASP.NET.

<h2><a id="configureproject"></a>Настройка проекта ASP.NET</h2>
1. Во-первых, убедитесь, что [установлена последняя версия][] **Azure SDK для .NET**.

2. В Visual Studio щелкните правой кнопкой мыши проект ASP.NET в **обозревателе решений** и выберите **Управление пакетами NuGet**. (Если вы используете WebMatrix, вместо этого нажмите кнопку **NuGet** на панели инструментов.)

3. Введите **WindowsAzure.Caching** в поле **Поиск в Интернете**.

	![NuGetDialog][NuGetDialog]

4. Выберите пакет **Кэширование Azure** и щелкните **Установить**.

<h2><a id="configurewebconfig"></a>Изменение файла Web.Config</h2>
Кроме внесения ссылки на сборку для кэша, NuGet-пакет добавляет записи заглушки в файл web.config. Чтобы использовать кэш для кэширования вывода страниц ASP.NET, в файл web.config необходимо внести некоторые изменения.

1. Откройте файл **web.config** для проекта ASP.NET.

2. Если присутствуют элементы **caching** и **outputCache**, закомментируйте их (или удалите).

3. Затем раскомментируйте элемент **caching**, добавленный пакетом NuGet кэширования Azure. Конечный результат должен выглядеть примерно так.

	![OutputConfig][OutputConfig]

4. Далее найдите раздел **dataCacheClients**. Раскомментируйте дочерний элемент **securityProperties**.

	![CacheConfig][CacheConfig]

5. В элементе **autoDiscover** задайте атрибут **identifier** для URL-адреса конечной точки кэша. Чтобы найти URL-адрес конечной точки, откройте диалоговое окно свойств кэша на портале управления Azure. На вкладке **Панель мониторинга** скопируйте значение **URL-АДРЕС КОНЕЧНОЙ ТОЧКИ** в разделе **Сводка**.

	![EndpointURL][EndpointURL]

6. В элементе **messageSecurity** задайте атрибут **authorizationInfo** для ключа доступа к кэшу. Чтобы найти ключ доступа, выберите свой кэш на портале управления Azure. Затем щелкните значок **Управление ключами** на нижней панели. Нажмите кнопку копирования рядом с текстовым полем **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА**.

	![ManageKeys][ManageKeys]

<h2><a id="useoutputcaching"></a>Использование кэширования вывода</h2>
Заключительным шагом является настройка страниц в приложении веб-форм ASP.NET на использование кэширования вывода. Это можно сделать, добавив атрибут **OutputCache** в начало источника ASPX. Например:

	<%@ OutputCache Duration="45" VaryByParam="*" %>

В предыдущем примере страница кэшируется в течение сорока пяти секунд. Так как **VaryByParam** имеет значение "*", вывод этой кэшированной страницы не изменяется, даже если передаются другие параметры запроса. Однако в приведенном ниже примере кэшируются разные версии страницы для каждого значения параметра "UserId":

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[установлена последняя версия]: http://www.windowsazure.com/ru-ru/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
  
  


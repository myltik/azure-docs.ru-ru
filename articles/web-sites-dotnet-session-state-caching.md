<properties linkid="video-center-index" urlDisplayName="индекс" pageTitle="Индекс Video Center" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Как использовать ASP.NET Session State с веб-сайтами Azure" authors=""  solutions="" writer="jroth" manager="" editor=""  />




# Как использовать ASP.NET Session State с веб-сайтами Azure

В этом разделе объясняется, как использовать службу кэша Azure (предварительная версия) для поддержки кэширования состояния сеанса ASP.NET.

Без внешнего поставщика состояние сеанса сохраняется в процессе на веб-сервере, на котором размещен сайт. Для веб-сайтов Azure существуют две проблемы, связанные с состоянием сеанса в процессе. Во-первых, для веб-сайтов с несколькими экземплярами состояние сеанса, хранимое на одном экземпляре, будет недоступно для других экземпляров. Поскольку запрос пользователя может быть направлен в любой экземпляр, наличие в нем сведений о сеансе не гарантируется. Во-вторых, любые изменения в конфигурации могут привести к тому, что веб-сайт будет работать абсолютно на другом сервере.

Служба кэша (предварительная версия) предоставляет собой распределенную службу кэширования, которая является внешней по отношению к веб-сайту. Это решает проблему с состоянием сеанса в процессе. Дополнительные сведения об использовании состояния сеанса см. в разделе [Общие сведения о состоянии сеанса ASP.NET][].

Основные шаги для использования службы кэша (предварительная версия) для кэширования состояния сеанса включают:

* [Создание кэша.](#createcache)
* [Настройка проекта ASP.NET на использование кэша Azure.](#configureproject)
* [Изменение файла web.config.](#configurewebconfig)
* [Использование объекта сеанса для хранения и получения кэшированных элементов.](#usesessionobject)

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
1. Во-первых, убедитесь, что [установлена последняя версия][]  **Azure SDK для .NET**.

2. В Visual Studio щелкните правой кнопкой мыши проект ASP.NET в **обозревателе решений** и выберите **Управление пакетами NuGet**. (Если вы используете WebMatrix, вместо этого нажмите кнопку **NuGet** на панели инструментов)

3. Введите **WindowsAzure.Caching** в поле **Поиск в Интернете**.

	![NuGetDialog][NuGetDialog]

4. Выберите пакет **Кэширование Azure**, щелкните **Установить**.

<h2><a id="configurewebconfig"></a>Изменение файла Web.Config</h2>
Кроме внесения ссылки на сборку для кэша, NuGet-пакет добавляет записи заглушки в файл web.config. Чтобы использовать кэш для хранения состояния сеанса, необходимо внести некоторые изменения в web.config.

1. Откройте файл **web.config** для проекта ASP.NET

2. Найдите существующий элемент **sessionState** и закомментируйте его (или удалите).

3. Затем раскомментируйте элемент **sessionState**, добавленный пакетом кэширования Azure NuGet. Конечный результат должен выглядеть примерно так.

	![SessionStateConfig][SessionStateConfig]

4. Далее найдите раздел **dataCacheClients**. Раскомментируйте дочерний элемент **securityProperties**.

	![CacheConfig][CacheConfig]

5. В элементе **autoDiscover** задайте атрибут **identifier** для URL-адреса конечной точки кэша. Чтобы найти URL-адрес конечной точки, откройте диалоговое окно свойств кэша на портале управления Azure. На вкладке **Панель мониторинга** скопируйте значение **URL-АДРЕС КОНЕЧНОЙ ТОЧКИ** в раздел **сводка**.

	![EndpointURL][EndpointURL]

6. В элементе **messageSecurity** задайте атрибут **authorizationInfo** для ключа доступа к кэшу. Чтобы найти ключ доступа, выберите свой кэш на портале управления Azure. Затем щелкните значок **Управление ключами** на нижней панели. Нажмите кнопку копирования рядом с текстовым полем **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА**.

	![ManageKeys][ManageKeys]

<h2><a id="usesessionobject"></a>Использование объекта сеанса в коде</h2>
Последним шагом является начало использования объекта сеанса в коде ASP.NET. Добавление объектов к состоянию сеанса выполняется с помощью метода **Session.Add**. Этот метод использует пары "ключ-значение" для хранения элементов в кэше состояния сеанса.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Следующий код извлекает значение из состояния сеанса.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

Дополнительные сведения об использовании состояния сеанса ASP.NET см. в разделе [Общие сведения о состоянии сеанса ASP.NET][].

  
  
  
  [установлена последняя версия]: http://www.windowsazure.com/ru-ru/downloads/?sdk=net  
  [Общие сведения о состоянии сеанса ASP.NET]: http://msdn.microsoft.com/ru-ru/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png


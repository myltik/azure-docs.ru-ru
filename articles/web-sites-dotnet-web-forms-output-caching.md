<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Использование кэширования выводимых данных веб-форм ASP.NET с помощью веб-сайтов Azure

В этом разделе объясняется, как использовать службу кэша Azure (предварительная версия) для поддержки кэширования вывода страниц ASP.NET для веб-форм ASP.NET. Кэширование вывода страницы представляет собой оптимизацию, которая непосредственно возвращает кэшированную отрисованную страницу в течение определенного времени. Это удобно в ситуациях, когда доступ к странице осуществляется чаще ее изменения. Следует отметить, что кэширование вывода страниц не поддерживается для приложений ASP.NET MVC.

Служба кэша (предварительная версия) представляет собой распределенную службу кэширования, которая является внешней по отношению к веб-сайту. Благодаря этому все экземпляры веб-сайта получают доступ к одной кэшированной отрисовке страницы.

Основные шаги по использованию службы кэша (предварительная версия) для кэширования вывода страниц включают:

-   [Создание кэша.][Создание кэша.]
-   [Настройка проекта ASP.NET на использование кэша Azure.][Настройка проекта ASP.NET на использование кэша Azure.]
-   [Изменение файла web.config.][Изменение файла web.config.]
-   [Использование кэширования вывода для временного возврата кэшированных версий страницы.][Использование кэширования вывода для временного возврата кэшированных версий страницы.]

## <span id="createcache"></span></a>Создание кэша

Экземпляры кэша в службе управляемого кэша создаются с помощью командлетов PowerShell.

> После того как экземпляр службы управляемого кэша создан командлетом PowerShell, его можно просмотреть и настроить на [портале управления Azure][портале управления Azure].

Чтобы создать экземпляр службы управляемого кэша, откройте окно Azure PowerShell.

> Инструкции по установке и использованию Azure PowerShell см. в разделе [Установка и настройка Windows Azure PowerShell][Установка и настройка Windows Azure PowerShell].

Вызовите командлет [Add-AzureAccount][Add-AzureAccount] и введите адрес электронной почты и пароль, связанные с вашей учетной записью. Подписка выбирается по умолчанию, она отображается после вызова командлета [Add-AzureAccount][Add-AzureAccount]. Чтобы сменить подписку, вызовите командлет [Select-AzureSubscription][Select-AzureSubscription].

> Если вы настроили Azure PowerShell сертификатом для своей учетной записи, этот шаг можно пропустить. Дополнительные сведения о подключении Azure PowerShell к своей учетной записи Azure см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Windows Azure PowerShell].

Будет показана выбранная по умолчанию подписка. Чтобы сменить подписку, вызовите командлет [Select-AzureSubscription][Select-AzureSubscription].

Вызовите командлет [New-AzureManagedCache][New-AzureManagedCache] и укажите название, регион, предложение кэша и размер кэша.

В **Name** введите имя поддомена для конечной точки кэша. Конечная точка должна быть строкой длиной от шести до двадцати символов, содержать только строчные буквы и цифры, а также должна начинаться с буквы.

В поле **Область** укажите регион для кэша. Для обеспечения оптимальной производительности создайте кэш в той же области, где находится приложение клиента кэша.

Поля **Sku** и **Память** используются совместно для определения размера кэша. Служба управляемого кэша предоставляется на трех следующих уровнях.

-   Basic — кэш размером от 128 МБ до 1 ГБ с шагом 128 МБ, один именованный кэш по умолчанию.
-   Standard — кэш размером от 1 до 10 ГБ с шагом 1 ГБ, поддержка уведомлений и до десяти именованных кэшей.
-   Premium — кэш размером от 5 до 150 ГБ с шагом 5 ГБ, поддержка уведомлений, высокой доступности и до десяти именованных кэшей.

Выберите значения **Sku** и **Память**, удовлетворяющие требованиям вашего приложения. Обратите внимание, что некоторые возможности кэша, например уведомления и высокая доступность, предоставляются только в некоторых предложениях. Для получения дополнительных сведений о выборе размера и предложения кэша, которые лучше всего подходят для вашего приложения, см. "Предложения кэша".

В следующем примере создается базовый 128 МБ кэш с именем contosocache в Южно-Центральном географическом регионе США.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Полный список параметров и значений, которые могут использоваться при создании кэша, см. в документации к командлету [New-AzureManagedCache][New-AzureManagedCache].

После вызова командлета PowerShell cоздание кэша может занять несколько минут. После создания новый кэш имеет состояние `Running` и готов для использования со значениями по умолчанию, его можно просмотреть и настроить на [портале управления Azure][портале управления Azure].

Ход выполнения операции создания можно отслеживать в окне Azure PowerShell. Когда кэш будет готов к использованию с помощью командлета [New-AzureManagedCache][New-AzureManagedCache], можно посмотреть сведения о кэше, как показано в следующем примере.

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>

В следующих разделах будут использоваться параметры из вкладки **Панель мониторинга** для настройки кэширования в проекте ASP.NET.

## <span id="configureproject"></span></a>Настройка проекта ASP.NET

1.  Во-первых, убедитесь, что [установлена последняя версия][установлена последняя версия] пакета **Azure SDK для .NET**.

2.  В Visual Studio щелкните правой кнопкой мыши по проекту ASP.NET в **обозревателе решений** и выберите **Управление пакетами NuGet**. (Если вы используете WebMatrix, вместо этого нажмите кнопку **NuGet** на панели инструментов.)

3.  Введите **WindowsAzure.Caching** в поле **Поиск в Интернете**.

    ![NuGetDialog][NuGetDialog]

4.  Выберите пакет **Кэширование Azure** и нажмите кнопку **Установить**.

## <span id="configurewebconfig"></span></a>Изменение файла Web.Config

Кроме внесения ссылки на сборку для кэша, NuGet-пакет добавляет записи заглушки в файл web.config. Чтобы использовать кэш для кэширования вывода страниц ASP.NET, в файл web.config необходимо внести некоторые изменения.

1.  Откройте файл **web.config** для проекта ASP.NET.

2.  Если присутствуют элементы **caching** и **outputCache**, закомментируйте их (или удалите).

3.  Затем раскомментируйте элемент **caching**, добавленный пакетом NuGet Azure Caching. Конечный результат должен выглядеть примерно так.

    ![OutputConfig][OutputConfig]

4.  Далее найдите раздел **dataCacheClients**. Раскомментируйте дочерний элемент **securityProperties**.

    ![CacheConfig][CacheConfig]

5.  В элементе **autoDiscover** задайте атрибут **identifier** для URL-адреса конечной точки кэша. Чтобы найти URL-адрес конечной точки, откройте диалоговое окно свойств кэша на портале управления Azure. На вкладке **Панель мониторинга** скопируйте значение **URL-АДРЕС КОНЕЧНОЙ ТОЧКИ** в раздел **сводка**.

    ![EndpointURL][EndpointURL]

6.  В элементе **messageSecurity** задайте атрибут **authorizationInfo** для ключа доступа к кэшу. Чтобы найти ключ доступа, выберите свой кэш на портале управления Azure. Затем щелкните значок **Управление ключами** на нижней панели. Нажмите кнопку копирования рядом с текстовым полем **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА**.

    ![ManageKeys][ManageKeys]

## <span id="useoutputcaching"></span></a>Использование кэширования вывода

Заключительным шагом является настройка страниц в приложении веб-форм ASP.NET на использование кэширования вывода. Это можно сделать, добавив атрибут **OutputCache** в начало источника ASPX. Например:

    <%@ OutputCache Duration="45" VaryByParam="*" %>

В предыдущем примере страница кэшируется в течение сорока пяти секунд. Так как **VaryByParam** имеет значение "\*", вывод этой кэшированной страницы не изменяется, даже если передаются другие параметры запроса. Однако в приведенном ниже примере кэшируются разные версии страницы для каждого значения параметра "UserId":

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>   

  [Создание кэша.]: #createcache
  [Настройка проекта ASP.NET на использование кэша Azure.]: #configureproject
  [Изменение файла web.config.]: #configurewebconfig
  [Использование кэширования вывода для временного возврата кэшированных версий страницы.]: #useoutputcaching
  [портале управления Azure]: https://manage.windowsazure.com/
  [Установка и настройка Windows Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
  [Add-AzureAccount]: http://msdn.microsoft.com/ru-ru/library/dn495128.aspx
  [Select-AzureSubscription]: http://msdn.microsoft.com/ru-ru/library/dn495203.aspx
  [New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
  [установлена последняя версия]: http://www.windowsazure.com/ru-ru/downloads/?sdk=net
  [NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
  [OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
  [CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
  [EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
  [ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG

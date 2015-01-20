<properties pageTitle="Начало работы с системой управления Azure API" metaKeywords="" description="Узнайте, как создавать API, операции и как начать работу с API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="sdanie" />

# Начало работы с Azure API Management

Это руководство покажет как быстро начать работу с API Management и сделать первый вызов API.

> Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][].

Создание экземпляра службы - это первый шаг в работе с API Management. Выполните вход на [портал управления][] и нажмите **Новый**, **Службы приложений**, **Управление API**, **Создать**.

![API Management new instance][api-management-create-instance-menu]

В поле **URL-адрес** укажите уникальное имя поддомена для использования в качестве URL-адреса службы.

Выберите **Подписку** и **Регион** для своего экземпляра службы. После выбора нажмите кнопку "Далее".

![New API Management service][api-management-create-instance-step1]

Введите **Contoso Ltd.** в поле **Название организации** и введите свой адрес электронной почты в поле для адреса администратора.

>Этот адрес электронный почты используется для получения уведомлений от системы API Management. Дополнительные сведения см. в разделе [Настройка уведомлений][].

![New API Management service][api-management-create-instance-step2]

Экземпляры службы управления API доступны на двух уровнях: Developer и Standard. По умолчанию новые экземпляры службы управления API создаются на уровне Developer. Чтобы выбрать уровень Standard, выберите флажок **Дополнительные настройки** и выберите уровень Standard на следующем экране.

>Microsoft Azure предлагает два уровня, где можно запустить службу управления API: Developer и Standard. Уровень Developer предназначен для разработки, тестирования и пилотного запуска приложений API, для которых доступность - это некритичный параметр. На уровне Standard можно масштабировать зарезервированное число блоков для обслуживания большего объема трафика. На уровне Standard служба управления API обеспечивает наибольший уровень мощности и производительности. Данный учебник можно изучать на любом уровне. Дополнительные сведения об уровнях управления API см. в разделе [Цены на управление API][].

Установите флажок для создания экземпляра службы.

![New API Management service][api-management-instance-created]

После создания службы на следующем шаге создается API.

## <a name="create-api"> </a>Создание API

API в API Management представляет набор операций, которые могут быть вызваны клиентскими приложениями. Операции API передаются существующей веб-службе.

Каждый экземпляр службы API Management поставляется предварительно настроенным с примером интерфейса Echo API, в котором можно вызвать любую команду HTTP, а возвращаемое значение будет содержать заголовки и тело, которые были отправлены. В данном учебнике используется серверная веб-служба Echo API для создания нового API в службе управления API под названием **My Echo Service**.

API создаются и настраиваются из консоли API Management, которая доступна на портале управления Azure. Чтобы открыть консоль управления API, щелкните **Консоль управления** на портале Azure для своей службы управления API.

![New API Management console][api-management-management-console]

Чтобы создать **My Echo Service**, выберите пункт **API** в меню **Управление API** слева, а затем **Добавить API**.

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

Следующие три поля используются для настройки нового API.

-	Введите **My Echo Service** в поле **Название веб-API**. **Название веб-API** - это уникальное и описательное имя для вашего API. Оно выводится на порталах разработчика и управления.
-	Введите **http://echoapi.cloudapp.net/api** в поле **URL-адрес веб-службы**. **URL-адрес веб-службы** указывает на службу HTTP, реализующую данный API. Портал управления API направит запросы по этому адресу.
-	Введите **myecho** в поле **Суффикс URL-адреса веб-API**. **Суффикс URL-адреса веб-API** добавляется к базовому URL-адресу службой управления API. Ваши интерфейсы API используют общий основной URL-адрес и отличаются уникальным суффиксом, добавляемым после основного адреса.
-	**Схема URL-адреса веб-API** определяет, какой протокол будет использоваться для доступа к API. По умолчанию указан протокол HTTPs.

Нажмите **Сохранить**, чтобы создать API. После создания нового API на портале управления выводится страница сводных данных для API.

![API summary][api-management-new-api-summary]


>Образец Echo API не использует проверку подлинности, но дополнительные сведения о ее настройке можно найти в разделе [Настройка параметров API][].


## <a name="add-operation"> </a>Добавление операции

Нажмите **Операции**, чтобы отобразить панель операций для этого API. Определения операций используются для проверки входящих запросов и автоматической генерации документации. Ничего не выводится, поскольку еще не добавлено ни одной операции.

![Operations][api-management-myecho-operations]

Нажмите **Добавить операцию**, чтобы добавить новую операцию. Отобразится окно **Новая операция**, где по умолчанию будет выбрана вкладка **Подпись**.

![Operation signature][api-management-operation-signature]

В этом примере указывается операция GET для службы echo. Введите следующие значения в поля на вкладке **Подпись**.

-	Введите **GET** в поле **Операция HTTP**. По мере ввода можно выбрать **GET** из отображенного списка HTTP-команд.
-	Введите **/resource** в поле **Шаблон URL-адреса**.
-	Введите **GET resource** в поле **Отображаемое имя**.
-	Наберите **Демонстрация вызова GET ресурса из примера. Оно обрабатывается базой данных echo, которая возвращает ответ, равный запросу (переданные заголовки и тело запроса возвращаются в том же виде, как они получены)** в поле **Описание**. Описание используется для генерации документации по этой операции при использовании API разработчиками.

Нажмите **Параметры**, чтобы настроить параметры строки запроса для данной операции. Чтобы добавить параметр запроса, нажмите **Добавить параметр запроса** и укажите следующие значения.

-	Введите **param1** в поле **Имя**.
-	Введите **Образец параметра, который обязателен.** в поле **Описание**.
-	Щелкните поле **Тип** и выберите в списке тип **string**. Поддерживаются типы **string**, **number**, **boolean** и **dateTime**.
-	Щелкните поле **Значения**, введите **образец** в это поле, а затем щелкните значок плюса, чтобы добавить текст значения по умолчанию для этого параметра. После добавления текста по умолчанию щелкните за пределами поля **Значения**, чтобы убрать окно добавления значения.
-	Выберите флажок **Обязательный**.

Нажмите **Сохранить**, чтобы добавить только что настроенную операцию в API.


## <a name="add-api-to-product"> </a>Добавление нового API к продукту

Разработчики сперва должны подписаться на продукт, прежде чем делать вызовы API. Продукт предоставляет доступ к API и может содержать ограничения доступа, например квоты использования и ограничения скорости. На этом шаге руководства мы добавим My Echo API к существующему продукту.

Выберите пункт **Продукты** в меню **Управление API** слева и настройте продукты, доступные в данном экземпляре API.

![Products][api-management-list-products]

По умолчанию каждый экземпляр API управления поставляется с двумя примерами продуктов:

-	**Starter**
-	**Unlimited**

В данном учебнике будет использоваться продукт **Starter**. Нажмите **Starter**, чтобы просмотреть настройки, в том числе API, которые связаны с этим продуктом.

![Add API][api-management-add-api-to-product]

Нажмите **Добавить API к продукту**.

![Add API][api-management-add-myechoapi-to-product]

Выберите флажок для **Мой Echo API** и нажмите **Сохранить**.

![API added][api-management-api-added-to-product]

Теперь **Мой Echo API** связан с продуктом, разработчики могут подписываться на него и начинать пользоваться API.

>Данный шаг учебника использует продукт **Starter**, который был заранее настроен и готов к использованию. Пошаговое руководство по созданию нового продукта см. в разделе [Как создать и опубликовать продукт][].

Администратор автоматически подписывается на все продукты и может получать доступ к интерфейсам API, которым эти продукты предоставляют доступ. Следовательно, не нужно вручную подписываться на только что созданный продукт перед отправкой вызова.

## <a name="call-operation"> </a>Вызов операции из портала разработчика

Операции могут быть вызваны из портала разработчика, где предоставляется удобный способ для просмотра и проверки операций API. На данном шаге учебника будет вызван метод Get, который был добавлен в **My Echo Service**. Выберите **портал разработчика** в меню сверху справа портала управления.

![Developer portal][api-management-developer-portal-menu]

Выберите **API** в верхнем меню и нажмите **My Echo Service**, чтобы увидеть доступные операции.

![Developer portal][api-management-developer-portal-myecho-api]

Обратите внимание, что показаны описание и параметры, добавленные при создании операции, что предоставляет документацию разработчикам, которые будут использовать эту операцию.

Нажмите **Ресурс GET** и выберите **Открыть консоль**. 

![Operation console][api-management-developer-portal-myecho-api-console]

Введите несколько значений для параметров и укажите ключ разработчика, а затем нажмите **HTTP Get**.

€

После вызова операции портал разработчика отображает **Запрошенный URL-адрес** у службы базы данных, **Состояние ответа**, **Заголовки ответа** и **Содержание ответа**. 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>Дальнейшие действия

-   Настройка политик
-   Настройка портала разработчика
-   Трассировка вызовов с помощью инспектора API

[Бесплатная ознакомительная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/

[Создание экземпляра управления API]: #create-service-instance
[Создание API]: #create-api
[Добавление операции]: #add-operation
[Добавление нового API в продукт]: #add-api-to-product
[Подписка на продукт, содержащий API]: #subscribe
[Вызов операции с портала разработчика]: #call-operation
[Просмотр аналитики]: #view-analytics
[Дальнейшие действия]: #next-steps

[Настройка параметров API]: ../api-management-howto-create-apis/#configure-api-settings
[Настройка уведомлений]: ../api-management-howto-configure-notifications
[Ответы]: ../api-management-howto-add-operations/#responses
[Как создать и опубликовать продукт]: ../api-management-howto-add-products
[Начало работы с расширенной настройкой API]: ../api-management-get-started-advanced
[Цены на управление API]: http://azure.microsoft.com/pricing/details/api-management/
[Портал управления]: https://manage.windowsazure.com/

[Настройка политик]: ../api-management-howto-policies
[Настройка портала разработчика]: ../api-management-customize-portal
[Отслеживание вызовов с помощью инспектора API]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->

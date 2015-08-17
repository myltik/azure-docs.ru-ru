<properties
	pageTitle="Добавление кэширования для повышения производительности в управлении API Azure"
	description="Сведения об уменьшении задержки, использования пропускной способности и загрузки веб-службы для вызовов службы управления API."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="08/05/2015"
	ms.author="sdanie"/>

# Добавление кэширования для повышения производительности в управлении API Azure

Операции в управлении API можно настроить для кэширования ответов. Кэширование ответов может значительно уменьшить время задержки API, потребляемую пропускную способность, и нагрузку на веб-службу применительно к данным, которые изменяются редко.

В этом руководстве показано, как добавить кэширование ответов для API и настроить политики для примеров операций Echo API. Затем можно вызвать операцию из портала разработчика, чтобы проверить кэширование в действии.


## Предварительные требования

Перед выполнением шагов из этого руководства необходимо получить экземпляр службы управления API с настроенным API и продуктом. Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра службы управления API][] в руководстве [Приступая к работе с управлением API][].

## <a name="configure-caching"> </a>Настройка операции для кэширования

На этом этапе необходимо проанализировать параметры кэширования операции **GET Resource (cached)** типового интерфейса Echo Api.

>[AZURE.NOTE]Каждый экземпляр службы API Management поставляется предварительно настроенным с Echo API, с которым можно экспериментировать при изучении API Management. Дополнительные сведения см. в разделе [Начало работы с Azure API Management][].

Для начала щелкните **Управление** на портале Azure службы управления API. Будет открыт портал издателя службы управления API.

![Портал издателя][api-management-management-console]

Щелкните **API** в левом меню **API Management**, а затем **Echo API**.

![Echo API][api-management-echo-api]

Перейдите на вкладку **Операции** и щелкните операцию **GET Resource (cached)** в списке **Операции**.

![Операции интерфейса Echo API][api-management-echo-api-operations]

Перейдите на вкладку **Кэширование** для просмотра параметров кэширования для этой операции.

![Вкладка «Кэширование»][api-management-caching-tab]

Чтобы включить кэширование для операции, установите флажок **Включить**. В этом примере кэширование включено.

Каждый ответ операции содержит ключ на основе значений в полях **В зависимости от параметров строки запроса** и **В зависимости от заголовков**. Если вы хотите кэшировать несколько ответов на основании параметров строки запроса или заголовков, то такой режим можно настроить с помощью этих полей.

**Длительность** указывает интервал срока действия кэшированных ответов. В этом примере интервал составляет **3600** секунд, что эквивалентно одному часу.

В случае использования конфигурации кэширования в этом примере операция **GET Resource (cached)** будет возвращать ответ из серверной службы. Этот ответ будет кэшироваться, а также отмечаться (ключом) указанных заголовков и параметров строки запроса. Для последующих вызовов операции с совпадающими параметрами будет возвращаться кэшированный ответ до тех пор, пока не истечет срок действия кэша.

## <a name="caching-policies"> </a>Анализ политик кэширования

На этом шаге необходимо проанализировать параметры кэширования для операции **GET Resource (cached)** образца Echo API.

После настройки параметров кэширования на вкладке **Кэширование** для операции добавляются политики кэширования. Эти политики можно просматривать и изменять в редакторе политик.

Щелкните **Политики** в левом меню **API Management** и выберите **Echo API / GET Resource (cached)** в раскрывающемся списке **Операция**.

![Операция с диапазоном политик][api-management-operation-dropdown]

При этом в редакторе политик отображаются политики для данной операции.

![Редактор политик API Management][api-management-policy-editor]

Определение политики для этой операции содержит политики, определяющие конфигурацию кэширования, которые были проанализированы с помощью вкладки **Кэширование** на предыдущем этапе.

	<policies>
		<inbound>
			<base />
			<cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
				<vary-by-header>Accept</vary-by-header>
				<vary-by-header>Accept-Charset</vary-by-header>
			</cache-lookup>
			<rewrite-uri template="/resource" />
		</inbound>
		<outbound>
			<base />
			<cache-store caching-mode="cache-on" duration="3600" />
		</outbound>
	</policies>

>Изменения, внесенные в политики кэширования в редакторе политик, будут отражаться на вкладке **Кэширование** операции и наоборот.

## <a name="test-operation"> </a>Вызов операции и проверка кэширования

Чтобы увидеть процесс кэширования в действии, можно вызвать операцию из портала разработчика. В правом верхнем меню выберите пункт **Developer portal** (Портал разработчика).

![Портал разработчика][api-management-developer-portal-menu]

Щелкните **API** в меню вверху и выберите **Echo API**.

![Echo API][api-management-apis-echo-api]

>Если есть только один настроенный или видимый API для данной учетной записи, тогда щелчок по API сразу приведет к операциям для этого API.

Выберите операцию **GET Resource (cached)** и щелкните **Открыть консоль**.

![Открытие консоли][api-management-open-console]

Консоль позволяет вызывать операции прямо на портале разработчика.

![Консоль][api-management-console]

Сохраните значения по умолчанию для **param1** и **param2**.

Выберите требуемый ключ в раскрывающемся списке **ключ подписки**. Если ваша учетная запись содержит только одну подписку, то она уже будет выбрана.

Введите **sampleheader:value1** в текстовом поле **Заголовки запроса**.

Щелкните **HTTP Get** и запишите значение заголовков ответа.

Введите **sampleheader:value2** в текстовом поле **Заголовки запроса** и щелкните **HTTP Get**.

Следует отметить, что значение **sampleheader** по-прежнему совпадает со значение **value1** в ответе. Попробуйте ввести другие значения и убедитесь, что возвращается кэшированный ответ из первого вызова.

Введите **25** в поле **param2** и щелкните **HTTP Get**.

Следует отметить, что значением **sampleheader** в ответе теперь является **value2**. Так как результаты операции определяются строкой запроса, то предыдущий кэшированный ответ не возвращается.

## <a name="next-steps"> </a>Дальнейшие действия

-	Просмотрите другие разделы в руководстве [Приступая к работе с расширенными параметрами API][].
-	Дополнительные сведения о политиках кэширования см. в разделе [Политики кэширования][] в [Справочнике по политикам API Management][].

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Начало работы с Azure API Management]: api-management-get-started.md
[Приступая к работе с управлением API]: api-management-get-started.md
[Приступая к работе с расширенными параметрами API]: api-management-get-started-advanced.md

[Справочнике по политикам API Management]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Политики кэширования]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Создание экземпляра службы управления API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps

<!---HONumber=August15_HO6-->
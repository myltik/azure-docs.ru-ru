<properties
	pageTitle="Пользовательское кэширование в службе управления API Azure"
	description="Информация о кэшировании элементов с помощью ключа в службе управления API Azure"
	services="api-management"
	documentationCenter=""
	authors="darrelmiller"
	manager=""
	editor=""/>

<tags
	ms.service="api-management"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="12/16/2015"
	ms.author="v-darmi"/>

# Пользовательское кэширование в службе управления API Azure
В службе управления API Azure есть встроенная поддержка [кэширования ответов HTTP](api-management-howto-cache.md) с помощью URL-адреса ресурса в качестве ключа. Ключ можно изменить с помощью заголовков запроса, используя свойства `vary-by`. Этот способ подходит для кэширования всех ответов HTTP (или представлений), но иногда его можно использовать только для кэширования части представления. Новые политики [cache-lookup-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) и [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) позволяют сохранять и извлекать произвольные фрагменты данных из определений политики. Эта возможность также полезна для ранее представленной политики [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest), так как теперь можно кэшировать ответы от внешних служб.

## Архитектура  
Служба управления API использует общий кэш данных клиентов таким образом, чтобы при увеличении масштаба до нескольких единиц вы по-прежнему имели доступ к тем же кэшированным данным. Но если вы работаете с развертыванием в нескольких регионах, в каждом регионе существует отдельный кэш. Поэтому важно обрабатывать кэш как хранилище данных, которое является единственным источником конкретной информации. Если вы так и делали, а затем решили воспользоваться преимуществами развертывания в нескольких регионах, клиенты с пользователями, которые ездят в командировки, могут потерять доступ к кэшированным данным.

## Кэширование фрагментов
В определенных случаях возвращаемые ответы содержат часть данных, которые трудно определить и которые остаются новыми в течение приемлемого времени. Например, рассмотрим созданную авиакомпанией службу, которая предоставляет сведения о бронировании авиабилетов, статусе рейсов и другую информацию. Если пользователь является участником программы сбора баллов авиакомпании, он также увидит их текущее состояние и накопленные мили. Эта информация о пользователе может храниться в другой системе, но, возможно, придется включить ее в ответы, возвращаемые на запрос о статусе рейсов и бронировании билетов. Это можно сделать с помощью кэширования фрагментов. Основное представление может быть возвращено с сервера-источника с помощью специального маркера, который указывает, где нужно вставить информацию о пользователе.

Рассмотрим следующий ответ JSON с API серверной части:


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Также рассмотрим дополнительный ресурс по адресу `/userprofile/{userid}`:

     { "username" : "Bob Smith", "Status" : "Gold" }

Чтобы определить информацию соответствующего пользователя, которая должна быть включена, необходимо определить конечного пользователя. Этот механизм зависит от реализации. В качестве примера я использую утверждение `Subject` маркера `JWT`.

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Мы сохраняем это значение `enduserid` в переменной контекста для дальнейшего использования. Затем нужно определить, получил ли предыдущий запрос сведения о пользователе и сохранил ли их в кэше. Для этого мы используем политику `cache-lookup-value`.

	  <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Если в кэше нет записи, которая соответствует значению ключа, переменная контекста `userprofile` не создается. Проверим правильность подстановки с помощью политики потока управления `choose`.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Если переменная контекста `userprofile` не существует, нам нужно выполнить запрос HTTP, чтобы получить ее.

	<send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
	</send-request>

Чтобы создать URL-адрес ресурса профиля пользователя, используем `enduserid`. После получения ответа можно будет извлечь текст из ответа и снова сохранить его в переменной контекста.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Чтобы не выполнять этот HTTP-запрос еще раз, когда тот же пользователь выполняет другой запрос, можно сохранить профиль пользователя в кэше.

	<cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Значение сохраняется в кэше с помощью того же ключа, который изначально использовался для его извлечения. Длительность хранения значения зависит от частоты изменения информации и толерантности пользователей к устаревшим сведениям.

Важно осознавать, что извлечение из кэша все еще выполняется как внепроцессный сетевой запрос и может добавить десятки миллисекунд к запросу. Преимущества можно заметить, когда определение сведений профиля пользователя занимает значительно больше времени, чем извлечение данных из кэша, из-за запросов к базе данных и статистической обработки информации с нескольких серверов.

В самом конце процесса нам нужно обновить возвращенный ответ с информацией профиля пользователя.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Я решил использовать кавычки как часть маркера, чтобы, даже если не произойдет замена, ответ по-прежнему содержал допустимые данные JSON. Основная цель этого подхода — упростить отладку.

Выполнив все действия, в качестве результата вы получите политику, которая выглядит приблизительно так:

     <policies>
    	<inbound>
    		<!-- How you determine user identity is application dependent -->
    		<set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

    		<!--Look for userprofile for this user in the cache -->
    		<cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

    		<!-- If we don’t find it in the cache, make a request for it and store it -->
    		<choose>
    			<when condition="@(!context.Variables.ContainsKey("userprofile"))">
    				<!—Make HTTP request to get user profile -->
    				<send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
    					<set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
    					<set-method>GET</set-method>
    				</send-request>

    				<!—Store response body in context variable -->
    				<set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

    				<!—Store result in cache -->
    				<cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
    			</when>
    		</choose>
    		<base />
    	</inbound>
    	<outbound>
    		<!—Update response body with user profile-->
    		<find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
    		<base />
    	</outbound>
     </policies>

Этот метод кэширования используется в основном на веб-сайтах, на которых HTML формируется на стороне сервера для отображения в виде одной страницы. Этот метод также можно использовать в API-интерфейсах, в которых клиенты не могут выполнять клиентское HTTP-кэширование или в которых нежелательно возлагать ответственность за кэширование на клиент.

Такое же кэширование фрагментов можно выполнить на внутренних веб-серверах с помощью сервера кэширования Redis. Однако для этой задачи лучше использовать службу управления API, если кэшированные фрагменты и основные ответы поступают с разных серверов.

## Прозрачное управление версиями
Это управление рекомендуется использовать для нескольких версий реализации API, которые должны поддерживаться одновременно. Оно обеспечивает поддержку различных сред (разработки, тестирования, производства и т. д.) или поддержку более старых версий API, благодаря чему пользователи API получают дополнительное время для перехода на более новые версии.

Чтобы не заставлять разработчика клиента изменять URL-адреса с `/v1/customers` на `/v2/customers`, рекомендуется хранить в данных профиля пользователя версию API, которую предпочитает пользователь, и вызывать соответствующий внутренний URL-адрес. Чтобы определить правильный внутренний URL-адрес для вызова конкретного клиента, необходимо запросить конкретные данные конфигурации. Кэшируя эти данные конфигурации, мы можем свести к минимуму снижение производительности во время выполнения поиска.

Сначала следует определить идентификатор, который используется для настройки нужной версии. В этом примере я решил связать версию с ключом подписки продукта.

		<set-variable name="clientid" value="@(context.Subscription.Key)" />

Затем выполним поиск по кэшу, чтобы выяснить, извлечена ли нужная клиентская версия.

		<cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Затем проверим, удалось ли найти ее в кэше.

	<choose>
		<when condition="@(!context.Variables.ContainsKey("clientversion"))">

Если не удалось, ее нужно извлечь.

	<send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
        		<set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
        		<set-method>GET</set-method>
	</send-request>

Извлеките текст из ответа.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Сохраните его в кэше для дальнейшего использования.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Наконец, обновите внутренний URL-адрес, чтобы выбрать версию службы, которую предпочитает клиент.

	<set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

Полная политика выглядит так:

	 <inbound>
		<base />
		<set-variable name="clientid" value="@(context.Subscription.Key)" />
		<cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

		<!-- If we don’t find it in the cache, make a request for it and store it -->
		<choose>
			<when condition="@(!context.Variables.ContainsKey("clientversion"))">
				<send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
					<set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
					<set-method>GET</set-method>
				</send-request>
				<!-- Store response body in context variable -->
				<set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
				<!-- Store result in cache -->
				<cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
			</when>
		</choose>
		<set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
	</inbound>


Разрешение пользователям API прозрачно управлять доступом клиентов к версиям серверной части без необходимости обновления и повторного развертывания клиентов решает многие проблемы управления версиями API.

## Изоляция клиентов

Некоторые компании с крупными развертываниями, состоящими из нескольких клиентов, создают отдельные группы клиентов на отдельных развернутых серверах. Это минимизирует число пользователей, которых может коснуться ошибка оборудования на сервере. Этот подход также обеспечивает поэтапное развертывание новых версий программного обеспечения. В идеале эта архитектура серверной части должна быть прозрачной для пользователей API. Этого можно добиться за счет такого же прозрачного управления версиями, которое реализуется посредством управления внутренними URL-адресами с помощью состояния конфигурации для каждого ключа API.

Вместо возвращения предпочтительной версии API для каждого ключа подписки вы будете возвращать идентификатор клиента для назначенной группы оборудования. Этот идентификатор можно использовать для создания соответствующих внутренних URL-адресов.

## Сводка
Использование кэширования службы управления API Azure для хранения любых данных обеспечивает эффективный доступ к данным конфигурации, которые могут повлиять на способ обработки входящего запроса. Этот подход также можно использовать для хранения фрагментов данных, которые могут дополнять ответы, возвращаемые из серверной части API.

## Дальнейшие действия
Отправьте нам свой отзыв в дискуссию Disqus к данной статье, если эти политики позволяют вам использовать другие сценарии либо если вы хотите, но пока не можете использовать те или иные сценарии.

<!---HONumber=AcomDC_1217_2015-->
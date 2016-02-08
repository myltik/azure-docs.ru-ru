<properties 
   pageTitle="Кэширование поставщика кэша вывода ASP.NET"
   description="Информация о том, как выходные данные страницы ASP.NET кэшируются с помощью кэша Redis для Azure."
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="01/13/2016"
   ms.author="sdanie" />

# Поставщик кэша вывода ASP.NET для кэша Redis для Azure

Поставщик кэша вывода Redis представляет собой механизм внепроцессного хранения для выходных данных кэширования. Эти данные предназначены специально для полных HTTP-ответов (кэширование вывода страниц). Поставщик подключается к новой точке расширения поставщика вывода кэша, которая появилась в ASP.NET 4.

Чтобы использовать поставщик кэша вывода Redis, сначала настройте кэш, а затем приложение ASP.NET, используя пакет NuGet поставщика кэша вывода Redis. Эта статья содержит информацию о том, как настроить приложение, чтобы оно использовало поставщик кэша вывода Redis. Дополнительные сведения о создании и настройке экземпляра кэша Redis для Azure см. в разделе [Создание кэша](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## Сохранение выходных данных страницы ASP.NET в кэше

Для настройки клиентского приложения в Visual Studio используйте пакет NuGet поставщика кэша вывода Redis: щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.

![Управление пакетами NuGet с помощью кэша Redis для Azure](./media/cache-asp.net-output-cache-provider/IC729541.png)

В текстовом поле **Поиск в Интернете** введите **RedisSessionStateProvider**, выберите нужный результат поиска и щелкните **Установить**.

![Поставщик кэша вывода для кэша Redis для Azure](./media/cache-asp.net-output-cache-provider/IC751727.jpg)

Пакет NuGet поставщика кэша вывода Redis имеет зависимость от пакета StackExchange.Redis.StrongName. Если в проекте отсутствует пакет StackExchange.Redis.StrongName, он будет установлен. Обратите внимание, что помимо пакета со строгим именем StackExchange.Redis.StrongName существует также версия с нестрогим именем StackExchange.Redis. Если проект использует версию с нестрогим именем StackExchange.Redis, необходимо удалить ее до или после установки пакета NuGet поставщика кэша вывода Redis. В противном случае в проекте возникнет конфликт имен. Дополнительные сведения об этих пакетах см. в разделе [Настройка клиентов кэша .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Пакет NuGet загружает и добавляет требуемые ссылки на сборку и добавляет следующий раздел в файл web.config, который содержит необходимые настройки для приложения ASP.NET, позволяющие использовать поставщик кэша вывода Redis.

    <caching>
      <outputCachedefaultProvider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache"type="Microsoft.Web.Redis.RedisOutputCacheProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

Раздел с комментариями содержит пример атрибутов и возможные настройки для каждого из них.

Задайте для атрибутов значения из колонки своего кэша на портале Microsoft Azure и настройте другие параметры по своему усмотрению. Инструкции по доступу к свойствам кэша см. в разделе [Настройка параметров кэша Redis](cache-configure.md#configure-redis-cache-settings).

-	**host** — укажите конечную точку кэша.
-	**port** — используйте порт без или с SSL в зависимости от параметров SSL.
-	**accessKey** — используйте для кэша первичный или вторичный ключ.
-	**ssl** — задайте значение true, если нужно обеспечить безопасный обмен данными между клиентом и кэшем с помощью SSL. В противном случае задайте значение false. Обязательно укажите правильный порт.
	-	Для новых кэшей не SSL порт по умолчанию запрещен. Если вы хотите, чтобы для этого параметра использовался SSL-порт, укажите значение true. Дополнительные сведения о том, как настроить использование порта без SSL, см. в статье [Настройка кэша](cache-configure.md) в разделе [Порты доступа](cache-configure.md#access-ports).
-	**databaseId** — укажите базу данных, которую необходимо использовать для выходных данных кэша. Если значение в этом поле не задано, по умолчанию используется значение 0.
-	**applicationName** — ключи хранятся в кэше Redis как <AppName>\_<SessionId>\_Data. Это позволяет нескольким приложениям совместно использовать один ключ. Этот параметр — необязательный, и, если для него не указано другое значение, используется значение по умолчанию.
-	**connectionTimeoutInMilliseconds** — этот параметр позволяет переопределить параметр connectTimeout в клиенте StackExchange.Redis. Если для параметра connectTimeout значение не указано, по умолчанию используется значение 5000. Дополнительную информацию см. в статье [Модель конфигурации StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-	**operationTimeoutInMilliseconds** — этот параметр позволяет переопределить параметр syncTimeout в клиенте StackExchange.Redis. Если для параметра syncTimeout значение не указано, по умолчанию используется значение 1000. Дополнительную информацию см. в статье [Модель конфигурации StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Добавьте директиву OutputCache для каждой страницы, для которой требуется кэшировать выходные данные.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

В этом примере кэшированные данные страницы будут оставаться в кэше в течение 60 секунд, а для каждой комбинации параметров будет кэшироваться другая версия страницы. Для получения дополнительной информации о директиве OutputCache см. статью [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Когда эти действия будут выполнены, приложение будет соответствующим образом настроено и сможет использовать поставщик кэша вывода Redis.

## Дальнейшие действия

Посетите страницу [Поставщик состояний сеансов ASP.NET для кэша Redis для Azure](cache-asp.net-session-state-provider.md).

<!---HONumber=AcomDC_0128_2016-->
---
title: Кэширование поставщика кэша вывода ASP.NET
description: Информация о том, как выходные данные страницы ASP.NET кэшируются с помощью кэша Redis для Azure.
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: wesmc
ms.openlocfilehash: 81c95949971d54833ca7a15ec5148116c94767f7
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909828"
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Поставщик кэша вывода ASP.NET для кэша Redis для Azure
Поставщик кэша вывода Redis представляет собой механизм внепроцессного хранения для выходных данных кэширования. Эти данные предназначены специально для полных HTTP-ответов (кэширование вывода страниц). Поставщик подключается к новой точке расширения поставщика вывода кэша, которая появилась в ASP.NET 4.

Чтобы использовать поставщик кэша вывода Redis, сначала настройте кэш, а затем приложение ASP.NET, используя пакет NuGet поставщика кэша вывода Redis. Этот раздел содержит информацию о том, как настроить приложение, чтобы оно использовало поставщик кэша вывода Redis. Дополнительные сведения о создании и настройке экземпляра кэша Redis для Azure см. в разделе [Создание кэша](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Сохранение выходных данных страницы ASP.NET в кэше
Чтобы настроить клиентское приложение в Visual Studio, используя пакет NuGet RedisCacheSessionState, в меню **Сервис** выберите **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

Выполните следующую команду в окне `Package Manager Console`:
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Пакет NuGet поставщика кэша вывода Redis имеет зависимость от пакета StackExchange.Redis.StrongName. Если в проекте отсутствует пакет StackExchange.Redis.StrongName, то он будет установлен. Дополнительные сведения о пакете NuGet RedisOutputCacheProvider см. на странице NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Помимо пакета со строгим именем StackExchange.Redis.StrongName существует также версия с нестрогим именем StackExchange.Redis. Если проект использует версию с нестрогим именем StackExchange.Redis, то необходимо удалить ее. В противном случае в проекте возникнет конфликт имен. Дополнительные сведения об этих пакетах см. в разделе [Настройка клиентов кэша .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Пакет NuGet скачивает и добавляет требуемые ссылки на сборку и добавляет следующий раздел в файл web.config. Этот раздел содержит необходимые настройки для приложения ASP.NET, позволяющие использовать поставщик кэша вывода Redis.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
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
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

Раздел с комментариями содержит пример атрибутов и возможные настройки для каждого из них.

Задайте для атрибутов значения из колонки своего кэша на портале Microsoft Azure и настройте другие параметры по своему усмотрению. Инструкции по доступу к свойствам кэша см. в разделе [Настройка параметров кэша Redis](cache-configure.md#configure-redis-cache-settings).

* **host** — укажите конечную точку кэша.
* **port** — используйте порт без или с SSL в зависимости от параметров SSL.
* **accessKey** — используйте для кэша первичный или вторичный ключ.
* **ssl** — задайте значение true, если нужно обеспечить безопасный обмен данными между клиентом и кэшем с помощью SSL. В противном случае задайте значение false. Обязательно укажите правильный порт.
  * Для новых кэшей не SSL порт по умолчанию запрещен. Если вы хотите, чтобы для этого параметра использовался SSL-порт, укажите значение true. Дополнительные сведения о том, как настроить использование порта без SSL, см. в статье [Настройка кэша](cache-configure.md#access-ports) в разделе [Порты доступа](cache-configure.md).
* **databaseId** — укажите базу данных, которую необходимо использовать для выходных данных кэша. Если значение в этом поле не задано, по умолчанию используется значение 0.
* **applicationName** — ключи хранятся в кэше Redis как `<AppName>_<SessionId>_Data`. Такая схема именования позволяет нескольким приложениям совместно использовать один ключ. Этот параметр — необязательный, и, если для него не указано другое значение, используется значение по умолчанию.
* **connectionTimeoutInMilliseconds** — этот параметр позволяет переопределить параметр connectTimeout в клиенте StackExchange.Redis. Если для параметра connectTimeout значение не указано, по умолчанию используется значение 5000. Дополнительную информацию см. в статье [Модель конфигурации StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** — этот параметр позволяет переопределить параметр syncTimeout в клиенте StackExchange.Redis. Если для параметра syncTimeout значение не указано, по умолчанию используется значение 1000. Дополнительную информацию см. в статье [Модель конфигурации StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Добавьте директиву OutputCache для каждой страницы, для которой требуется кэшировать выходные данные.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

В предыдущем примере кэшированные данные страницы остаются в кэше в течение 60 секунд, а для каждой комбинации параметров кэшируется другая версия страницы. Для получения дополнительной информации о директиве OutputCache ознакомьтесь с [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Когда эти действия будут выполнены, приложение будет соответствующим образом настроено и сможет использовать поставщик кэша вывода Redis.

## <a name="next-steps"></a>Дополнительная информация
Посетите страницу [Поставщик состояний сеансов ASP.NET для кэша Redis для Azure](cache-aspnet-session-state-provider.md).


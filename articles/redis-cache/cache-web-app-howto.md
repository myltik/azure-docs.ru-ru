---
title: Создание веб-приложения ASP.NET с использованием кэша Redis | Документация Майкрософт
description: В этом кратком руководстве содержатся сведения о создании веб-приложения ASP.NET с помощью кэша Redis
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: baaa53b04f608e2cb3546fcac6a6eb4eda4d3c4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640760"
---
# <a name="quickstart-create-a-aspnet-web-app-with-redis-cache"></a>Краткое руководство. Создание веб-приложение ASP.NET с использованием кэша Redis


## <a name="introduction"></a>Введение

В этом кратком руководстве описано, как создать и развернуть веб-приложение ASP.NET в службе приложений Azure с помощью Visual Studio 2017. Пример приложения подключается к кэшу Redis для Azure для хранения и извлечения данных. Завершив работу с кратким руководством, вы получите рабочее веб-приложение, размещенное в Azure, которое выполняет чтение и запись в кэш Redis для Azure.

![Выполнение простого теста в Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством необходимо следующее:

* Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/) с указанными ниже рабочими нагрузками:
    * ASP.NET и веб-разработка.
    * разработка Azure.

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

Откройте Visual Studio и щелкните **Файл**, **Создать**, **Проект**.

![Создание проекта](./media/cache-web-app-howto/cache-create-project.png)

В диалоговом окне "Новый проект" выполните следующие действия:

1. В списке **Шаблоны** разверните узел **Visual C#**.
1. Выберите **Облако**.
1. Щелкните **Веб-приложение ASP.NET**.
1. Убедитесь, что выбрана платформа **.NET Framework 4.5.2** или ее более новая версия.
1. Укажите имя проекта в текстовом поле **Имя**. В этом примере мы использовали имя **ContosoTeamStats**.
1. Последовательно выберите **ОК**.

Откроется экран нового веб-приложения ASP.NET.

![Выбор шаблона проекта](./media/cache-web-app-howto/cache-select-template.png)

Выберите тип проекта **MVC**.

Для параметра **Проверка подлинности** обязательно укажите значение **Без проверки подлинности**. В зависимости от установленной версии Visual Studio значение по умолчанию может быть другим. Чтобы изменить его, щелкните **Изменить проверку подлинности** и выберите **Без проверки подлинности**.

Нажмите кнопку **ОК** , чтобы создать проект.

## <a name="create-a-cache"></a>Создание кэша

Создайте кэш для приложения.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Создайте на компьютере файл с именем *CacheSecrets.config* и поместите его в расположение, которое не будет записано после изменения с исходным кодом примера приложения. В этот кратком руководстве файл *CacheSecrets.config* расположен в папке *C:\AppSecrets\CacheSecrets.config*.

Измените файл *CacheSecrets.config* и добавьте содержимое, приведенное ниже.

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

Замените `<cache-name>` на имя узла кэша.

Замените `<access-key>` первичным ключом для кэша.

> [!TIP]
> При повторном создании первичного ключа доступа вторичный ключ доступа используется во время смены ключей как дополнительный ключ.
>

Сохраните файл.

## <a name="update-the-mvc-application"></a>Обновление приложения MVC

В этом разделе вы обновите приложение для поддержки нового представления, которое будет отображать простой тест кэша Azure Redis.

* [Добавление в файл web.config параметра приложения для использования кэша](#Update-the-webconfig-file-with-an-app-setting-for-the-cache)
* [Настройка приложения для использования клиента StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Обновление HomeController и макета](#update-the-homecontroller-and-layout)
* [Добавление нового представления RedisCache](#add-a-new-rediscache-view)

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Добавление в файл web.config параметра приложения для использования кэша

При локальном запуске приложения сведения в файле *CacheSecrets.config* используются для подключения к экземпляру кэша Redis для Azure. Позже вы развернете это приложение в Azure. А сейчас вы настроите параметр приложения в Azure, который будет использоваться приложением вместо этого файла для получения сведений о соединении кэша. Так как файл *CacheSecrets.config* не развертывается в Azure с помощью приложения, он используется только при тестировании приложения локально. Храните эти сведения в надежном месте для предотвращения вредоносного доступа к данным кэша. Для этого сделайте следующее.

В **обозревателе решений** откройте файл *web.config*, щелкнув его дважды.

![Web.config](./media/cache-web-app-howto/cache-web-config.png)

В файле *web.config* найдите элемент `<appSetting>` и добавьте атрибут `file`. Если использовалось другое имя файла или расположение, замените эти значения на представленные в примере.

* До: `<appSettings>`
* После: ` <appSettings file="C:\AppSecrets\CacheSecrets.config">`

Среда выполнения ASP.NET объединяет содержимое внешнего файла с разметкой в элементе `<appSettings>`. Если указанный файл не удается найти, среда выполнения игнорирует атрибут файла. Секреты (строка подключения к вашему кэшу) не включаются в исходный код приложения. При развертывании веб-приложения в Azure файл *CacheSecrests.config* не будет развернут.

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Настройка приложения для использования StackExchange.Redis

Чтобы настроить приложение для использования пакета NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) для Visual Studio, выберите **Инструменты > Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.

Выполните следующую команду в окне `Package Manager Console`:

```powershell
Install-Package StackExchange.Redis
```

Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis. Если вы предпочитаете использовать версию клиентской библиотеки `StackExchange.Redis` со строгими именами, установите пакет `StackExchange.Redis.StrongName`.

### <a name="update-the-homecontroller-and-layout"></a>Обновление HomeController и макета

В **обозревателе решений** разверните папку **Controllers** и откройте файл *HomeController.cs*.

Добавьте следующие два оператора `using` в верхней части файла для поддержки параметров клиента кэша и приложения.

```csharp
using System.Configuration;
using StackExchange.Redis;
```

Добавьте следующий метод в класс `HomeController` для поддержки нового действия `RedisCache`, которое выполняет некоторые команды для нового кэша.

```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Redis Cache on ASP.NET.";

        var lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        // Connection refers to a property that returns a ConnectionMultiplexer
        // as shown in the previous example.
        IDatabase cache = lazyConnection.Value.GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demostrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        ViewBag.command5 = "CLIENT LIST";
        ViewBag.command5Result = cache.Execute("CLIENT", "LIST").ToString().Replace(" id=", "\rid=");

        lazyConnection.Value.Dispose();

        return View();
    }
```

В **обозревателе решений** разверните папку **Views**>**Shared** и откройте файл *_Layout.cshtml*.

Замените

```csharp
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

на:

```csharp
@Html.ActionLink("Azure Redis Cache Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

### <a name="add-a-new-rediscache-view"></a>Добавление нового представления RedisCache

В **обозревателе решений** разверните папку **Views** и щелкните правой кнопкой мыши папку **Home**. Выберите **Добавить** > **Представление...**.

В диалоговом окне "Добавление представления" введите **RedisCache** для имени представления и щелкните **Добавить**.

Замените код в файле *RedisCache.cshtml* следующим кодом:

```csharp
@{
    ViewBag.Title = "Azure Redis Cache Test";
}

<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>
<br /><br />
<table border="1" cellpadding="10">
    <tr>
        <th>Command</th>
        <th>Result</th>
    </tr>
    <tr>
        <td>@ViewBag.command1</td>
        <td><pre>@ViewBag.command1Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command2</td>
        <td><pre>@ViewBag.command2Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command3</td>
        <td><pre>@ViewBag.command3Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command4</td>
        <td><pre>@ViewBag.command4Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command5</td>
        <td><pre>@ViewBag.command5Result</pre></td>
    </tr>
</table>
```

## <a name="run-the-app-locally"></a>Локальный запуск приложения

По умолчанию проект размещает приложение локально в [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) для тестирования и отладки.

В меню Visual Studio выберите **Отладка** > **Начать отладку**, чтобы создать и запустить приложение локально для выполнения тестирования и отладки.

В браузере на панели навигации щелкните **Azure Redis Cache Test** (Проверка кэша Redis для Azure).

В приведенном ниже примере указан ключ `Message`, ранее имевший кэшированное значение, которое было задано с помощью консоли Redis на портале. Приложение обновило кэшированное значение. Кроме того, оно выполнило команды `PING` и `CLIENT LIST`.

![Выполнение простого теста локально](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Публикация и выполнение в Azure

После успешного локального тестирования приложения вы развернете приложение в Azure и запустите его в облаке.

### <a name="publish-the-app-to-azure"></a>Публикация приложения в Azure

В Visual Studio щелкните правой кнопкой мыши узел проекта в обозревателе решений и выберите **Опубликовать**.

![Опубликовать](./media/cache-web-app-howto/cache-publish-app.png)

Щелкните элемент **Служба приложений Microsoft Azure**, выберите параметр **Создать** и нажмите кнопку **Опубликовать**.

![Публикация в службе приложений](./media/cache-web-app-howto/cache-publish-to-app-service.png)

В диалоговом окне **Создание службы приложений** внесите следующие изменения:

| Параметр | Рекомендуемое значение | ОПИСАНИЕ |
| ------- | :---------------: | ----------- |
| **Имя приложения** | Используйте значение по умолчанию. | При развертывании приложения в Azure его имя будет соответствовать имени узла для приложения. В имя можно добавить суффикс метки времени, если необходимо сделать его уникальным. |
| **Подписка** | Выберите подписку Azure. | В этой подписке будет взиматься плата за связанные операции размещения. Если у вас несколько подписок Azure, убедитесь, что выбрана нужная подписка.|
| **Группа ресурсов** | Используйте группу ресурсов, в которой создан кэш. Например, *TestResourceGroup*. | Группа ресурсов помогает вам управлять всеми ресурсами как группой. Позже, если вы захотите удалить приложение, можно просто удалить всю группу. |
| **План службы приложений** | Щелкните **Создать** и создайте новый план службы приложений с именем *TestingPlan*. <br />Используйте то же **расположение**, что и при создании кэша. <br />В качестве размера выберите **Бесплатный**. | План службы приложений определяет набор вычислительных ресурсов, на которых выполняется веб-приложение. |

![Диалоговое окно "Служба приложений Azure"](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

После настройки параметров размещения службы приложений щелкните **Создать**, чтобы создать новую службу приложений для своего приложения.

Просмотрите состояние публикации в Azure в окне Visual Studio **Выходные данные**. После успешного завершения публикации URL-адрес для службы приложений зарегистрируется, как показано ниже:

![Выходные данные публикации](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Добавление параметра приложения для кэша

После завершения публикации для новой службы приложения добавьте новый параметр приложения. Этот параметр будет использоваться для хранения сведений о подключении к кэшу. В строке поиска в верхней части портала Azure введите имя приложения, чтобы найти только что созданное приложение.

![Поиск службы приложений](./media/cache-web-app-howto/cache-find-app-service.png)

Добавьте параметр нового приложения с именем **CacheConnection**, которое приложение будет использовать для подключения к кэшу. Используйте значение, заданное для `CacheConnection` в файле *CacheSecrets.config*. Значение содержит имя узла и ключ доступа кэша.

![Добавление параметра приложения](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Запуск приложения в Azure

В браузере найдите URL-адрес службы приложений. URL-адрес отображается в результатах выполнения операции публикации в окне выходных данных в Visual Studio. Его также можно найти на портале Azure на странице обзора созданной службы приложений.

На панели навигации щелкните **Azure Redis Cache Test** (проверка кэша Redis для Azure), чтобы проверить доступ к кэшу.

![Выполнение простого теста в Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

При переходе к следующему руководству в нем можно использовать ресурсы, созданные в этом руководстве.

В противном случае, если вы закончите работу с примером приложения из краткого руководства, вы можете удалить ресурсы Azure, созданные в текущем руководстве, чтобы избежать ненужных расходов. 

> [!IMPORTANT]
> Удаление группы ресурсов — необратимая операция, и все соответствующие ресурсы удаляются окончательно. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если ресурсы для размещения этого примера созданы в имеющейся группе ресурсов, содержащей ресурсы, которые следует сохранить, можно удалить каждый ресурс отдельно в соответствующих колонках вместо удаления группы ресурсов.
>

Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.

Введите имя группы ресурсов в текстовое поле **Фильтровать по имени...**. В инструкциях в этой статье использовалась группа ресурсов с именем *TestResources*. В своей группе ресурсов в списке результатов щелкните **...**, а затем **Удалить группу ресурсов**.

![Delete](./media/cache-web-app-howto/cache-delete-resource-group.png)

Подтвердите операцию удаления группы ресурсов. Введите имя группы ресурсов и нажмите кнопку **Удалить**.

Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

## <a name="next-steps"></a>Дополнительная информация

В следующем руководстве вы будете использовать кэш Redis для Azure в более реалистичном сценарии для улучшения производительности приложения. Вы обновите это приложение, чтобы кэшировать результаты списка лидеров с помощью шаблона "Кэш на стороне" с ASP.NET и базой данных.

> [!div class="nextstepaction"]
> [Руководство по созданию списка лидеров с применением шаблона "Кэш на стороне" в ASP.NET](cache-web-app-cache-aside-leaderboard.md)

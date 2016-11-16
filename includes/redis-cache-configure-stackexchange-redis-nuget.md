Приложения .NET могут использовать клиент кэша **StackExchange.Redis** , который можно настроить в Visual Studio, используя пакет NuGet, упрощающий настройку клиентских приложений кэша. 

> [!NOTE]
> Дополнительные сведения см. на странице GitHub, посвященной [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis), и в [документации по клиенту кэша StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).
> 
> 

Чтобы настроить клиентское приложение в Visual Studio, используя пакет StackExchange.Redis из NuGet, щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**. 

![Управление пакетами NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Введите в поле поиска **StackExchange.Redis** или **StackExchange.Redis.StrongName**, выберите в результатах нужную версию и щелкните **Установить**.

> [!NOTE]
> Если вы хотите использовать версию клиентской библиотеки **StackExchange.Redis** со строгими именами, выберите **StackExchange.Redis.StrongName**, в противном случае выберите **StackExchange.Redis**.
> 
> 

![Пакет NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis.

> [!NOTE]
> Если ранее вы настроили проект для использования StackExchange.Redis, то можете проверять наличие обновлений для пакета с помощью **диспетчера пакетов NuGet**. Чтобы проверить наличие обновленных версий пакета NuGet для StackExchange.Redis и установить их, щелкните **Обновления** в окне **Диспетчер пакетов NuGet**. Если доступно обновление для пакета NuGet для StackExchange.Redis, то можно обновить проект, чтобы использовать эту обновленную версию.
> 
> 



<!--HONumber=Nov16_HO2-->



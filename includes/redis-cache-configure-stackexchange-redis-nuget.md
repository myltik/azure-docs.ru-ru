Приложения .NET могут использовать клиент кэша **StackExchange.Redis**, который можно настроить в Visual Studio, используя пакет NuGet, упрощающий настройку клиентских приложений кэша.

>[AZURE.NOTE] Дополнительные сведения см. на странице [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) на github и в [документации по клиенту кэша StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).

Чтобы настроить клиентское приложение в Visual Studio, используя пакет StackExchange.Redis из NuGet, щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**.

![Управление пакетами NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Введите в поле поиска **StackExchange.Redis** или **StackExchange.Redis.StrongName**, выберите в результатах нужную версию и щелкните **Установить**.

>[AZURE.NOTE] При желании использовать версию клиентской библиотеки **StackExchange.Redis** со строгими именами выберите **StackExchange.Redis.StrongName**, в противном случае выберите **StackExchange.Redis**.

![Пакет NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis.

<!---HONumber=AcomDC_0615_2016-->
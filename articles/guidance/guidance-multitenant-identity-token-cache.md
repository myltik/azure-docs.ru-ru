<properties
   pageTitle="Кэширование маркеров доступа в мультитенантном приложении | Microsoft Azure"
   description="Кэширование маркеров доступа, используемых для вызова серверного веб-API"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>


# Кэширование маркеров доступа в мультитенантном приложении

Этот материал входит в [цикл статей]. К статьям прилагается полный [пример приложения].

Получение маркера доступа OAuth является относительно ресурсоемким процессом, поскольку для этого требуется отправить HTTP-запрос в конечную точку маркера. Поэтому рекомендуется по возможности кэшировать маркеры. [Библиотека проверки подлинности Azure AD][ADAL] (ADAL) автоматически кэширует маркеры, полученные из Azure AD, включая маркеры обновления.

ADAL обеспечивает реализацию кэша маркеров по умолчанию. Однако этот кэш маркеров предназначен для собственных клиентских приложений и _не_ подходит для веб-приложений по следующим причинам:

-	это статический экземпляр, который не является потокобезопасным;
-	он не масштабируется для большого количества пользователей, так как маркеры всех пользователей попадают в один словарь;
-	он не может быть общим для веб-серверов в ферме.

Вместо него следует реализовать настраиваемый кэш маркеров, производный от класса `TokenCache` ADAL. Этот кэш подходит для серверной среды и обеспечивает нужный уровень изоляции между маркерами для разных пользователей.

Класс `TokenCache` хранит словарь маркеров, индексированных по издателю, ресурсу, пользователю и идентификатору клиента. Настраиваемый кэш маркеров должен записывать этот словарь в резервное хранилище, например кэш Redis.

В приложении Tailspin кэш маркеров реализуется классом `DistributedTokenCache`. Эта реализация использует абстракцию [IDistributedCache][distributed-cache] из ASP.NET Core 1.0. Таким образом, резервным хранилищем может быть любая реализация `IDistributedCache`.

-	По умолчанию в приложении Surveys используется кэш Redis.
-	Для веб-сервера на основе одного экземпляра можно использовать [кэш в памяти][in-memory-cache] ASP.NET Core 1.0. (Этот вариант также подходит для локального запуска приложения во время разработки.)

> [AZURE.NOTE] В настоящее время кэш Redis для .NET Core не поддерживается.

`DistributedTokenCache` хранит данные кэша как пары "ключ-значение" в резервном хранилище. Ключом является идентификатор пользователя и идентификатор клиента, поэтому в резервном хранилище имеются отдельные данные кэша для каждого уникального сочетания пользователя и клиента.

![Кэш маркеров](media/guidance-multitenant-identity/token-cache.png)

Резервное хранилище секционируется пользователем. Для каждого HTTP-запроса маркеры для пользователя считываются из резервного хранилища и загружаются в словарь `TokenCache`. Если в качестве резервного хранилища используется Redis, каждый экземпляр сервера в ферме серверов выполняет чтение и запись в один и тот же кэш. Этот метод позволяет поддерживать большое количество пользователей.

## Шифрование кэшированных маркеров

Маркеры являются конфиденциальными данными, поскольку предоставляют доступ к ресурсам пользователей. (Кроме того, хэш маркера (в отличие от хэша пароля) нельзя просто сохранить.) Поэтому крайне важно обеспечить безопасность маркеров. Кэш на основе Redis защищен паролем, но если кто-либо узнает этот пароль, он может получить все кэшированные маркеры доступа. Поэтому `DistributedTokenCache` шифрует все данные, записываемые в резервное хранилище. Шифрование осуществляется с помощью API-интерфейсов [защиты данных][data-protection] ASP.NET Core 1.0.

> [AZURE.NOTE] При развертывании на веб-сайтах Azure ключи шифрования архивируются в сетевом хранилище и синхронизируются на всех компьютерах (см. статью [Управление ключами][key-management]). По умолчанию ключи не шифруются при работе на веб-сайтах Azure, однако можно [включить шифрование с помощью сертификата X.509][x509-cert-encryption].


## Реализация DistributedTokenCache

Класс [DistributedTokenCache][DistributedTokenCache] является производным от класса [TokenCache][tokencache-class] ADAL.

В конструкторе класс `DistributedTokenCache` создает ключ для текущего пользователя и загружает кэш из резервного хранилища.

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

Ключ создается путем объединения идентификатора пользователя и идентификатора клиента. Оба идентификатора берутся из утверждений в `ClaimsPrincipal` пользователя.

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

Чтобы загрузить данные кэша, прочтите сериализованный BLOB-объект из резервного хранилища и вызовите метод `TokenCache.Deserialize` для преобразования BLOB-объекта в данные кэша.

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

При каждом доступе ADAL к кэшу возникает событие `AfterAccess`. Если данные кэша изменились, свойство `HasStateChanged` получает значение "true". В этом случае обновите резервное хранилище в соответствии с изменениями, а затем задайте свойству `HasStateChanged` значение "false".

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache отправляет два следующих события:

- `BeforeWrite`. Вызывается непосредственно перед тем, как ADAL выполнит запись в кэш. Его можно использовать для реализации стратегии параллелизма.
- `BeforeAccess`. Вызывается непосредственно перед тем, как ADAL выполнит чтение из кэша. В этом случае можно перезагрузить кэш, чтобы получить его последнюю версию.

Мы решили не обрабатывать эти два события.

- В случае параллелизма приоритет имеет последняя запись. Это нормально, поскольку маркеры хранятся независимо для каждого пользователя и клиента, поэтому конфликт может произойти только в случае, если один пользователь открыл два сеанса входа одновременно.
- Для операции чтения кэш будет загружаться при каждом запросе. Запросы являются кратковременными. Если в это время кэш будет изменен, следующий запрос получит новое значение.

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[цикл статей]: guidance-multitenant-identity.md
[пример приложения]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->
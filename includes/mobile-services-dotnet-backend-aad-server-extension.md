### Настройка мобильной службы .NET для Azure Active Directory (необязательно)

>[AZURE.NOTE]Эти шаги необязательны, поскольку относятся только к поставщику входа Azure Active Directory.

1. Установите [пакет WindowsAzure.MobileServices.Backend.Security NuGet](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security).

2. В Visual Studio разверните App\_Start и откройте WebApiConfig.cs. Добавьте в начало следующий оператор `using`:

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. Кроме того, в метод `Register` в файле WebApiConfig.cs сразу после создания экземпляра `options` добавьте следующий код:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=August15_HO6-->
### (Необязательно) Настройка мобильной службы .NET для Azure Active Directory

>[AZURE.NOTE] Эти шаги необязательны, поскольку относятся только к поставщику входа Azure Active Directory.

1. Установите [пакет NuGet WindowsAzure.MobileServices.Backend.Security](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security).

2. В Visual Studio разверните App_Start и откройте WebApiConfig.cs. Добавьте в начало следующую инструкцию `using`:

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. Кроме того, в метод `Register` в файле WebApiConfig.cs сразу после создания экземпляра `options` добавьте следующий код:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!--HONumber=47-->

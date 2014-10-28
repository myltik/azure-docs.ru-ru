### (Необязательно) Настройка мобильной службы .NET для входа с помощью AAD

> [WACOM.NOTE] Эти шаги необязательны, поскольку относятся только к поставщику входа Azure Active Directory.

1.  Установите пакет NuGet **WindowsAzure.MobileServices.Backend.Security**.

2.  В Visual Studio разверните App\_Start и откройте файл WebApiConfig.cs. В метод `Register` сразу после `options` добавьте следующий код:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));



### (Необязательно) Настройка мобильной службы .NET для входа с помощью AAD

>[WACOM.NOTE] Эти шаги необязательны, поскольку относятся только к поставщику входа Azure Active Directory.

1. Установите пакет **WindowsAzure.MobileServices.Backend.Security** NuGet.

2. В Visual Studio разверните App_Start и откройте файл WebApiConfig.cs. Добавьте следующий код в метод Register непосредственно сразу после создания экземпляра options.

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


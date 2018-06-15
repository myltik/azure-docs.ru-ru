---
title: Безопасное хранение секретных параметров веб-приложения | Документация Майкрософт
description: Безопасное хранение таких секретных параметров, как учетные данные Azure или сторонние ключи API, с помощью поставщика Key Vault ASP.NET Core, секрета пользователя или построителей конфигурации .NET 4.7.1
services: visualstudio
documentationcenter: ''
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: ''
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: 61cb9fadd60ba35f11e2f3ade94bc647fac9ed72
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235929"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Безопасное хранение секретных параметров веб-приложения

## <a name="overview"></a>Обзор
В этой статье объясняется, как безопасно хранить секретные параметры конфигурации приложений Azure.

Как правило, все параметры конфигурации веб-приложения хранятся в файлах конфигурации, например Web.config. При этом секретные параметры, например учетные данные облачной службы, регистрируются в общедоступных системах управления версиями наподобие GitHub. В свою очередь безопасность может оказаться под угрозой, так как для ее обеспечения требуются дополнительные расходы на изменение исходного кода и перенастройку параметров разработки.

Чтобы обеспечить защиту процесса разработки, создаются инструментарии и библиотеки платформы, которые позволяют безопасно хранить секретные параметры приложения с незначительными изменениями исходного кода или вообще без них.

## <a name="aspnet-and-net-core-applications"></a>Приложения ASP.NET Core и .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Хранение секретных параметров в хранилище секретов пользователя за пределами папки системы управления версиями
Если вы создаете упрощенный прототип или у вас нет доступа к Интернету, переместите секретные параметры из папки системы управления версиями в хранилище секретов пользователя. Хранилище секретов пользователя — это файл, сохраненный в папке профиля пользователя, следовательно, секреты не будут регистрироваться в системе управления версиями. На следующей схеме показано, как применяется [секрет пользователя](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager).

![Хранение секретных параметров с помощью секрета пользователя за пределами системы управления версиями](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Чтобы безопасно хранить секрет при работе с консольным приложением .NET Сore, используйте хранилище Key Vault.

### <a name="save-secret-settings-in-azure-key-vault"></a>Хранение секретных параметров в Azure Key Vault
Если вы разрабатываете командный проект и вам необходимо обеспечить безопасность при совместном использовании исходного кода, используйте хранилище [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Создайте хранилище ключей в своей подписке Azure. Заполните все обязательные поля пользовательского интерфейса и щелкните *Создать* в нижней части колонки.

    ![Создание хранилища Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Предоставьте членам своей команды доступ к хранилищу Key Vault. Если у вас большая команда разработчиков, можно создать [группу Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) и предоставить этой группе безопасности доступ к хранилищу Key Vault. В разделе *Операции управления секретами* в раскрывающемся списке *Разрешения секретов* щелкните *Получить* и выберите *Список*.

    ![Добавление политики доступа к хранилищу Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Добавьте свой секрет в хранилище Key Vault на портале Azure. Чтобы имя секрета Key Vault было допустимым, замените ":" на "--" в параметрах вложенной конфигурации. ":" является недопустимым символом в имени секрета Key Vault.

    ![Добавление секрета Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Установите [расширение аутентификации служб Azure для Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). С помощью этого расширения приложение может получить доступ к хранилищу Key Vault, используя удостоверение Visual Studio для входа.

5. Добавьте в проект следующие пакеты NuGet:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Добавьте следующий код в файл Program.cs:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Добавьте URL-адрес Key Vault в файл launchsettings.json. Имя переменной среды *KEYVAULT_ENDPOINT* определяется в коде, который вы добавили на шаге 6.

    ![Добавление URL-адреса Key Vault в качестве переменной среды проекта](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Начните отладку проекта. Она должна выполниться успешно.

## <a name="aspnet-and-net-applications"></a>Приложения ASP.NET и .NET

Приложение .NET 4.7.1 поддерживает хранилище Key Vault и построители конфигурации секрета. Это позволяет перемещать секреты за пределы папки системы управления версиями, не меняя код.
Чтобы продолжить работу, [скачайте .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) и перенесите приложение, если оно использует более старую версию платформы .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Хранение секретных параметров в файле секрета за пределами папки системы управления версиями
Используйте следующую процедуру, если вы создаете упрощенный прототип и не хотите подготавливать ресурсы Azure.

1. Установите следующий пакет NuGet в своем проекте.
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Создайте файл, похожий на приведенный ниже. Сохраните его за пределами папки проекта.

    ```xml

       <root>
              <secrets ver="1.0">
                     <secret name="secret1" value="foo_one" />
                        <secret name="secret2" value="foo_two" />
                       </secrets>
      </root>
      ```

3. Определите файл секрета как построитель конфигурации в файле Web.config. Поместите этот раздел перед разделом *appSettings*.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Укажите в разделе appSettings, что используется построитель конфигурации секрета. Проверьте наличие записи для секретного параметра с пустым значением.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Запустите отладку приложения. Она должна выполниться успешно.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Хранение секретных параметров в Azure Key Vault
Чтобы настроить хранилище Key Vault для проекта, следуйте инструкциям из раздела об ASP.NET Сore.

1. Установите следующий пакет NuGet в своем проекте.
```
Microsoft.Configuration.ConfigurationBuilders.UserSecrets.1.0.0-preview2.nupkg
```

2. Определите построитель конфигурации Key Vault в файле Web.config. Поместите этот раздел перед разделом *appSettings*. Замените имя Key Vault на *vaultName*, если ваше хранилище размещено в общедоступном облаке Azure, или укажите полный универсальный код ресурса (URI), если вы используете независимое облако.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3.  Укажите в разделе appSettings, что используется построитель конфигурации Key Vault. Проверьте наличие записи для секретного параметра с пустым значением.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Начните отладку проекта. Она должна выполниться успешно.

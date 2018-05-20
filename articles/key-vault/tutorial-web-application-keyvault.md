---
title: Настройка веб-приложения Azure для считывания секрета из Key Vault | Документация Майкрософт
description: Руководство по настройке веб-приложения ASP.Net Core для считывания секрета из Key Vault
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: b4e317a82b93513c6161d9da0c55883e99580cbb
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Руководство по настройке веб-приложения Azure для считывания секрета из Key Vault

В этом руководстве описано, как настроить в веб-приложении Azure чтение данных из хранилища ключей с помощью управляемых удостоверений службы. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание Key Vault;
> * сохранение секрета в Key Vault;
> * создание веб-приложения Azure;
> * включение удостоверений управляемой службы;
> * предоставление разрешений, необходимых приложению для чтения данных из Key Vault.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

Чтобы войти в Azure с помощью CLI, введите следующее:

```azurecli
az login
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

Созданная вами группа ресурсов будет использоваться далее в этом руководстве.

## <a name="create-an-azure-key-vault"></a>Создание Azure Key Vault

Теперь создайте Key Vault в группе ресурсов, созданной на предыдущем шаге. Для этого нужно предоставить некоторые сведения.

>[!NOTE]
> В этом руководстве для Key Vault используется имя Contoso KeyVault2, но вам нужно выбрать и использовать другое уникальное имя.

* Имя хранилища: **ContosoKeyVault**.
* Имя группы ресурсов: **ContosoResourceGroup**.
* Расположение: **восточная часть США**.

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

В выходных данных команды будут показаны свойства созданного Key Vault. Запишите значения двух указанных ниже свойств.

* **Имя хранилища** — в нашем примере это **ContosoKeyVault**. Имя Key Vault будет использоваться во всех командах для этого Key Vault.
* **URI хранилища** — в нашем примере это https://<YourKeyVaultName>.vault.azure.net/. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

>[!IMPORTANT]
> Параметр имени vault_name должен содержать уникальное значение, состоящее из букв и цифр, и иметь длину от 3 до 24 знаков. В противном случае отобразится сообщение об ошибке: Parameter 'vault_name' must conform to the following pattern: '^[a-zA-Z0-9-]{3,24}$' (Параметр vault_name должен соответствовать следующему шаблону: '^[a-zA-Z0-9-]{3,24}$').

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в Key Vault

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Вы может хранить здесь строки подключения SQL и прочие сведения, которые должны храниться безопасно, но быть доступны для приложения. В этом руководстве мы сохраним пароль с именем **AppSecret** и значением **MySecret**.

Введите указанные ниже команды, чтобы создать в Key Vault секрет с именем **AppSecret** и значением **MySecret**.

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

Эта команда отображает секретные сведения, включая URI. Завершив эти действия, вы сохраните в Azure Key Vault секрет со значением URI-адреса. Запишите эти сведения. Они потребуются вам на следующих шагах.

## <a name="create-a-web-app"></a>Создание веб-приложения

В этом разделе вы создадите приложение ASP.NET MVC и развернете его в Azure в качестве веб-приложения. Дополнительные сведения о веб-приложениях Azure см. в статье [Обзор веб-приложений](../app-service/app-service-web-overview.md).

1. Создайте проект в Visual Studio, последовательно выбрав пункты **Файл > Создать > Проект**. 

2. В диалоговом окне **Новый проект** последовательно выберите пункты **Visual C# > Веб > Веб-приложение ASP.NET Core**.

3. Присвойте приложению имя **WebKeyVault** и выберите **ОК**.
   >[!IMPORTANT]
   > Здесь нужно использовать имя WebKeyVault, чтобы скопированный код правильно соответствовал пространству имен. Если вы присвоите узлу другое имя, измените соответствующим образом код приложения.

    ![Диалоговое окно "Новый проект ASP.NET"](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Вы можете развернуть в Azure веб-приложения ASP.NET Core любого типа. Для работы с этим руководством выберите шаблон **Веб-приложение** и выберите режим **Без аутентификации**.

    ![Диалоговое окно ASPNET с выбранным режимом "Без аутентификации"](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Нажмите кнопку **ОК**.

6. После создания проекта ASP.NET Core отобразится страница приветствия ASP.NET Core с множеством ссылок на ресурсы, которые помогут вам приступить к работе.

7. В меню последовательно выберите пункты **Отладка > Запуск без отладки**, чтобы запустить веб-приложение локально.

## <a name="modify-the-web-app"></a>Изменение веб-приложения

Для вашего веб-приложения нужно установить два пакета NuGet. Чтобы установить их, сделайте следующее:

1. В обозревателе решений щелкните правой кнопкой мыши имя веб-сайта.
2. Выберите **Управление пакетами NuGet для решения**.
3. Установите флажок рядом с полем поиска. **Включить предварительные выпуски**
4. Найдите два указанных ниже пакета NuGet и подтвердите их добавление в решение.

    * [Microsoft.Azure.Services.AppAuthentication (предварительная версия)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) позволяет легко получать маркеры доступа для аутентификации службы в службах Azure. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) содержит методы для взаимодействия с Key Vault.

5. С помощью обозревателя решений откройте `Program.cs` и замените все содержимое файла Program.cs приведенным ниже кодом. Вместо ```<YourKeyVaultName>``` укажите имя вашего хранилища ключей.

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
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
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Перейдите в обозревателе решений к разделу **Страницы** и откройте `About.cshtml`. Замените содержимое файла **About.cshtml.cs** приведенным ниже кодом.

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. В главном меню выберите элементы **Отладка** > **Запуск без отладки**. Когда откроется браузер, перейдите в нем к странице **About** (Сведения). Отображается значение AppSecret.

>[!IMPORTANT]
> Если вы получите сообщение с ошибкой HTTP 502.5 — Process Failure (Сбой процесса), проверьте имя Key Vault в параметре `Program.cs`.

## <a name="publish-the-web-application-to-azure"></a>Публикация веб-приложения в Azure

1. Выберите **WebKeyVault** над областью редактора.
2. Щелкните **Опубликовать**.
3. Еще раз щелкните **Опубликовать**.
4. Теперь щелкните **Создать**.

>[!IMPORTANT]
> Откроется окно браузера с сообщением HTTP 502.5 — Process Failure (Сбой процесса). Это ожидаемое поведение. Чтобы просматривать секреты из Key Vault, нужно предоставить права удостоверению приложения.

## <a name="enable-managed-service-identity"></a>Включение удостоверения управляемой службы

Azure Key Vault позволяет обеспечить безопасное хранение учетных данных, а также других ключей и секретов, но для их получения код должен выполнять аутентификацию в Key Vault. Управляемое удостоверение службы (MSI) упрощает решение этой задачи, предоставляя службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

1. Вернитесь в окно командной строки Azure.
2. Выполните команду assign-identity, чтобы создать удостоверение для этого приложения.

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>Эта команда выполняет те же действия, что и включение параметра **Удостоверение управляемой службы** в свойствах веб-приложения на портале.

## <a name="grant-rights-to-the-application-identity"></a>Предоставление прав удостоверению приложения

На портале Azure перейдите в раздел политик доступа к Key Vault и предоставьте себе права на управление секретами в Key Vault. Это позволит вам выполнять приложение на локальном компьютере разработки.

1. Найдите используемое Key Vault в диалоговом окне **Поиск ресурсов** на портале Azure.
2. Выберите **Политики доступа**.
3. Выберите **Добавить новую**, затем в разделе **Разрешения секретов** выберите **Получение** и **Список**.
4. Щелкните **Выбор субъекта** и добавьте удостоверение вашего приложения. Имя удостоверения совпадает с именем приложения.
5. Щелкните **ОК**.

Теперь ваша учетная запись Azure и удостоверение приложения получат права на чтение данных из Key Vault. Обновив страницу с ошибкой, вы теперь увидите целевую страницу сайта. Выберите элемент **About** (Сведения). Здесь вы видите значение, которое хранится в Key Vault.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов и все ее ресурсы, используйте команду **az group delete**.

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство разработчика хранилища ключей Azure](key-vault-developers-guide.md)

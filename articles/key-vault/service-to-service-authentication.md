---
title: Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET
description: Использование библиотеки Microsoft.Azure.Services.AppAuthentication для выполнения проверки подлинности в Azure Key Vault с помощью .NET.
keywords: локальные учетные данные проверки подлинности azure key vault
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: db0b0ca72f41c68e19db6635d9ba0e9144183204
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31417581"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET

Чтобы выполнить проверку подлинности в Azure Key Vault, необходимо иметь учетные данные Azure Active Directory: в виде общего секрета или сертификата. Управление такими учетными данными может быть сложным. Очевидным решением может казаться объединить учетные данные в приложение, включив их в исходные данные или файлы конфигурации.

Пакет `Microsoft.Azure.Services.AppAuthentication` для библиотеки .NET упрощает эту проблему. Он использует учетные данные разработчика для выполнения проверки подлинности во время локальной разработки. Если решение позже развертывается в Azure, библиотека автоматически переключается на использование учетных данных приложения.  

Использовать учетные данные разработчика во время локального развертывания более безопасно, так как вам не нужно создавать учетные данные Azure AD или совместно использовать учетные данные с другими разработчиками.

Библиотека `Microsoft.Azure.Services.AppAuthentication` автоматически управляет проверкой подлинности, которая, в свою очередь, позволяет сконцентрироваться на решении, а не на учетных данных.

Библиотека `Microsoft.Azure.Services.AppAuthentication` поддерживает локальную разработку с Microsoft Visual Studio, Azure CLI и встроенную проверку подлинности Azure AD. При развертывании в службах приложений Azure или в виртуальных машинах Azure библиотека автоматически использует [управляемое удостоверение службы](/azure/active-directory/msi-overview) (MSI). Изменение кода или конфигурации не требуется. Библиотека также поддерживает непосредственное использование [учетных данных клиента](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) Azure AD, если MSI недоступно или когда контекст безопасности разработчика не удается определить во время локальной разработки.

<a name="asal"></a>
## <a name="using-the-library"></a>Использование библиотеки

Для приложений .NET для работы с управляемым удостоверением службы проще всего использовать пакет `Microsoft.Azure.Services.AppAuthentication`. Узнайте, как начать работу.

1. Добавьте ссылку на пакет NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) в приложение.

2. Добавьте следующий код:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

Класс `AzureServiceTokenProvider` кэширует токен в памяти и извлекает его из Azure AD прямо перед истечением срока действия. Следовательно, вам больше не нужно проверять срок действия перед вызовом метода `GetAccessTokenAsync`. Просто вызовите метод, когда необходимо использовать токен. 

Для метода `GetAccessTokenAsync` требуется наличие идентификатора ресурса. Дополнительные сведения см. в разделе [Какие службы Azure поддерживают управляемое удостоверение службы?](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity)


<a name="samples"></a>
## <a name="samples"></a>Примеры

В следующих примерах показана библиотека `Microsoft.Azure.Services.AppAuthentication` в действии:

1. [Использование управляемого удостоверения службы для извлечения секрета из Azure Key Vault во время выполнения](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

2. [Развертывание программным способом шаблона Azure Resource Manager из виртуальной машины Azure с помощью MSI](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Использование примера .NET Core и MSI для вызова служб Azure из виртуальной машины Azure под управлением Linux](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Проверка подлинности среды локальной разработки

Для локальной разработки существует два основных сценария проверки подлинности:

- [проверка подлинности в службах Azure](#authenticating-to-azure-services);
- [проверка подлинности в пользовательских службах](#authenticating-to-custom-services).

Здесь вы ознакомитесь с требованиями к каждому сценарию и поддерживаемыми средствами.


### <a name="authenticating-to-azure-services"></a>Проверка подлинности в службах Azure

Локальные компьютеры не поддерживают управляемое удостоверение службы.  В результате библиотека `Microsoft.Azure.Services.AppAuthentication` использует учетные данные разработчика для запуска в среде локальной разработки. При развертывании решения в Azure библиотека использует MSI для переключения на поток предоставления учетных данных клиента OAuth 2.0.  Это значит, что вы можете без проблем тестировать один и тот же код локально и удаленно.

Для локальной разработки `AzureServiceTokenProvider` получает токены с помощью **Visual Studio**, **интерфейса командной строки Azure** (CLI) или **встроенной проверки подлинности Azure AD**. Каждый вариант применяется последовательно, и библиотека использует первый вариант, который завершается успешно. Если ни один вариант не работает, выводится исключение `AzureServiceTokenProviderException` с подробными сведениями.

### <a name="authenticating-with-visual-studio"></a>Проверка подлинности с помощью Visual Studio

Для использования Visual Studio убедитесь в следующем:

1. У вас установлена служба [Visual Studio 2017 версии 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) или более поздней.

2. [Расширение проверки подлинности приложения для Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) установлено.
 
3. Вы вошли в Visual Studio и выбрали учетную запись для локальной разработки. Чтобы выбрать учетную запись локальной разработки, щелкните **Сервис**&nbsp;>&nbsp;**Параметры**&nbsp;>&nbsp;**Azure Service Authentication** (Проверка подлинности в службе Azure). 

Если возникли проблемы при использовании Visual Studio, например ошибки с файлом поставщика токенов, внимательно просмотрите указанные ниже шаги. 

Вам также может потребоваться повторно выполнить проверку подлинности токена разработчика.  Для этого выберите **Сервис**&nbsp;>&nbsp;**Параметры**>**Azure&nbsp;Service&nbsp;Authentication** (Проверка подлинности в службе Azure) и найдите ссылку **Re-authenticate** (Повторно выполнить проверку подлинности) в выбранной учетной записи.  Щелкните ее, чтобы выполнить проверку подлинности. 

### <a name="authenticating-with-azure-cli"></a>Проверка подлинности с помощью Azure CLI

Чтобы использовать Azure CLI для локальной разработки, сделайте следующее:

1. Установите [Azure CLI версии 2.0.12](/cli/azure/install-azure-cli) или более поздней. Обновите более ранние версии. 

2. Выполните команду **az login** для входа в Azure.

Для проверки доступа выполните команду `az account get-access-token`.  Если появится ошибка, убедитесь в успешном завершении шага 1. 

Если Azure CLI не установлен в каталоге по умолчанию, вы можете получить ошибку, что `AzureServiceTokenProvider` не может найти путь для Azure CLI.  С помощью переменной среды **AzureCLIPath** можно определить папку установки Azure CLI. При необходимости `AzureServiceTokenProvider` добавляет каталог, указанный в переменной среды **AzureCLIPath**, в переменную среды **Path**.

Если вы вошли в Azure CLI с помощью нескольких учетных записей или учетная запись имеет доступ к нескольким подпискам, необходимо указать конкретную подписку для использования.  Для этого выполните следующую команду:

```
az account set --subscription <subscription-id>
```

Эта команда создает выходные данные только при сбое.  Чтобы проверить параметры текущей учетной записи, выполните следующую команду:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Использование встроенной проверки подлинности Azure AD

Чтобы использовать проверку подлинности Azure AD, убедитесь, что:

- локальная служба Active Directory [синхронизируется с Azure AD](/azure/active-directory/connect/active-directory-aadconnect);

- код выполняется на компьютере, присоединенном к домену.


### <a name="authenticating-to-custom-services"></a>Проверка подлинности в пользовательских службах

Когда служба вызывает службы Azure, можно выполнить предыдущие действия, так как службы Azure предоставляют доступ пользователям и приложениям.  

При создании службы, которая вызывает пользовательскую службу, выполните проверку подлинности среды локальной разработки с помощью учетных данных клиента Azure AD.  Существуют два варианта: 

1.  Войдите в Azure, используя субъект-службу:

    1.  [Создайте субъект-службу](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Выполните вход с помощью Azure CLI:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Так как субъект-служба может не иметь доступа к подписке, используйте аргумент `--allow-no-subscriptions`.

2.  Укажите сведения о субъекте-службе с помощью переменных среды.  Дополнительные сведения см. в разделе [Запуск приложения с помощью переменной среды](#running-the-application-using-a-service-principal).

После входа в Azure `AzureServiceTokenProvider` использует субъект-службу, чтобы извлечь токен для локальной разработки.

Это относится только к локальной разработке. При развертывании решения в Azure библиотека переключается на выполнение проверки подлинности MSI.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Запуск приложения с помощью управляемого удостоверения службы 

При выполнении кода в службе приложений Azure или на виртуальной машине Azure с включенным MSI библиотека автоматически использует управляемое удостоверение службы. Изменения кода не требуются. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Запуск приложения с помощью субъекта-службы 

Возможно, может понадобиться создать учетные данные клиента Azure AD для проверки подлинности. Ниже приведены распространенные примеры.

1. Ваш код выполняется в среде локальной разработки, но не использует удостоверение разработчика.  Service Fabric, например, использует [учетную запись NetworkService](/azure/service-fabric/service-fabric-application-secret-management) для локальной разработки.
 
2. Ваш код выполняется в среде локальной разработки и выполняется проверка подлинности в пользовательской службе, поэтому вы не можете использовать удостоверение разработчика. 
 
3. Ваш код выполняется в вычислительном ресурсе Azure, который еще не поддерживает управляемое удостоверение службы, например в пакетной службе Azure.

Чтобы войти в Azure AD с помощью сертификата, сделайте следующее:

1. Создайте [сертификат субъекта-службы](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Разверните сертификат в хранилище _LocalMachine_ или _CurrentUser_. 

3. Задайте переменную среды с именем **AzureServicesAuthConnectionString**, как показано в следующем примере:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Замените _{AppId}_, _{TenantId}_ и _{Thumbprint}_ идентификатором приложения, идентификатором клиента и отпечатком, созданными на шаге 1.

    Свойство **CertificateStoreLocation** должно иметь значение _CurrentUser_ или _LocalMachine_ в зависимости от плана развертывания.

4. Запустите приложение. 

Чтобы выполнить вход с помощью учетных данных в виде общего секрета Azure AD, сделайте следующее:

1. Создайте [субъект-службу с паролем](/azure/azure-resource-manager/resource-group-authenticate-service-principal) и предоставьте ей доступ к Key Vault. 

2. Задайте переменную среды с именем **AzureServicesAuthConnectionString**, как показано в следующем примере:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Замените _{AppId}_, _{TenantId}_ и _{ClientSecret}_ идентификатором приложения, идентификатором клиента и секретом клиента, созданными на шаге 1.

3. Запустите приложение. 

Если установка выполнена правильно, дальнейшие изменения в коде не требуются.  `AzureServiceTokenProvider` использует переменную среды и сертификат для выполнения проверки подлинности в Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Поддержка строки подключения

По умолчанию `AzureServiceTokenProvider` использует несколько способов извлечения токена. 

Для управления процессом используйте строку подключения, переданную в конструктор `AzureServiceTokenProvider` или указанную в переменной среды *AzureServicesAuthConnectionString*. 

Поддерживаются следующие варианты.

| Вариант&nbsp;строки&nbsp;подключения | Сценарий | Комментарии|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Локальная разработка | AzureServiceTokenProvider использует Azure CLI для получения токена. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Локальная разработка | AzureServiceTokenProvider использует Visual Studio для получения токена. |
| `RunAs=CurrentUser;` | Локальная разработка | AzureServiceTokenProvider использует встроенную проверку подлинности Azure AD для получения токена. |
| `RunAs=App;` | Удостоверение управляемой службы | AzureServiceTokenProvider использует управляемое удостоверение службы для получения токена. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Субъект-служба |`AzureServiceTokenProvider` использует секрет для получения токена из Azure AD. |


## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше об [управляемых удостоверениях службы](/azure/app-service/app-service-managed-service-identity).

- Узнайте о различных способах [выполнения проверки подлинности и авторизации приложений](/azure/app-service/app-service-authentication-overview).

- Узнайте больше о [сценариях проверки подлинности](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application) Azure AD.
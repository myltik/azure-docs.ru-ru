---
title: Использование хранилища ключей Azure из веб-приложения | Документация Майкрософт
description: В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения.
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: adhurwit
ms.openlocfilehash: de8276b22377db37075c6793d0710bb18625446c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Использование хранилища ключей Azure из веб-приложения

## <a name="introduction"></a>Введение

В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения Azure. В нем рассматривается процесс доступа к секрету в хранилище ключей Azure для его использования в веб-приложении.

**Предполагаемое время выполнения:** 15 минут.

Общие сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником требуется:

* URI для секрета кода в хранилище ключей Azure
* Идентификатор клиента и секрет клиента для веб-приложения, зарегистрированного в Azure Active Directory, которое имеет доступ к вашему хранилищу ключей
* Веб-приложение. Мы покажем действия для приложения ASP.NET MVC, развернутого в Azure в качестве веб-приложения.

>[!IMPORTANT]
>* В этом примере показан старый способ подготовки удостоверений AAD вручную. Сейчас доступна предварительная версия новой возможности [Управляемое удостоверение службы (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), которая автоматически подготавливает удостоверения AAD. Дополнительные сведения см. в примерах в репозитории [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/).

> [!NOTE]
>* Необходимо выполнить действия, описанные в разделе [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md) , чтобы получить URI секрета, идентификатор клиента и секрет клиента для веб-приложения.


Доступ к хранилищу ключей будет получать веб-приложение, зарегистрированное в Azure Active Directory и получившее права доступа к хранилищу ключей. Если это не так, вернитесь к разделу "Регистрация приложения" в учебнике "Приступая к работе" и повторите перечисленные шаги.

Этот учебник поможет веб-разработчикам понять принципы создания веб-приложений в Azure. Дополнительные сведения о веб-приложениях Azure см. в статье [Обзор веб-приложений](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Добавление пакетов NuGet

Существует два пакета, которые необходимо установить для веб-приложения.

* Библиотека проверки подлинности Active Directory содержит методы для взаимодействия с Azure Active Directory и управления удостоверениями пользователей.
* Библиотека хранилища ключей Azure содержит методы для взаимодействия с хранилищем ключей Azure.

Оба пакета можно установить с помощью консоли диспетчера пакетов, используя команду Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Изменение файла Web.Config

Существует три параметра приложения, которые необходимо добавить в файл web.config следующим образом.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Если вы не собираетесь размещать приложения как веб-приложения Azure, добавьте фактические значения идентификатора клиента, секрета клиента и URI секрета в файл Web.config. В противном случае оставьте значения-заполнители, так как мы добавим фактические значения на портале Azure, чтобы получить дополнительный уровень безопасности.

## <a id="gettoken"></a>Добавление метода для получения маркера доступа

Для использования API хранилища ключей требуется маркер доступа. Клиент хранилища ключей обрабатывает вызовы API хранилища ключей, но вам необходимо предоставить ему функцию, которая возвращает маркер доступа.  

Ниже приведен код для получения маркера доступа из Azure Active Directory. Этот код можно расположить в любом месте приложения. Я добавлю его в класс Utils или EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

> [!NOTE]
>* Новая функция управляемого удостоверения службы (MSI) сейчас является самым простым способом аутентификации. Дополнительные сведения см. по ссылкам на пример использования [Key Vault с MSI в приложении .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) и связанное [руководство по использованию MSI в службе приложений и решении "Функции Azure"](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 
>* Использование идентификатора клиента и секрета клиента — это еще один способ выполнить аутентификацию для приложения Azure AD. Его использование в веб-приложении обеспечивает разделение обязанностей и больший контроль над вашим управлением ключами. Но ему требуется размещение секрета клиента в параметрах конфигурации, что в некоторых ситуациях может быть так же рискованно, как размещение секрета, который необходимо защитить, в параметрах конфигурации. Ниже приведены сведения о том, как использовать идентификатор клиента и сертификат вместо идентификатора и секрета клиента для аутентификации приложения Azure AD.

## <a id="appstart"></a>Получение секрета при запуске приложения

Теперь нам требуется, чтобы код вызвал API хранилища ключей и получил секрет. Следующий фрагмент кода можно поместить в любом месте, при условии что он вызывается перед тем, как его необходимо использовать. Я поместил код в событии запуска приложения в файле Global.asax, чтобы он выполнялся один раз при запуске, а секрет становился доступным для приложения.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Добавление параметров приложения на портале Azure (необязательно)

Если у вас есть веб-приложение Azure, то вы можете добавить фактические значения параметров приложений на портале Azure. При этом фактические значения не будут размещены в файле web.config, а будут защищены с помощью портала, где предоставляются отдельные возможности управления доступом. Эти значения будут заменены на значения, введенные в файле web.config. Убедитесь, что имена совпадают.

![Параметры приложения на портале Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Проверка подлинности с помощью сертификата вместо секрета клиента

Еще один способ проверки подлинности приложения Azure AD состоит в использовании идентификатора клиента и сертификата вместо идентификатора и секрета клиента. Ниже приведены действия по использованию сертификата в веб-приложении Azure.

1. Получите или создайте сертификат
2. Свяжите сертификат с приложением Azure AD
3. Добавьте код в веб-приложение, чтобы использовать сертификат
4. Добавьте сертификат в веб-приложение

### <a name="get-or-create-a-certificate"></a>Получите или создайте сертификат

Для наших целей будет создан тестовый сертификат. Вот несколько команд, которые можно использовать в командной строке разработчика для создания сертификата. Измените каталог, в котором будут созданы файлы сертификата.  Кроме того, для начальной и конечной даты сертификата используется текущая дата плюс один год.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Запишите дату окончания и пароль для PFX-файла (в этом примере: 07/31/2018 и test123). Они вам потребуются позднее.

Дополнительные сведения о создании тестового сертификата см. в [этом практическом руководстве](https://msdn.microsoft.com/library/ff699202.aspx).

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Свяжите сертификат с приложением Azure AD

Теперь, когда у вас есть сертификат, необходимо связать его с приложением Azure AD. Сейчас портал Azure не поддерживает этот рабочий процесс. Однако это можно сделать с помощью PowerShell. Чтобы связать сертификат с приложением Azure AD, выполните следующие команды.

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

После выполнения этих команд приложение можно будет увидеть в Azure AD. Во время поиска в диалогом окне поиска выберите "Приложения, которыми владеет моя компания", а не "Приложения, которые использует моя компания".

Дополнительные сведения об объектах приложения Azure AD и ServicePrincipal см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory (Azure AD)](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Добавьте код в веб-приложение, чтобы использовать сертификат

Теперь в веб-приложение будет добавлен код для доступа к сертификату и его использования для аутентификации.

Во-первых, есть код для доступа к сертификату.

```cs
public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```

Обратите внимание, что StoreLocation является CurrentUser вместо LocalMachine и что мы указали false для метода Find, поскольку мы используем тестовый сертификат.

Далее следует код, который с помощью CertificateHelper и создает ClientAssertionCertificate, необходимый для проверки подлинности.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Здесь представлен новый код для получения токена доступа. Этот параметр заменяет метод GetToken из предыдущего примера. Для удобства я задал другое имя.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

Я поместил весь этот код в класс Utils проекта веб-приложения для удобства использования.

Последнее изменение кода находится в методе Application_Start. Сначала необходимо вызвать метод GetCert() для загрузки ClientAssertionCertificate. А затем мы изменяем метод обратного вызова, который использовался при создании нового KeyVaultClient. Обратите внимание, что этот параметр заменяет код из предыдущего примера.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Добавление сертификата в веб-приложение на портале Azure

Добавление сертификата в веб-приложение — это простой двухэтапный процесс. Сначала войдите на портал Azure и перейдите к своему веб-приложению. В колонке "Параметры" веб-приложения щелкните запись "Пользовательские домены и SSL". В открывшейся колонке вы сможете загрузить сертификат KVWebApp.pfx, созданный в предыдущем примере. Убедитесь, что вы помните пароль для PFX.

![Добавление сертификата в веб-приложение на портале Azure][2]

Последнее, что требуется выполнить, — добавить в веб-приложение параметр с именем WEBSITE\_LOAD\_CERTIFICATES и значением "*". Это позволит гарантировать загрузку всех сертификатов. Если требуется загрузить только переданные сертификаты, можно ввести разделенный запятыми список их отпечатков.

Дополнительные сведения о добавлении сертификата в веб-приложение см. в статье [Использование сертификатов в приложениях веб-сайтов Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/).

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Добавление сертификата в хранилище ключей в качестве секрета

Вместо отправки сертификата непосредственно в службу веб-приложения вы можете сохранить его в хранилище ключей в секрете, а затем развернуть его оттуда. Этот двухэтапный процесс описан в записи блога, посвященной [развертыванию сертификата веб-приложения Azure из хранилища ключей](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Дальнейшие действия

Справочные материалы по программированию см. в [справочнике по API клиента C# для хранилища ключей Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png

---
title: "Использование хранилища ключей Azure из веб-приложения | Документация Майкрософт"
description: "В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения."
services: key-vault
documentationcenter: 
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: adhurwit
translationtype: Human Translation
ms.sourcegitcommit: f7589fa62dcfedc6f99439f453a40f999ff8d845
ms.openlocfilehash: 1c94e442576d28a6e40bcc3a0720ed31db722af5


---
# <a name="use-azure-key-vault-from-a-web-application"></a>Использование хранилища ключей Azure из веб-приложения
## <a name="introduction"></a>Введение
В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения Azure. В нем рассматривается процесс доступа к секрету в хранилище ключей Azure для его использования в веб-приложении.

**Предполагаемое время выполнения:** 15 минут.

Общие сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим учебником требуется:

* URI для секрета кода в хранилище ключей Azure
* Идентификатор клиента и секрет клиента для веб-приложения, зарегистрированного в Azure Active Directory, которое имеет доступ к вашему хранилищу ключей
* Веб-приложение. Мы покажем действия для приложения ASP.NET MVC, развернутого в Azure в качестве веб-приложения.

> [!NOTE]
> Необходимо выполнить действия, описанные в разделе [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md) , чтобы получить URI секрета, идентификатор клиента и секрет клиента для веб-приложения.
> 
> 

Доступ к хранилищу ключей будет получать веб-приложение, зарегистрированное в Azure Active Directory и получившее права доступа к хранилищу ключей. Если это не так, вернитесь к разделу "Регистрация приложения" в учебнике "Приступая к работе" и повторите перечисленные шаги.

Этот учебник поможет веб-разработчикам понять принципы создания веб-приложений в Azure. Дополнительные сведения о веб-приложениях Azure см. в статье [Обзор веб-приложений](../app-service-web/app-service-web-overview.md).

## <a name="a-idpackagesaadd-nuget-packages"></a><a id="packages"></a>Добавление пакетов NuGet
Существует два пакета, которые необходимо установить для веб-приложения.

* Библиотека проверки подлинности Active Directory содержит методы для взаимодействия с Azure Active Directory и управления удостоверениями пользователей.
* Библиотека хранилища ключей Azure содержит методы для взаимодействия с хранилищем ключей Azure.

Оба пакета можно установить с помощью консоли диспетчера пакетов, используя команду Install-Package.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a name="a-idwebconfigamodify-webconfig"></a><a id="webconfig"></a>Изменение файла Web.Config
Существует три параметра приложения, которые необходимо добавить в файл web.config следующим образом.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Если вы не собираетесь размещать приложения как веб-приложения Azure, добавьте фактические значения идентификатора клиента, секрета клиента и URI секрета в файл Web.config. В противном случае оставьте значения-заполнители, так как мы добавим фактические значения на портале Azure, чтобы получить дополнительный уровень безопасности.

## <a name="a-idgettokenaadd-method-to-get-an-access-token"></a><a id="gettoken"></a>Добавление метода для получения маркера доступа
Для использования API хранилища ключей требуется маркер доступа. Клиент хранилища ключей обрабатывает вызовы API хранилища ключей, но вам необходимо предоставить ему функцию, которая возвращает маркер доступа.  

Ниже приведен код для получения маркера доступа из Azure Active Directory. Этот код можно расположить в любом месте приложения. Я добавлю его в класс Utils или EncryptionHelper.  

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

> [!NOTE]
> Использование идентификатора клиента и секрета клиента — это самый простой способ выполнить проверку подлинности для приложения Azure AD. Его использование в веб-приложении обеспечивает разделение обязанностей и больший контроль над вашим управлением ключами. Но ему требуется размещение секрета клиента в параметрах конфигурации, что в некоторых ситуациях может быть так же рискованно, как размещение секрета, который необходимо защитить, в параметрах конфигурации. Ниже приведены сведения о том, как использовать идентификатор клиента и сертификат вместо идентификатора и секрета клиента для проверки подлинности приложения Azure AD.
> 
> 

## <a name="a-idappstartaretrieve-the-secret-on-application-start"></a><a id="appstart"></a>Получение секрета при запуске приложения
Теперь нам требуется, чтобы код вызвал API хранилища ключей и получил секрет. Следующий фрагмент кода можно поместить в любом месте, при условии что он вызывается перед тем, как его необходимо использовать. Я поместил код в событии запуска приложения в файле Global.asax, чтобы он выполнялся один раз при запуске, а секрет становился доступным для приложения.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec.Value;



## <a name="a-idportalsettingsaadd-app-settings-in-the-azure-portal-optional"></a><a id="portalsettings"></a>Добавление параметров приложения на портале Azure (необязательно)
Если у вас есть веб-приложение Azure, теперь вы можете добавить фактические значения параметров приложений на портале Azure. При этом фактические значения не будут размещены в файле web.config, а будут защищены с помощью портала, где предоставляются отдельные возможности управления доступом. Эти значения будут заменены на значения, введенные в файле web.config. Убедитесь, что имена совпадают.

![Параметры приложения на портале Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Проверка подлинности с помощью сертификата вместо секрета клиента
Еще один способ проверки подлинности приложения Azure AD состоит в использовании идентификатора клиента и сертификата вместо идентификатора и секрета клиента. Ниже приведены действия по использованию сертификата в веб-приложении Azure.

1. Получите или создайте сертификат
2. Свяжите сертификат с приложением Azure AD
3. Добавьте код в веб-приложение, чтобы использовать сертификат
4. Добавьте сертификат в веб-приложение

**Получение или создание сертификата** Для наших целей будет создан тестовый сертификат. Вот несколько команд, которые можно использовать в командной строке разработчика для создания сертификата. Задайте каталог, в котором будут созданы файлы сертификата.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Запишите дату окончания и пароль для PFX-файла (в этом примере: 07/31/2016 и test123). Они вам потребуются позднее.

Дополнительные сведения о создании тестового сертификата см. в [этом практическом руководстве](https://msdn.microsoft.com/library/ff699202.aspx).

**Свяжите сертификат с приложением Azure AD** Теперь, когда у вас есть сертификат, необходимо связать его с приложением Azure AD. В настоящее время портал Azure не поддерживает этот рабочий процесс. Однако это можно сделать с помощью PowerShell. Чтобы связать сертификат с приложением Azure AD, выполните следующие команды:

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $x509.Import("C:\data\KVWebApp.cer")
    $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())
    $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    $x509.Thumbprint

После выполнения этих команд приложение можно будет увидеть в Azure AD. Во время поиска в диалогом окне поиска выберите "Приложения, которыми владеет моя компания", а не "Приложения, которые использует моя компания".

Дополнительные сведения об объектах приложения Azure AD и ServicePrincipal см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/active-directory-application-objects.md).

**Добавление кода для веб-приложения, чтобы использовать сертификат** Теперь в веб-приложение будет добавлен код для доступа к сертификату и его использования для проверки подлинности.

Во-первых, есть код для доступа к сертификату.

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


Обратите внимание, что StoreLocation является CurrentUser вместо LocalMachine и что мы указали false для метода Find, поскольку мы используем тестовый сертификат.

Далее следует код, который с помощью CertificateHelper и создает ClientAssertionCertificate, необходимый для проверки подлинности.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Здесь представлен новый код для получения токена доступа. Этот параметр заменяет метод GetToken выше. Для удобства я задал другое имя.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

Я поместил весь этот код в класс Utils проекта веб-приложения для удобства использования.

Последнее изменение кода находится в методе Application_Start. Сначала необходимо вызвать метод GetCert() для загрузки ClientAssertionCertificate. А затем мы изменяем метод обратного вызова, который использовался при создании нового KeyVaultClient. Обратите внимание, что этот параметр заменяет имевшийся ранее код.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Добавление сертификата в веб-приложение на портале Azure** Добавление сертификата в веб-приложение — это простой двухэтапный процесс. Сначала войдите на портал управления Azure и перейдите к своему веб-приложению. В колонке "Параметры" веб-приложения щелкните запись "Пользовательские домены и SSL". В открывшейся колонке вы сможете загрузить ранее созданный сертификат KVWebApp.pfx. Убедитесь, что вы помните пароль для PFX.

![Добавление сертификата в веб-приложение на портале Azure][2]

Последнее, что требуется выполнить, — добавить в веб-приложение параметр с именем WEBSITE\_LOAD\_CERTIFICATES и значением "*". Это позволит гарантировать загрузку всех сертификатов. Если требуется загрузить только переданные сертификаты, можно ввести разделенный запятыми список их отпечатков.

Дополнительные сведения о добавлении сертификата в веб-приложение см. в статье [Использование сертификатов в приложениях веб-сайтов Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/).

**Добавление сертификата в хранилище ключей в качестве секрета** Вместо отправки сертификата непосредственно в службу веб-приложения вы можете сохранить его в хранилище ключей в секрете, а затем развернуть его оттуда. Этот двухэтапный процесс описан в записи блога, посвященной [развертыванию сертификата веб-приложения Azure из хранилища ключей](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a name="a-idnextanext-steps"></a><a id="next"></a>Дальнейшие действия
Справочные материалы по программированию см. в [справочнике по API клиента C# для хранилища ключей Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png



<!--HONumber=Jan17_HO2-->



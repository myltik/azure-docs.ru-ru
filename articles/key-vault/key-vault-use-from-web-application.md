<properties pageTitle="Use Azure Key Vault from a Web Application | Microsoft Azure" description="Use this tutorial to help you learn how to use Azure Key Vault from a web application." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="adhurwit"/>

# Использование хранилища ключей Azure из веб-приложения #

## Введение  
В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения Azure. В нем рассматривается процесс доступа к секрету в хранилище ключей Azure для его использования в веб-приложении.

**Предполагаемое время выполнения:** 15 минут.


Общую информацию о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)

## Предварительные требования 

Для работы с этим учебником требуется:

- URI для секрета кода в хранилище ключей Azure
- Идентификатор клиента и секрет клиента для веб-приложения, зарегистрированного в Azure Active Directory, которое имеет доступ к вашему хранилищу ключей
- Веб-приложение. Мы покажем действия для приложения ASP.NET MVC, развернутого в Azure в качестве веб-приложения. 

> [AZURE.NOTE]Необходимо выполнить действия, описанные в разделе [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md), чтобы получить URI секрета, идентификатор клиента и секрет клиента для веб-приложения.

Доступ к хранилищу ключей будет получать веб-приложение, зарегистрированное в Azure Active Directory и получившее права доступа к хранилищу ключей. Если это не так, вернитесь к разделу "Регистрация приложения" в учебнике "Приступая к работе" и повторите перечисленные шаги.

Этот учебник поможет веб-разработчикам понять принципы создания веб-приложений в Azure. Дополнительные сведения о веб-приложениях Azure см. в разделе [Обзор веб-приложений](../app-service-web-overview.md).



## <a id="packages"></a>Добавление пакетов NuGet ##
Существует два пакета, которые необходимо установить для веб-приложения.

- Библиотека проверки подлинности Active Directory содержит методы для взаимодействия с Azure Active Directory и управления удостоверениями пользователей.
- Библиотека хранилища ключей Azure содержит методы для взаимодействия с хранилищем ключей Azure.


Оба пакета можно установить с помощью консоли диспетчера пакетов, используя команду Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault 


## <a id="webconfig"></a>Изменение файла Web.Config ##
Существует три параметра приложения, которые необходимо добавить в файл web.config следующим образом.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Если вы не собираетесь размещать приложения как веб-приложения Azure, добавьте фактические значения идентификатора клиента, секрета клиента и URI секрета в файл Web.config. В противном случае оставьте значения-заполнители, так как мы добавим фактические значения на портале Azure, чтобы получить дополнительный уровень безопасности.


## <a id="gettoken"></a>Добавление метода для получения маркера доступа ##
Для использования API хранилища ключей требуется маркер доступа. Клиент хранилища ключей обрабатывает вызовы API хранилища ключей, но вам необходимо предоставить ему функцию, которая возвращает маркер доступа.

Ниже приведен код для получения маркера доступа из Azure Active Directory. Этот код можно расположить в любом месте приложения. Я добавлю его в класс Utils или EncryptionHelper.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

> [AZURE.NOTE]Использование секрета клиента с помощью идентификатора клиента и секрета клиента является самым простым способом проверки подлинности приложения Azure AD. Его использование в веб-приложении обеспечивает разделение обязанностей и больший контроль над вашим управлением ключами. Но ему требуется размещение секрета клиента в параметрах конфигурации, что в некоторых ситуациях может быть так же рискованно, как размещение секрета, который необходимо защитить, в параметрах конфигурации. Ниже приведены сведения о том, как использовать идентификатор клиента и сертификат вместо идентификатора и секрета клиента для проверки подлинности приложения Azure AD.



## <a id="appstart"></a>Получение секрета при запуске приложения ##
Теперь нам требуется, чтобы код вызвал API хранилища ключей и получил секрет. Следующий фрагмент кода можно поместить в любом месте, при условии что он вызывается перед тем, как его необходимо использовать. Я поместил код в событии запуска приложения в файле Global.asax, чтобы он выполнялся один раз при запуске, а секрет становился доступным для приложения.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Добавление параметров приложения на портале Azure (необязательно) ##
Если у вас есть веб-приложение Azure, теперь вы можете добавить фактические значения параметров приложений на портале Azure. При этом фактические значения не будут размещены в файле web.config, а будут защищены с помощью портала, где предоставляются отдельные возможности управления доступом. Эти значения будут заменены на значения, введенные в файле web.config. Убедитесь, что имена совпадают.

![Параметры приложения на портале Azure][1]


## Проверка подлинности с помощью сертификата вместо секрета клиента 
Еще один способ проверки подлинности приложения Azure AD состоит в использовании идентификатора клиента и сертификата вместо идентификатора и секрета клиента. Ниже приведены действия по использованию сертификата в веб-приложении Azure.

1. Получите или создайте сертификат
2. Свяжите сертификат с приложением Azure AD
3. Добавьте код в веб-приложение, чтобы использовать сертификат
4. Добавьте сертификат в веб-приложение


**Получение или создание сертификата** Для наших целей будет создан тестовый сертификат. Вот несколько команд, которые можно использовать в командной строке разработчика для создания сертификата. Задайте каталог, в котором будут созданы файлы сертификата.

	makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
	pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Запишите дату окончания и пароль для PFX-файла (в этом примере: 07/31/2016 и test123). Они вам потребуются позднее.

Дополнительные сведения о создании тестового сертификата см. в разделе [Практическое руководство по созданию собственного тестового сертификата](https://msdn.microsoft.com/library/ff699202.aspx).


**Свяжите сертификат с приложением Azure AD** Теперь, когда у вас есть сертификат, необходимо связать его с приложением Azure AD. Но в настоящее время портал управления Azure не поддерживает это. Вместо этого необходимо использовать Powershell. Ниже приведены команды, которые нужно выполнить.

	$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
	
	PS C:\> $x509.Import("C:\data\KVWebApp.cer")
	
	PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())
	
	PS C:\> $now = [System.DateTime]::Now
	
	# this is where the end date from the cert above is used
	PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31") 
	
	PS C:\> $adapp = New-AzureADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow
	
	PS C:\> $sp = New-AzureADServicePrincipal -ApplicationId $adapp.ApplicationId
	
	PS C:\> Set-AzureKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToKeys all -ResourceGroupName 'contosorg'
	
	# get the thumbprint to use in your app settings
	PS C:\>$x509.Thumbprint

После выполнения этих команд приложение можно будет увидеть в Azure AD. Если вы сначала не видите приложение, выполните поиск по запросу "Приложения, которыми владеет моя компания" вместо "Приложения, используемые моей компанией".

Дополнительные сведения об объектах приложения Azure AD и ServicePrincipal см. в разделе [Объекты приложений и объекты участников-служб](../active-directory/active-directory-application-objects.md)



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

Последнее изменение кода находится в методе Application\_Start. Сначала необходимо вызвать метод GetCert() для загрузки ClientAssertionCertificate. А затем мы изменяем метод обратного вызова, который использовался при создании нового KeyVaultClient. Обратите внимание, что этот параметр заменяет имевшийся ранее код.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Добавление сертификата в веб-приложение** Добавление сертификата в веб-приложение представляет собой простой двухэтапный процесс. Сначала войдите на портал управления Azure и перейдите к своему веб-приложению. В колонке "Параметры" веб-приложения щелкните запись "Пользовательские домены и SSL". В открывшейся колонке вы сможете загрузить ранее созданный сертификат KVWebApp.pfx. Убедитесь, что вы помните пароль для PFX.

![Добавление сертификата в веб-приложение на портале Azure][2]


Последнее, что требуется выполнить, — добавить в веб-приложение параметр с именем WEBSITE\_LOAD\_CERTIFICATES и значением *. Это позволит гарантировать загрузку всех сертификатов. Если требуется загрузить только переданные сертификаты, можно ввести разделенный запятыми список их отпечатков.

Дополнительные сведения о добавлении сертификата в веб-приложение см. в разделе [Использование сертификатов в приложениях веб-сайтов Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)



## <a id="next"></a>Дальнейшие действия ##


Справочные материалы по программированию см. в [справочнике по API клиента C# для хранилища ключей Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 

<!---HONumber=AcomDC_1223_2015-->
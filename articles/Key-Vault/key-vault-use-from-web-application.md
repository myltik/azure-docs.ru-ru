<properties pageTitle="Использование хранилища ключей Azure из веб-приложения | Обзор" description="В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# Использование хранилища ключей Azure из веб-приложения #

## Введение  
В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения Azure. В нем рассматривается процесс доступа к секрету в хранилище ключей Azure для его использования в веб-приложении.

**Предполагаемое время выполнения:** 15 минут.


Общую информацию о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)

## Предварительные требования 

Для работы с этим учебником требуется:

- URI для секрета кода в хранилище ключей Azure
- Идентификатор клиента и секрет клиента для веб-приложения, зарегистрированного в Azure Active Directory, который имеет доступ к вашему хранилищу ключей
- Веб-приложение. Мы покажем действия для приложения ASP.NET MVC, развернутого в Azure в качестве веб-приложения. 

> [AZURE.NOTE]Необходимо выполнить действия, описанные в разделе [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md), чтобы получить URI секрета, идентификатор клиента и секрет клиента для веб-приложения.

Доступ к хранилищу ключей будет получать веб-приложение, зарегистрированное в Azure Active Directory и получившее права доступа к хранилищу ключей. Если это не так, вернитесь к разделу "Регистрация приложения" в учебнике "Приступая к работе" и повторите перечисленные шаги.

Этот учебник поможет веб-разработчикам понять принципы создания веб-приложений в Azure. Дополнительные сведения о веб-приложениях Azure см. в разделе [Обзор веб-приложений](../app-service-web-overview.md).



## <a id="packages"></a>Добавление пакетов NuGet ##
Существует три пакета, которые необходимо установить для веб-приложения.

- Библиотека проверки подлинности Active Directory содержит методы для взаимодействия с Azure Active Directory и управления удостоверениями пользователей.
- Библиотека хранилища ключей Azure содержит методы для взаимодействия с хранилищем ключей Azure.


Все три пакета можно установить с помощью консоли диспетчера пакетов, используя команду Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


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



## <a id="next"></a>Дальнейшие действия ##


Справочные материалы по программированию см. в [справочнике по API клиента C# для хранилища ключей Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=58_postMigration-->
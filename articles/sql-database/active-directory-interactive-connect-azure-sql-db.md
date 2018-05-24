---
title: Подключение ActiveDirectoryInteractive к SQL | Документация Майкрософт
description: Пример кода C# с пояснениями для подключения к службе "База данных SQL Azure" с использованием режима SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: article
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 6489fb5630e1990c942b461859650e2e469cda73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365021"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Использование режима ActiveDirectoryInteractive для подключения к службе "База данных SQL Azure"

В этой статье содержится готовый к запуску пример кода C#, который подключается к службе "База данных SQL Microsoft Azure". В программе на C# используется интерактивный режим проверки подлинности, который поддерживает многофакторную проверку подлинности (MFA) Azure AD. Например, при попытке подключения на мобильный телефон может отправляться код проверки.

Дополнительные сведения о поддержке MFA для средств SQL см. в статье [Azure Active Directory support in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory) (Поддержка Azure Active Directory в SQL Server Data Tools (SSDT)).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>Значение перечисления SqlAuthenticationMethod .ActiveDirectoryInteractive

Начиная с .NET Framework версии 4.7.2, перечисление [**SqlAuthenticationMethod**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) имеет новое значение — **.ActiveDirectoryInteractive**. При использовании в клиентской программе C# это значение перечисления указывает системе использовать интерактивный режим Azure AD, поддерживающий многофакторную проверку подлинности в службе "База данных SQL Azure". Пользователь, который затем запускает программу, увидит следующие диалоговые окна:

1. Диалоговое окно с отображением имени пользователя Azure AD, в котором запрашивается его пароль.
    - Если пароль не требуется, это диалоговое окно не отображается. Пароль не требуется в том случае, если домен пользователя включен в федерацию с Azure AD.

    Если многофакторная проверка подлинности обязательна для пользователя согласно набору политик, заданному в Azure AD, то далее отображаются представленные ниже диалоговые окна.

2. Дополнительные диалоговые окна отображаются в системе только при первом прохождении MFA пользователем. В диалоговом окне запрашивается номер мобильного телефона, на который будут отправляться текстовые сообщения. Каждое сообщение содержит *код проверки*, который пользователь должен ввести в следующем диалоговом окне.

3. Во другом диалоговом окне запрашивается код проверки MFA, который система отправила на мобильный телефон.

Сведения о том, как настроить Azure AD таким образом, чтоб требовалась аутентификация MFA, см. в статье [Getting started with Azure Multi-Factor Authentication in the cloud](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) (Начало работы с MFA Azure в облаке).

Снимки экрана этих диалоговых окон см. в статье [Configure multi-factor authentication for SQL Server Management Studio and Azure AD](sql-database-ssms-mfa-authentication-configure.md) (Настройка MFA для SQL Server Management Studio и Azure AD).

> [!TIP]
> Наша общая страница поиска для всех видов API-интерфейсов .NET Framework доступна по следующей ссылке на наш удобный инструмент **.NET API Browser**:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Добавив имя типа к необязательному присоединенному параметру **?term=**, можно получить страницу поиска с результатом:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Подготовка для C# с использованием портала Azure

Мы предполагаем, что вы уже [создали сервер службы "База данных SQL Azure"](sql-database-get-started-portal.md) и он доступен.


### <a name="a-create-an-app-registration"></a>О. Регистрация приложения

Для использования аутентификации Azure AD клиентская программа на C# должна предоставить GUID в качестве *clientId*, когда приложение попытается подключиться. При завершении регистрации приложения создается GUID. Он отображается на портале Azure как **идентификатор приложения**. Ниже указаны шаги навигации:

1. Портал Azure &gt; **Azure Active Directory** &gt; **Регистрация приложения**

    ![Регистрация приложения](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. Создается и отображается значение **Идентификатор приложения**.

    ![Отображение идентификатора приложения](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Зарегистрированное приложение** &gt; **Параметры** &gt; **Требуемые разрешения** &gt; **Добавить**

    ![Параметры разрешений для зарегистрированного приложения](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Требуемые разрешения** &gt; **Добавить доступ через API** &gt; **Выбор API** &gt; **База данных SQL Azure**

    ![Добавление доступа через API для службы "База данных SQL Azure"](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Доступ к API** &gt; **Выберите разрешения** &gt; **Делегированные разрешения**

    ![Делегирование разрешений API для службы "База данных SQL Azure"](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Определение администратора Azure AD на сервере службы "База данных SQL"

У каждого сервера службы "База данных SQL Azure" есть собственный логический сервер SQL из Azure AD. В нашем сценарии C# необходимо определить администратора Azure AD для сервера SQL Azure.

1. **SQL Server** &gt; **Администратор Active Directory** &gt; **Задать администратора**

    - Дополнительные сведения об администраторах и пользователях Azure AD для службы "База данных SQL Azure" см. на снимках экрана в статье [Configure and manage Azure Active Directory authentication with SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) (Настройка и управление аутентификацией Azure Active Directory с использованием службы "База данных SQL"), раздел **Provision an Azure Active Directory administrator for your Azure SQL Database server** (Подготовка администратора Azure Active Directory для сервера службы "База данных SQL Azure").


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Подготовка пользователя Azure AD для подключения к определенной базе данных

В службе Azure AD, связанной с конкретным сервером службы "База данных SQL Azure", можно добавить пользователя, у которого должен быть доступ к определенной базе данных.

Дополнительные сведения см. в статье об [аутентификации Azure Active Directory с помощью службы "База данных SQL Azure", Управляемого экземпляра или хранилища данных SQL](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Добавление пользователя без прав администратора в Azure AD

Чтобы подключиться к серверу службы "База данных SQL", можно использовать учетную запись администратора Azure AD этого сервера. Однако в более общем случае в Azure AD добавляется пользователь без прав администратора. Если для подключения используется учетная запись пользователя без прав администратора, последовательность MFA вызывается в том случае, если MFA принудительно задана для этого пользователя в Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Библиотека аутентификации Azure Active Directory (ADAL)

Программа C# основана на пространстве имен **Microsoft.IdentityModel.Clients.ActiveDirectory**. Классы для данного пространства имен содержатся в сборке с тем же именем.

- Используйте программу NuGet для загрузки и установки сборки ADAL.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Добавьте ссылку на сборку для поддержки компиляции программы C#.




## <a name="sqlauthenticationmethod-enum"></a>Перечисление SqlAuthenticationMethod

Одно из пространств имен, на которых основывается пример C#, — **System.Data.SqlClient**. Особый интерес представляет перечисление **SqlAuthenticationMethod**. Это перечисление имеет следующие значения:

- **SqlAuthenticationMethod.ActiveDirectory*Interactive***:&nbsp; используйте его с именем пользователя Azure AD для обеспечения многофакторной проверки подлинности (MFA).
    - Значение подробно описано в этой статье. Оно обеспечивает интерактивное взаимодействие с помощью диалоговых окон для ввода пароля пользователя, а затем аутентификацию MFA, если она принудительно задана для этого пользователя.
    - Это значение доступно начиная с .NET Framework версии 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory*Integrated***:&nbsp; следует использовать для *федеративной* учетной записи. При использовании федеративной учетной записи имя пользователя известно домену Windows. Этот метод не поддерживает MFA.

- **SqlAuthenticationMethod.ActiveDirectory*Password***:&nbsp; следует использовать для аутентификации, при которой требуется указать пользователя Azure AD и его пароль. Аутентификация выполняется в службе "База данных SQL Azure" Этот метод не поддерживает MFA.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Подготовка значений параметров C# с портала Azure

Для успешного выполнения программы на C# необходимо назначить нужные значения указанным ниже статическим полям. Эти статические поля действуют как входные параметры программы. Здесь показаны поля со значениями pretend. Также показаны расположения на портале Azure, откуда можно получить нужные значения:


| Имя статического поля | Значение Pretend | Расположение на портале Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-favorite-sqldb-svr.database.windows.net" | **Серверы SQL Server** &gt; **Фильтровать по имени** |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **Azure Active Directory** &gt; **Пользователь** &gt; **Новый гостевой пользователь** |
| Initial_DatabaseName | "master" | **Серверы SQL Server** &gt; **Базы данных SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** &gt; **Регистрация приложений**<br /> &nbsp; &nbsp; &gt; **Поиск по имени** &gt; **Идентификатор приложения** |
| RedirectUri | new Uri( "https://bing.com/") | **Azure Active Directory** &gt; **Регистрация приложений**<br /> &nbsp; &nbsp; &gt; **Поиск по имени** &gt; *[Your-App-regis]* &gt;<br /> &nbsp; &nbsp; **Параметры** &gt; **Значения RedirectURI**<br /><br />Для выполнения задач этой статьи для RedirectUri подойдет любое допустимое значение. Это значение в нашем случае на самом деле не используется. |
| &nbsp; | &nbsp; | &nbsp; |


В зависимости от конкретной ситуации могут требоваться не все параметры, приведенные в предыдущей таблице.




## <a name="run-ssms-to-verify"></a>Запуск SSMS для проверки

Перед запуском программы на C# полезно запустить среду SQL Server Management Studio (SSMS). При этом проверяется правильность различных конфигураций. Затем любой сбой программы C# можно сузить до ее исходного кода.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Проверка IP-адресов брандмауэра службы "База данных SQL"

Запустите SSMS с того же компьютера в том же здании, где будет позже запущена программа C#. Можно использовать любой режим **аутентификации**, даже самый простой. Если что-либо указывает на то, что брандмауэр сервера баз данных не принимает ваш IP-адрес, это можно исправить, как показано в статье о [правилах брандмауэра уровня сервера и уровня службы "База данных SQL Azure"](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Проверка MFA для Azure AD

Снова запустите SSMS, на этот раз для **аутентификации** должно быть установлено значение **Active Directory - Universal with MFA support** (Active Directory — универсальная с поддержкой MFA). Для выбора этого параметра необходима среда SSMS 17.5 или более поздней версии.

Дополнительные сведения см. в разделе о [настройке многофакторной проверки подлинности для SSMS и Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Пример кода C#

Чтобы скомпилировать этот пример на C#, необходимо добавить ссылку на сборку библиотеки DLL с именем **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Справочная документация

- Пространство имен **System.Data.SqlClient**:
    - поиск:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient);
    - прямая ссылка:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient).

- Пространство имен **Microsoft.IdentityModel.Clients.ActiveDirectory**:
    - поиск:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory);
    - прямая ссылка:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory).


#### <a name="c-source-code-in-two-parts"></a>Исходный код C#, в двух частях

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Вторая половина программы C#

Для удобства при чтении программа C# разбивается на два блока кода. Чтобы запустить программу, объедините эти два блока.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Фактические выходные данные тестовой программы на C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>Дополнительная информация

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)


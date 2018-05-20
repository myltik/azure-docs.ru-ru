---
title: Смена ключей подписывания Azure AD
description: В этой статье рассматриваются рекомендации по смене ключей подписывания для Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 29ac254bf3b0e8decb26452fc36112af0a3970af
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Смена ключей подписывания Azure Active Directory
В этой статье представлены основные сведения об открытых ключах, которые используются в Azure Active Directory (Azure AD) для подписывания маркеров безопасности. Важно отметить, что эти ключи периодически меняются и в случае чрезвычайной ситуации могут быть изменены немедленно. Все приложения, использующие Azure AD, должны обладать механизмом программной смены ключей или предоставлять возможность периодически запускать процесс смены ключей вручную. Здесь вы узнаете принцип действия ключей, а также то, как оценить степень влияния их смены на приложение и как обновить приложение или настроить периодический запуск процесса смены ключей в ручном режиме, чтобы приложение могло обрабатывать этот процесс.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Общие сведения о ключах подписывания в Azure AD
Azure AD использует шифрование с открытым ключом на основе отраслевых стандартов, чтобы устанавливать доверительные отношения с приложениями, использующими этот каталог. В частности, это происходит следующим образом. Azure AD использует ключ подписывания, состоящий из пары открытого и закрытого ключей. Когда пользователь входит в приложение, использующее Azure AD для проверки подлинности, Azure AD создает токен безопасности, который содержит сведения о пользователе. Azure AD подписывает этот токен, используя закрытый ключ, прежде чем отправить токен приложению. Чтобы убедиться, что маркер является допустимым и получен от Azure AD, приложение должно проверить его подпись с помощью открытого ключа. Этот ключ предоставляется Azure AD и содержится в [документе обнаружения OpenID Connect](http://openid.net/specs/openid-connect-discovery-1_0.html) или [документе метаданных федерации SAML/WS-Fed](active-directory-federation-metadata.md) клиента.

В целях безопасности ключи подписывания Azure AD периодически меняются и в случае чрезвычайной ситуации могут быть изменены немедленно. Любое приложение, которое интегрируется с Azure AD, должно быть подготовлено к обработке смены ключа независимо от того, насколько часто происходит такая смена. Если такая логика не предусмотрена и приложение попытается использовать ключ с истекшим сроком действия, чтобы проверить подпись токена, произойдет сбой запроса на вход.

В документе метаданных федерации и документе обнаружения OpenID Connect всегда существует несколько допустимых ключей. Следует подготовить приложение к использованию любого из ключей, указанных в документе, так как один ключ может быть сменен раньше, другой может оказаться его заменой и т. д.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Как оценить степень влияния смены ключей на приложение
Обработка смены ключей в приложении зависит от нескольких переменных, например от типа приложения или используемых протокола идентификации и библиотеки. В приведенных ниже разделах рассматривается, влияет ли смена ключей на самые распространенные типы приложений, а также содержатся рекомендации по обновлению приложения для поддержки автоматической смены ключей и обновлении ключей вручную.

* [Собственные клиентские приложения, осуществляющие доступ к ресурсам](#nativeclient)
* [Веб-приложения и интерфейсы API, осуществляющие доступ к ресурсам](#webclient)
* [Защищающие ресурсы веб-приложения и интерфейсы API, созданные с помощью служб приложений Azure](#appservices)
* [Веб-приложения и интерфейсы API, защищающие ресурсы с использованием ПО промежуточного слоя для аутентификации Microsoft Azure Active Directory, .NET OWIN OpenID Connect или WS-Fed](#owin)
* [Веб-приложения и интерфейсы API, защищающие ресурсы с использованием ПО промежуточного слоя для аутентификации на основе токена носителя JWT или OpenID Connect .NET Core](#owincore)
* [Веб-приложения и интерфейсы API, защищающие ресурсы с помощью модуля Node.js passport-azure-ad](#passport)
* [Защищающие ресурсы веб-приложения и интерфейсы API, созданные в Visual Studio 2015 или Visual Studio 2017](#vs2015)
* [Защищающие ресурсы веб-приложения, созданные в Visual Studio 2013](#vs2013)
* [Защищающие ресурсы веб-интерфейсы, созданные в Visual Studio 2013](#vs2013_webapi)
* [Защищающие ресурсы веб-приложения, созданные в Visual Studio 2012](#vs2012)
* [Защищающие ресурсы веб-приложения, созданные с помощью Visual Studio 2008, 2010 или Windows Identity Foundation](#vs2010)
* [Веб-приложения и интерфейсы API, защищающие ресурсы с помощью других библиотек или ручного внедрения поддерживаемых протоколов](#other)

Это руководство **не** применимо к:

* приложениям, добавленным из коллекции приложений Azure AD (включая пользовательские), так как для них предусмотрены отдельные правила в отношении ключей подписывания [Дополнительные сведения](../active-directory-sso-certs.md).
* локальным приложениям, опубликованным через прокси приложения, так как им не требуются ключи подписывания.

### <a name="nativeclient"></a>Собственные клиентские приложения, осуществляющие доступ к ресурсам
Как правило, приложения, только осуществляющие доступ к ресурсам (например, Microsoft Graph, KeyVault, API Outlook и другие Microsoft API), получают маркеры и передают их владельцу ресурса. Так как они не защищают ресурсы, они не проверяют токен, а вместе с тем и его подпись.

Собственные клиентские приложения, будь то классические или мобильные, попадают в эту категорию. Поэтому смена ключей не влияет на этот тип приложений.

### <a name="webclient"></a>Веб-приложения и интерфейсы API, осуществляющие доступ к ресурсам
Как правило, приложения, только осуществляющие доступ к ресурсам (например, Microsoft Graph, KeyVault, API Outlook и другие Microsoft API), получают маркеры и передают их владельцу ресурса. Так как они не защищают ресурсы, они не проверяют токен, а вместе с тем и его подпись.

Веб-приложения и интерфейсы API, использующие поток только для приложений (учетные данные или сертификат клиента), попадают в эту категорию. Поэтому смена ключей не влияет на этот тип приложений.

### <a name="appservices"></a>Защищающие ресурсы веб-приложения и интерфейсы API, созданные с помощью служб приложений Azure
В функциях проверки подлинности и авторизации (EasyAuth) служб приложений Azure уже предусмотрена логика для обработки автоматической смены ключей.

### <a name="owin"></a>Веб-приложения и интерфейсы API, защищающие ресурсы с использованием ПО промежуточного слоя для аутентификации Microsoft Azure Active Directory, .NET OWIN OpenID Connect или WS-Fed
Если в приложении используется ПО промежуточного слоя для аутентификации Microsoft Azure Active Directory, .NET OWIN OpenID Connect или WS-Fed, в нем уже предусмотрена логика для обработки автоматической смены ключей.

Чтобы проверить это, нужно поискать один из следующих фрагментов кода в файле Startup.cs или Startup.Auth.cs приложения.

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Веб-приложения и интерфейсы API, защищающие ресурсы с использованием ПО промежуточного слоя для аутентификации на основе токена носителя JWT или OpenID Connect.NET Core
Если в приложении используется ПО промежуточного слоя для аутентификации на основе токена носителя JWT или .NET Core OWIN OpenID Connect, в нем уже предусмотрена логика для обработки автоматической смены ключей.

Чтобы проверить это, нужно поискать один из следующих фрагментов кода в файле Startup.cs или Startup.Auth.cs приложения.

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="passport"></a>Веб-приложения и интерфейсы API, защищающие ресурсы с помощью модуля Node.js passport-azure-ad
Если в приложении используется модуль Node.js passport-ad, в нем уже предусмотрена логика для обработки автоматической смены ключей.

Это можно проверить, выполнив поиск следующего фрагмента кода в файле app.js приложения.

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Защищающие ресурсы веб-приложения и интерфейсы API, созданные в Visual Studio 2015 или Visual Studio 2017
Если приложение создано в Visual Studio 2015 или Visual Studio 2017 с использованием шаблона веб-приложения и в меню **Изменить способ проверки подлинности** выбран пункт **Рабочие или учебные учетные записи**, в приложении уже есть необходимая логика для обработки автоматической смены ключей. Эта логика, внедренная в ПО промежуточного слоя OWIN OpenID Connect, извлекает ключи из документа обнаружения OpenID Connect, кэширует их и периодически обновляет.

Если проверка подлинности добавлена в решение вручную, в приложении может отсутствовать необходимая логика смены ключей. Ее потребуется написать самостоятельно или выполнить действия, описанные в разделе [Веб-приложения и интерфейсы API, защищающие ресурсы с помощью других библиотек или ручного внедрения поддерживаемых протоколов](#other).

### <a name="vs2013"></a>Защищающие ресурсы веб-приложения, созданные в Visual Studio 2013
Если приложение создано в Visual Studio 2013 с использованием шаблона веб-приложения и в меню **Изменить способ проверки подлинности** выбран пункт **Учетные записи организации**, в приложении уже есть необходимая логика для обработки автоматической смены ключей. Логика хранит уникальный идентификатор организации и сведения о ключе подписывания в двух таблицах базы данных, связанных с проектом. Строку подключения для базы данных можно найти в файле Web.config проекта.

Если проверка подлинности добавлена в решение вручную, в приложении может отсутствовать необходимая логика смены ключей. Ее понадобится написать самостоятельно или выполнить действия, описанные в разделе [Веб-приложения и интерфейсы API, использующие другие библиотеки или ручное внедрение поддерживаемых протоколов](#other).

Далее представлены шаги, которые помогут проверить правильность работы логики приложения.

1. В Visual Studio 2013 откройте решение и в окне справа выберите вкладку **Обозреватель серверов** .
2. Разверните узлы **Подключения данных**, **DefaultConnection**, а затем — **Таблицы**. Найдите и щелкните правой кнопкой мыши таблицу **IssuingAuthorityKeys**, а затем выберите **Показать данные таблицы**.
3. В таблице **IssuingAuthorityKeys** будет по крайней мере одна строка, соответствующая значению отпечатка для ключа. Удалите все строки в таблице.
4. Щелкните правой кнопкой мыши таблицу **Tenants** и выберите **Показать данные таблицы**.
5. В таблице **Клиенты** будет по крайней мере одна строка, соответствующая уникальному идентификатору клиента каталога. Удалите все строки в таблице. Если не удалить строки в таблицах **Tenants** и **IssuingAuthorityKeys**, то при выполнении произойдет ошибка.
6. Создайте и запустите приложение. Войдя в учетную запись, можно остановить работу приложения.
7. Вернитесь в **обозреватель сервера** и просмотрите значения в таблицах **IssuingAuthorityKeys** и **Tenants**. Как можно заметить, в них автоматически добавлены соответствующие сведения из документа метаданных федерации.

### <a name="vs2013"></a>Защищающие ресурсы веб-интерфейсы, созданные в Visual Studio 2013
Если приложение веб-API создано в Visual Studio 2013 с использованием шаблона веб-API и в меню **Изменить способ проверки подлинности** выбран пункт **Учетные записи организации**, в нем уже есть необходимая логика.

Если проверка подлинности настроена вручную, следуйте указаниям ниже, чтобы узнать, как настроить веб-API для автоматического обновления сведений о ключах.

В следующем фрагменте кода показано, как получить последние ключи из документа метаданных федерации, а затем использовать [обработчик маркеров JWT](https://msdn.microsoft.com/library/dn205065.aspx) для проверки маркера. Во фрагменте кода предполагается, что для сохранения ключа, с помощью которого будет выполняться последующая проверка токенов из Azure AD, будет использоваться собственный механизм кэширования, несмотря на место его хранения (база данных, файл конфигурации или другое расположение).

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Защищающие ресурсы веб-приложения, созданные в Visual Studio 2012
Если приложение создано в Visual Studio 2012, вероятно, оно настроено с помощью средства Identity and Access Tool. Кроме того, возможно, используется [проверка реестра имен поставщиков](https://msdn.microsoft.com/library/dn205067.aspx). VINR отвечает за обслуживание сведений о доверенных поставщиках удостоверений (Azure AD) и ключах, используемых для проверки токенов, которые выдали эти поставщики. VINR также упрощает автоматическое обновление сведений о ключах, хранящихся в файле Web.config. Для этого скачивается последний документ метаданных федерации, связанный с каталогом, проверяется,не устарела ли конфигурация по сравнению с этим документом, и приложение обновляется для использования нового ключа при необходимости.

Если приложение создано с использованием любого из примеров кода или документации с пошаговыми инструкциями, предоставленных корпорацией Майкрософт, логика смены ключей уже добавлена в проект. Вы заметите, что код ниже уже есть в проекте. Если в приложении нет этой логики, выполните следующие шаги, чтобы добавить ее и убедиться в правильности ее работы.

1. В **обозревателе решений** добавьте ссылку на сборку **System.IdentityModel** для соответствующего проекта.
2. Откройте файл **Global.asax.cs** и добавьте следующие директивы using:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Добавьте в файл **Global.asax.cs** следующий метод:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Вызовите метод **RefreshValidationSettings()** в методе **Application_Start()** в файле **Global.asax.cs**, как показано ниже.
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

После выполнения этих шагов файл Web.config приложения обновится с использованием последних сведений документа метаданных федерации, в том числе последних ключей. Это обновление будет выполняться при каждом перезапуске пула приложений в IIS. По умолчанию в IIS настроен перезапуск приложений через каждые 29 часов.

Выполните следующие шаги, чтобы убедиться, что логика смены ключей работает должным образом.

1. Убедившись, что приложение использует код, приведенный выше, откройте файл **Web.config**, перейдите к блоку **<issuerNameRegistry>** и найдите следующие несколько строк.
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. В таблице **<add thumbprint=””>** измените значение отпечатка, заменив любой из знаков другим знаком. Сохраните файл **Web.config** .
3. Выполните сборку приложения и запустите его. Если у вас получилось войти, приложение успешно обновляет ключ, скачивая необходимые данные из документа метаданных федерации каталога. Если при входе возникают проблемы, убедитесь, что в приложение внесены правильные изменения. Для этого прочитайте статью о [добавлении единого входа в веб-приложение с помощью Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) или скачайте и проверьте пример кода в разделе [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) (Мультитенантное облачное приложение для Azure Active Directory).

### <a name="vs2010"></a>Защищающие ресурсы веб-приложения, созданные с помощью Visual Studio 2008 или 2010 и Windows Identity Foundation v1.0 для .NET 3.5
Если приложение создано на платформе Windows Identity Foundation v1.0, в нем не предусмотрен механизм автоматического обновления конфигурации для использования нового ключа.

* *проще* всего в пакете SDK для Windows Identity Foundation, с помощью которого можно получить последний документ метаданных и обновить конфигурацию.
* Обновите платформу приложения до версии .NET 4.5, которая содержит новейшую версию Windows Identity Foundation, расположенную в пространстве имен System. Затем можно использовать [проверку реестра имен поставщиков](https://msdn.microsoft.com/library/dn205067.aspx) , чтобы автоматически обновить конфигурацию приложения.
* Запустите процесс смены ключей в ручном режиме согласно инструкциям, приведенным в конце этого руководства.

Ниже приведены инструкции по использованию FedUtil для обновления конфигурации.

1. Убедитесь, что на компьютере для разработки для Visual Studio 2008 или 2010 установлен пакет SDK для Windows Identity Foundation v1.0. Если этот пакет не установлен, его можно [скачать отсюда](https://www.microsoft.com/en-us/download/details.aspx?id=4451).
2. В Visual Studio откройте решение, щелкните соответствующий проект правой кнопкой мыши и выберите пункт **Update federation metadata**(Обновить метаданные федерации). Если этот параметр недоступен, средство FedUtil и/или пакет SDK для Windows Identity Foundation v1.0 не установлен.
3. В командной строке выберите **Обновить** , чтобы начать обновление метаданных федерации. При наличии доступа к среде сервера, где размещено приложение, можно использовать [планировщик автоматического обновления метаданных](https://msdn.microsoft.com/library/ee517272.aspx)средства FedUtil.
4. Нажмите кнопку **Готово** , чтобы завершить обновление.

### <a name="other"></a>Веб-приложения и интерфейсы API, защищающие ресурсы с помощью других библиотек или ручного внедрения поддерживаемых протоколов
Если используется другая библиотека или вручную внедрены поддерживаемые протоколы, их необходимо проверить, чтобы убедиться, что ключ извлекается из документа обнаружения OpenID Connect или документа метаданных федерации. Для этого необходимо проверить в коде приложения или коде библиотеки наличие вызовов документа обнаружения OpenID Connect или документа метаданных федерации.

Если ключ хранится в другом месте или жестко закодирован в приложении, можно вручную извлечь и обновить его, выполнив смену ключа вручную согласно инструкциям, приведенным в конце этого руководства. **Настоятельно рекомендуем настроить поддержку автоматической смены ключей в приложении**, используя методы, описанные в этой статье, чтобы в будущем избежать проблем с работой и дополнительных затрат в том случае, если в Azure AD будет повышена частота смены или произойдет внештатная аварийная смена ключей.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Тестирование приложения для оценки степени влияния
Чтобы проверить, поддерживается ли автоматическая смена ключей в приложении, необходимо скачать скрипты и выполнить инструкции, описанные [в этом репозитории GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Как сменить ключи вручную, если приложение не поддерживает автоматическую смену ключей
Если приложение **не** поддерживает автоматическую смену ключей, необходимо создать процесс, который будет периодически отслеживать ключи подписывания Azure AD и выполнять смену ключей в ручном режиме соответствующим образом. [Этот репозиторий GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) содержит скрипты и инструкции о том, как это сделать.


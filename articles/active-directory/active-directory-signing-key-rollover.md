<properties
	pageTitle="Смена ключей подписывания Azure AD | Microsoft Azure"
	description="В этой статье рассматриваются рекомендации по смене ключей подписывания для Azure Active Directory"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Смена ключей подписывания Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

В этом разделе представлены основные сведения об открытых ключах, которые используются в Azure Active Directory (Azure AD) для подписывания маркеров безопасности. Следует отметить, что эти ключи меняются каждые 6 недель. В экстренной ситуации ключ можно сменить раньше. Все приложения, использующие Azure AD, должны обладать механизмом программной смены ключей. Далее вы узнаете о принципе действия ключей, а также о том, как обновить приложение для смены ключей.

## Общие сведения о ключах подписывания в Azure AD

Azure AD использует шифрование с открытым ключом на основе отраслевых стандартов, чтобы устанавливать доверительные отношения с приложениями, использующими этот каталог. В частности, это происходит следующим образом. Azure AD использует ключ подписывания, состоящий из пары открытого и закрытого ключей. Когда пользователь входит в приложение, использующее Azure AD для проверки подлинности, Azure AD создает маркер безопасности, который содержит сведения о пользователе. Azure AD подписывает этот маркер, используя закрытый ключ, прежде чем отправить маркер приложению. Чтобы убедиться, что маркер является допустимым и получен от Azure AD, приложение должно проверить его подпись с помощью открытого ключа, предоставленного Azure AD, который содержится в документе метаданных федерации клиента. Этот открытый ключ — и ключ подписывания, от которого он происходит, — аналогичен тому, который используется для всех клиентов в Azure AD.

В целях безопасности открытый ключ Azure AD меняется каждые 6 недель. В экстренной ситуации ключ можно сменить раньше. Любое приложение, которое интегрируется с Azure AD, должно быть подготовлено к обработке смены ключа независимо от того, насколько часто происходит такая смена. В зависимости от времени создания приложения и библиотеки проверки подлинности, использованной для его создания, в нем может быть предусмотрена или не предусмотрена логика, необходимая для обработки смены ключей. Если такая логика не предусмотрена и приложение попытается использовать открытый ключ с истекшим сроком действия, чтобы проверить подпись маркера, произойдет сбой запроса на вход.

Поскольку ключ может быть сменен в любой момент, всегда существует более одного допустимого открытого ключа в документе метаданных федерации. Следует подготовить приложение к использованию любого из ключей, указанных в документе, так как один ключ может быть сменен раньше, другой может оказаться его заменой и т. д. Рекомендуется, чтобы приложение кэшировало эти ключи в базе данных или в файле конфигурации, чтобы увеличить эффективность обмена данными с Azure AD при входе и ускорить проверку маркера с использованием другого ключа.

## Как обновить приложение для использования логики смены ключей

Обработка смены ключей в приложении зависит от нескольких переменных, например от использованной платформы идентификации, ее версии или типа приложения. В разделах ниже показано, как обновить приложения и конфигурации самых распространенных типов. Вы также можете выполнить шаги, чтобы убедиться, что логика работает должным образом.

### Веб-приложения, созданные в Visual Studio 2013

Если приложение создано в Visual Studio 2013 с использованием шаблона веб-приложения и в меню **Изменить способ проверки подлинности** выбран пункт **Учетные записи организации**, в приложении уже есть необходимая логика для обработки смены ключей. Логика хранит уникальный идентификатор организации и сведения о ключе подписывания в двух таблицах базы данных, связанных с проектом. Строку подключения для базы данных можно найти в файле Web.config проекта.

Если проверка подлинности добавлена в решение вручную, в приложении отсутствует необходимая логика смены ключей. Ее понадобится написать самостоятельно или выполнить шаги в разделе "Получение последнего ключа вручную и обновление приложения".

Далее представлены шаги, которые помогут проверить правильность работы логики приложения.

1. В Visual Studio 2013 откройте решение и в окне справа выберите вкладку **Обозреватель серверов**.
2. Разверните узлы **Подключения данных**, **DefaultConnection**, а затем **Таблицы**. Найдите и щелкните правой кнопкой мыши таблицу **IssuingAuthorityKeys**, а затем выберите пункт **Показать таблицу данных**.
3. В таблице **IssuingAuthorityKeys** будет по крайней мере одна строка, соответствующая значению отпечатка для ключа. Удалите все строки в таблице.
4. Щелкните правой кнопкой мыши таблицу **Клиенты** и выберите пункт **Показать таблицу данных**.
5. В таблице **Клиенты** будет по крайней мере одна строка, соответствующая уникальному идентификатору клиента каталога. Удалите все строки в таблице. Если не удалить строки в таблицах **Клиенты** и **IssuingAuthorityKeys**, при выполнении произойдет ошибка.
6. Создайте и запустите приложение. Войдя в учетную запись, можно остановить работу приложения.
7. Вернитесь на вкладку **Обозреватель сервера** и посмотрите значения в таблице **IssuingAuthorityKeys** и **Клиенты**. Как можно заметить, в них автоматически добавлены соответствующие сведения из документа метаданных федерации.

### Веб-приложения, созданные в Visual Studio 2012

Если приложение создано в Visual Studio 2012, вероятно, оно настроено с помощью средства Identity and Access Tool. Кроме того, возможно, используется [проверка реестра имен поставщиков (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR отвечает за обслуживание сведений о доверенных поставщиках удостоверений (Azure AD) и ключах, используемых для проверки маркеров, которые выдали эти поставщики. VINR также упрощает автоматическое обновление сведений о ключах, хранящихся в файле Web.config. Для этого скачивается последний документ метаданных федерации, связанный с каталогом, проверяется,не устарела ли конфигурация по сравнению с этим документом, и приложение обновляется для использования нового ключа при необходимости.

Если приложение создано с использованием любого из примеров кода или документации с пошаговыми инструкциями, предоставленных корпорацией Майкрософт, логика смены ключей уже добавлена в проект. Вы заметите, что код ниже уже есть в проекте. Если в приложении нет этой логики, выполните следующие шаги, чтобы добавить ее и убедиться в правильности ее работы.

1. На вкладке **Обозреватель решений** добавьте ссылку на сборку **System.IdentityModel** для соответствующего проекта.
2. Откройте файл **Global.asax.cs** и добавьте следующие директивы using:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Добавьте в файл **Global.asax.cs** следующий метод:
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Вызовите метод **RefreshValidationSettings()** в методе **Application\_Start()** в файле **Global.asax.cs**, как показано ниже:
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

1. Убедившись, что приложение использует код выше, откройте файл **Web.config**, перейдите к блоку **<issuerNameRegistry>** и найдите следующие несколько строк:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. В параметре **<add thumbprint=””>** измените значение отпечатка, заменив любой из символов другим символом. Сохраните файл **Web.config**.

3. Выполните сборку приложения и запустите его. Если у вас получилось войти, приложение успешно обновляет ключ, скачивая необходимые данные из документа метаданных федерации каталога. Если при входе возникают проблемы, убедитесь, что в приложение внесены правильные изменения. Для этого см. раздел [Adding Sign-On to Your Web Application Using Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) (Добавление единого входа в веб-приложение с помощью Azure AD) или скачайте и проверьте пример кода в разделе [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) (Мультитенантное облачное приложение для Azure Active Directory).


### Веб-приложения, созданные с помощью Visual Studio 2008 или 2010 и Windows Identity Foundation v1.0 для .NET 3.5

Если приложение создано на платформе Windows Identity Foundation v1.0, в нем не предусмотрен механизм автоматического обновления конфигурации для использования нового ключа. Чтобы обновить ключ, проще всего использовать средство FedUtil в пакете SDK для Windows Identity Foundation, с помощью которого можно получить последний документ метаданных и обновить конфигурацию. Соответствующие указания приведены ниже. Кроме того, можно выполнить одну из следующих рекомендаций:

- Следуйте инструкциям в разделе "Получение последнего ключа вручную и обновление приложения" ниже и создайте логику для выполнения шагов программным способом.
- Обновите платформу приложения до версии .NET 4.5, которая содержит новейшую версию Windows Identity Foundation, расположенную в пространстве имен System. Затем можно использовать [проверку реестра имен поставщиков (VINR)](https://msdn.microsoft.com/library/dn205067.aspx), чтобы автоматически обновить конфигурацию приложения.


1. Убедитесь, что на компьютере для разработки для Visual Studio 2008 или 2010 установлен пакет SDK для Windows Identity Foundation v1.0. Если он не установлен, пакет можно [скачать отсюда](https://www.microsoft.com/ru-RU/download/details.aspx?id=4451).
2. В Visual Studio откройте решение, щелкните соответствующий проект правой кнопкой мыши и выберите пункт **Update federation metadata** (Обновить метаданные федерации). Если этот параметр недоступен, средство FedUtil и/или пакет SDK для Windows Identity Foundation v1.0 не установлен.
3. В командной строке выберите элемент **Обновить**, чтобы начать обновление метаданных федерации. При наличии доступа к среде сервера, где размещено приложение, можно использовать [планировщик автоматического обновления метаданных](https://msdn.microsoft.com/library/ee517272.aspx) средства FedUtil.
4. Щелкните **Готово**, чтобы завершить обновление.

### Веб-API, использующие веб-маркеры JSON (JWT)

При наличии приложения, которое вызывает веб-API с использованием маркера JWT, выданного Azure AD для авторизации запроса, маркер JWT проверяется так же, как и запрос на вход (проверяется подпись с использованием открытого ключа Azure AD). Приложения веб-API следует подготовить к обработке смены ключей, так как в конечном счете они используют один сертификат X509 для подписывания маркеров.

Если приложение веб-API создано в Visual Studio 2013 с использованием шаблона веб-API и в меню **Изменить способ проверки подлинности** выбран пункт **Учетные записи организации**, в нем уже есть необходимая логика. Если проверка подлинности настроена вручную, следуйте указаниям ниже, чтобы узнать, как настроить веб-API для автоматического обновления сведений о ключах.

В следующем фрагменте кода показано, как получить последние ключи из документа метаданных федерации, а затем использовать [обработчик маркеров JWT](https://msdn.microsoft.com/library/dn205065.aspx) для проверки маркера. Во фрагменте кода предполагается, что для сохранения ключа, с помощью которого будет выполняться последующая проверка маркеров из Azure AD, будет использоваться собственный механизм кэширования, несмотря на место его хранения (база данных, файл конфигурации или другое расположение).

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
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
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

### Получение последнего ключа вручную и обновление приложения

Если тип приложения или платформы в данный момент не поддерживает механизм автоматического обновления ключа, можно сделать следующее:

1. В веб-браузере перейдите по адресу https://manage.windowsazure.com, войдите в свою учетную запись и в меню слева щелкните значок Active Directory.
2. Щелкните каталог, в котором зарегистрировано приложение, а затем на панели команд выберите ссылку **Просмотр конечных точек**.
3. В списке конечных точек единого входа и каталога скопируйте ссылку **Документ метаданных федерации**.
4. Откройте новую вкладку в браузере и перейдите по скопированному URL-адресу. Отобразится содержимое XML-документа метаданных федерации. Дополнительные сведения об этом документе см. в разделе о метаданных федерации.
5. Чтобы обновить приложение для использования нового ключа, найдите все блоки **<RoleDescriptor>**, а затем скопируйте значение элемента **<X509Certificate>** в каждом из них. Например:
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
6. Скопировав значение элемента **<X509Certificate>**, откройте обычный текстовый редактор и вставьте значение. Удалите все пробелы в конце, а затем сохраните файл с расширением **CER**.

Вы только что создали сертификат X509, используемый в качестве открытого ключа для Azure AD. Используя сведения о сертификате, например его отпечаток и дату окончания срока действия, можно вручную или программным образом проверить, действительны ли сертификат и отпечаток, используемые в приложении в данный момент.

<!---HONumber=AcomDC_0601_2016-->
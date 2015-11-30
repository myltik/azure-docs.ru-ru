<properties 
	pageTitle="Использование Axinom для доставки лицензий Widevine в службы мультимедиа Azure" 
	description="В этой статье описывается использование служб мультимедиа Azure (AMS) для доставки потока, который зашифрован динамически службой AMS, с помощью лицензий DRM PlayReady и Widevine. Лицензию PlayReady выдает сервер лицензирования служб мультимедиа PlayReady, а лицензию Widevine — сервер лицензирования Axinom." 
	services="media-services" 
	documentationCenter="" 
	authors="willzhan,Mingfeiy,rajputam,Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"  
	ms.author="juliako"/>

#Использование Axinom для доставки лицензий Widevine в службы мультимедиа Azure  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##Обзор

В службы мультимедиа Azure (AMS) добавлена динамическая защита Google Widevine (подробные сведения см. [в блоге Мингфей Ян (Mingfei Yan)](https://azure.microsoft.com/ru-RU/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)). Кроме того, в мультимедиапроигрыватель Azure (AMP) добавлена поддержка Widevine (подробные сведения см. в [документации по AMP](http://amp.azure.net/libs/amp/latest/docs/)). Это большое достижение в потоковой передаче содержимого DASH, защищенного с помощью CENC с несколькими собственными технологиями DRM (PlayReady и Widevine) в современных браузерах, оснащенных MSE и EME.

Начиная с версии 3.5.2 пакета SDK служб мультимедиа для .NET, службы мультимедиа позволяют настраивать шаблоны лицензии Widevine и получать лицензии Widevine. Для доставки лицензий Widevine можно использовать следующие партнеры AMS: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) и [castLabs](http://castlabs.com/company/partners/azure/).

В этой статье описывается интеграция и тестирование сервера лицензирования Widevine под управлением Axinom. В частности, рассматриваются следующие операции:

- настройка динамического стандартного шифрования с помощью нескольких технологий DRM (PlayReady и Widevine) с соответствующими URL-адресами для получения лицензии;
- создание маркера JWT для удовлетворения требований сервера лицензирования;
- разработка приложения мультимедиапроигрывателя Azure, которое обрабатывает получение лицензий с помощью проверки подлинности маркеров JWT;

Описание всей системы и процесса передачи ключа содержимого, идентификатора ключа, начального значения, маркера JTW и соответствующих утверждений представлено на следующей схеме.

![DASH и CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##Система защиты содержимого

Сведения о настройке динамической защиты и политики доставки ключей см. в блоге Мингфей Ян (Mingfei Yan) [Как настроить упаковку Widevine с помощью служб мультимедиа Azure](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Вы можете настроить динамическую защиту CENC c помощью нескольких технологий DRM для потоковой передачи DASH при наличии двух следующих типов защиты.

1. Защита PlayReady для MS Edge и IE11, в которой могут быть установлены ограничения авторизации маркеров. При ограничении по маркеру к политике должен прилагаться маркер, выданный службой маркеров безопасности (STS), например Azure Active Directory.
1. Защита Widevine для Chrome (может потребоваться проверка подлинности маркера с помощью маркера, выданного другой службой STS). 

В разделе [Создание маркеров JWT](media-services-axinom-integration.md#jwt-token-generation) объясняется, почему Azure Active Directory нельзя использовать в качестве службы маркеров безопасности для сервера лицензирования Widevine Axinom.

###Рекомендации

1. Чтобы создать ключ содержимого для настройки службы доставки ключей, необходимо использовать заданное начальное значение Axinom (8888000000000000000000000000000000000000) и созданный или выбранный идентификатор ключа. Сервер лицензирования Axinom будет выдавать все лицензии, содержащие ключи содержимого на основе одного начального значения, которое является допустимым как для тестирования, так и для рабочих задач.
1. URL-адрес для получения лицензии Widevine для тестирования: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). Допускается использование HTTP и HTTPS.

##Подготовка мультимедиапроигрывателя Azure

Проигрыватель AMP 1.4.0 поддерживает воспроизведение содержимого AMS, которое динамически упаковывается с помощью DRM PlayReady и Widevine. Если сервер лицензирования Widevine не требует проверки подлинности маркера, для тестирования содержимого DASH, защищенного с помощью Widevine, не требуются никакие дополнительные действия. Например, группа разработчиков для AMP предоставляет простой [пример](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html). Вы сможете увидеть, как он работает в браузерах Edge и IE11 с помощью PlayReady и в Chrome с помощью Widevine. Сервер лицензирования Widevine, предоставляемый Axinom, требует проверки подлинности маркера JWT. Маркер JWT должен передаваться с запросом лицензии через заголовок HTTP X-AxDRM-Message. Для этого перед тем, как задавать источник, на веб-страницу, где размещается AMP, необходимо добавить следующий код JavaScript:

	<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Остальная часть кода AMP — стандартный API-интерфейс AMP, как в приведенном [здесь](http://amp.azure.net/libs/amp/latest/docs/) документе по AMP.

Обратите внимание, что указанный выше код JavaScript для настройки пользовательского заголовка авторизации все же является временным решением, которое будет действовать до выпуска официального постоянного решения для AMP.

##Создание маркера JWT

Сервер лицензирования Widevine для тестирования, предоставляемый Axinom, требует проверки подлинности маркера JWT. Кроме того, одно из утверждений в маркере JWT является объектом сложного типа, а не данными примитивного типа.

К сожалению, Azure AD может выдавать маркеры JWT только примитивного типа. Аналогично API .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler и JwtPayload) позволяет использовать в качестве входных данных только утверждения сложного типа. При этом утверждения по-прежнему сериализуются как строка. Поэтому мы не можем использовать ни один из этих типов, чтобы создать маркер JWT по запросу лицензии Widevine.


Мы будем использовать [пакет NuGet JWT](https://www.nuget.org/packages/JWT) Джона Шиэна (John Sheehan), так как он соответствует требованиям.

Ниже приведен код для создания маркера JWT с утверждениями, которые требуются для тестирования сервером лицензирования Axinom Widevine.

	
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.IdentityModel.Tokens;
	using System.IdentityModel.Protocols.WSTrust;
	using System.Security.Claims;
	
	namespace OpenIdConnectWeb.Utils
	{
	    public class JwtUtils
	    {
	        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
	        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
	        {
	            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
	
	            var payload = new Dictionary<string, object>()
	                         {
	                             { "version", 1 },
	                             { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
	                             { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
	                         };
	
	            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
	
	            return token;
	        }
	
	        //convert hex string to byte[]
	        public static byte[] ConvertHexStringToByteArray(string hexString)
	        {
	            if (hexString.Length % 2 != 0)
	            {
	                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
	            }
	
	            byte[] HexAsBytes = new byte[hexString.Length / 2];
	            for (int index = 0; index < HexAsBytes.Length; index++)
	            {
	                string byteValue = hexString.Substring(index * 2, 2);
	                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
	            }
	
	            return HexAsBytes;
	        }
	
	    }  
	
	}  

Сервер лицензирования Axinom Widevine

	<add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
	<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
	<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
	<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###Рекомендации

1.	Хотя служба доставки лицензий AMS PlayReady требует наличия перед маркером проверки подлинности префикса Bearer=, сервер лицензирования Axinom Widevine не использует его.
2.	В качестве ключа подписи используется ключ обмена данными Axinom. Обратите внимание, что этот ключ представляет собой шестнадцатеричную строку, однако при кодировании его следует рассматривать как последовательность байтов, а не как строку. Это возможно при использовании метода ConvertHexStringToByteArray.

##Получение идентификатора ключа

Вы могли заметить, что в коде, создающем маркер JWT, идентификатор ключа является обязательным. Поскольку маркер JWT должен быть создан до загрузки проигрывателя AMP, вам сначала необходимо получить идентификатор ключа.

Это можно сделать несколькими способами. Например, идентификатор ключа может храниться вместе с метаданными содержимого в базе данных. Также ключ можно извлечь из MPD-файла DASH. В следующем фрагменте кода реализован второй способ.

	//get key_id from DASH MPD
	public static string GetKeyID(string dashUrl)
	{
	    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
	    {
	        dashUrl += "(format=mpd-time-csf)";
	    }
	
	    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
	    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
	    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
	    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
	    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
	    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
	    objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
	    objXmlNamespaceManager.PushScope();
	
	    XPathNodeIterator objXPathNodeIterator;
	    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
	
	    string key_id = string.Empty;
	    if (objXPathNodeIterator.MoveNext())
	    {
	        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
	    }
	
	    return key_id;
	}

##Сводка

В системе защиты содержимого служб мультимедиа Azure и мультимедиапроигрывателе Azure недавно добавлена поддержка Widevine. Благодаря этому мы смогли реализовать потоковую передачу DASH и нескольких собственных технологий DRM (PlayReady + Widevine) со службой лицензий PlayReady в AMS и сервером лицензирования Widevine от Axinom для следующих современных браузеров:

- Chrome
- Microsoft Edge под управлением Windows 10.
- IE 11 под управлением Windows 8.1 и Windows 10.
- Мультимедиапроигрыватель Azure также поддерживает браузеры Firefox (классический) и Safari под управлением Mac OS (не iOS) посредством Silverlight и того же URL-адреса.

Для мини-решения, в котором используется сервер лицензирования Axinom Widevine, требуются следующие параметры. Все параметры, кроме ключа идентификатора, предоставляются Axinom в соответствии с настройками сервера Widevine.


Параметр|Использование
---|---
Идентификатор ключа обмена данными|Должен быть включен в качестве значения утверждения com\_key\_id в маркере JWT (см. [этот](media-services-axinom-integration.md#jwt-token-generation) раздел).
Ключ обмена данными|Должен использоваться в качестве ключа подписывания маркера JWT (см. [этот](media-services-axinom-integration.md#jwt-token-generation) раздел).
Начальное значение ключа|Должно использоваться для создания ключа содержимого с любым заданным идентификатором ключа содержимого (см. [этот](media-services-axinom-integration.md#content-protection) раздел).
URL-адрес для приобретения лицензии Widevine|Должен использоваться при настройке политики доставки ресурсов-контейнеров для потоковой передачи DASH (см. [этот](media-services-axinom-integration.md#content-protection) раздел).
Идентификатор ключа содержимого|Должен быть включен в значение утверждения сообщения об обслуживании маркера JWT (см. [этот](media-services-axinom-integration.md#jwt-token-generation) раздел). 


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###Благодарности 

Мы выражаем признательность тем, кто помог нам в составлении этого документа — это Кристьян Йоджи (Kristjan Jõgi) из Axinom, Мингфей Ян (Mingfei Yan) и Амит Раджпут (Amit Rajput).

<!---HONumber=Nov15_HO4-->
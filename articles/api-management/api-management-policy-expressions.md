---
title: "Выражения политики в службе управления API Azure | Документация Майкрософт"
description: "Сведения о выражениях политики в службе управления API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: a5bcd03e71a69928fa1e02a5286801c4933d17ef
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
---
# <a name="api-management-policy-expressions"></a>Выражения политики в службе управления API
В этой статье описывается синтаксис выражений политики C# 6.0. У каждого выражения есть доступ к неявно заданной переменной [контекста](api-management-policy-expressions.md#ContextVariables) и разрешенному [подмножеству](api-management-policy-expressions.md#CLRTypes) типов .NET Framework.  

Дополнительные сведения

- Способы передачи сведений о контексте во внутреннюю службу. Используйте политики [настройки параметра строки запроса](api-management-transformation-policies.md#SetQueryStringParameter) и [настройки HTTP-заголовка](api-management-transformation-policies.md#SetHTTPheader), чтобы передать эти сведения.
- Использование политики [проверки JWT](api-management-access-restriction-policies.md#ValidateJWT) для предварительной авторизации доступа к операциям на основе утверждений маркеров.   
- Сведения об использовании трассировки с помощью [инспектора API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) для оценки политик, а также результаты этой оценки.  
- Использование выражений с политиками [получения из кэша](api-management-caching-policies.md#GetFromCache) и [хранения в кэше](api-management-caching-policies.md#StoreToCache) для настройки кэширования ответа службы управления API. Установите такую же длительность, как и для кэширования ответа во внутренней службе, которая задается директивой `Cache-Control` на сервере.  
- Сведения о том, как отфильтровать содержимое. Удалите элементы данных из ответа, полученного из внутренней службы с помощью политик [потока управления](api-management-advanced-policies.md#choose) и [задания текста](api-management-transformation-policies.md#SetBody). 
- Чтобы скачать инструкции политики, перейдите к репозиторию GitHub [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).  
  
  
##  <a name="Syntax"></a> Синтаксис  
 Выражения с одним оператором заключаются в элементы `@(expression)`, где `expression` — оператор выражения C# правильного формата.  
  
 Выражения с несколькими операторами заключаются в элементы `@{expression}`. Все ветви кода в выражениях с несколькими операторами должны заканчиваться оператором `return`.  
  
##  <a name="PolicyExpressionsExamples"></a> Примеры  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> Использование  
 Выражения можно использовать в качестве значений атрибутов или текстовых значений в любой [политике](api-management-policies.md) службы управления API, если в справочнике по политике не указано иное.  
  
> [!IMPORTANT]
>  При использовании выражений политики выполняется только ограниченная проверка выражений политики во время ее определения. Выражения выполняет шлюз во время выполнения. Все исключения, создаваемые выражениями политики, приводят к ошибке среды выполнения.  
  
##  <a name="CLRTypes"></a> Типы .NET Framework, допустимые в выражениях политики  
 В следующей таблице перечислены типы .NET Framework и их члены, допустимые в выражениях политики.  
  
|Тип CLR|Поддерживаемые методы|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JArray|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JConstructor|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JContainer|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JObject|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JProperty|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JRaw|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JToken|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JTokenType|Поддерживаются все методы|  
|Newtonsoft.Json.Linq.JValue|Поддерживаются все методы|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Все|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Все|  
|System.Collections.Generic.ISet<TKey, TValue>|Все|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key, Value|  
|System.Collections.Generic.List<TKey, TValue>|Все|  
|System.Collections.Generic.Queue<TKey, TValue>|Все|  
|System.Collections.Generic.Stack<TKey, TValue>|Все|  
|System.Convert|Все|  
|System.DateTime|Все|  
|System.DateTimeKind|Время в формате UTC|  
|System.DateTimeOffset|Все|  
|System.Decimal|Все|  
|System.Double|Все|  
|System.Guid|Все|  
|System.IEnumerable<T\>|Все|  
|System.IEnumerator<T\>|Все|  
|System.Int16|Все|  
|System.Int32|Все|  
|System.Int64|Все|  
|System.Linq.Enumerable<T\>|Поддерживаются все методы|  
|System.Math|Все|  
|System.MidpointRounding|Все|  
|System.Nullable<T\>|Все|  
|System.Random|Все|  
|System.SByte|Все|  
|System.Security.Cryptography. HMACSHA384|Все|  
|System.Security.Cryptography. HMACSHA512|Все|  
|System.Security.Cryptography.HashAlgorithm|Все|  
|System.Security.Cryptography.HMAC|Все|  
|System.Security.Cryptography.HMACMD5|Все|  
|System.Security.Cryptography.HMACSHA1|Все|  
|System.Security.Cryptography.HMACSHA256|Все|  
|System.Security.Cryptography.KeyedHashAlgorithm|Все|  
|System.Security.Cryptography.MD5|Все|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Все|  
|System.Security.Cryptography.SHA1|Все|  
|System.Security.Cryptography.SHA1Managed|Все|  
|System.Security.Cryptography.SHA256|Все|  
|System.Security.Cryptography.SHA256Managed|Все|  
|System.Security.Cryptography.SHA384|Все|  
|System.Security.Cryptography.SHA384Managed|Все|  
|System.Security.Cryptography.SHA512|Все|  
|System.Security.Cryptography.SHA512Managed|Все|  
|System.Single|Все|  
|System.String|Все|  
|System.StringSplitOptions|Все|  
|System.Text.Encoding|Все|  
|System.Text.RegularExpressions.Capture|Index, Length, Value|  
|System.Text.RegularExpressions.CaptureCollection|Count, Item|  
|System.Text.RegularExpressions.Group|Captures, Success|  
|System.Text.RegularExpressions.GroupCollection|Count, Item|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result|  
|System.Text.RegularExpressions.Regex|(Конструктор) IsMatch, Match, Matches, Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|Все|  
|System.Tuple|Все|  
|System.UInt16|Все|  
|System.UInt32|Все|  
|System.UInt64|Все|  
|System.Uri|Все|  
|System.Xml.Linq.Extensions|Поддерживаются все методы|  
|System.Xml.Linq.XAttribute|Поддерживаются все методы|  
|System.Xml.Linq.XCData|Поддерживаются все методы|  
|System.Xml.Linq.XComment|Поддерживаются все методы|  
|System.Xml.Linq.XContainer|Поддерживаются все методы|  
|System.Xml.Linq.XDeclaration|Поддерживаются все методы|  
|System.Xml.Linq.XDocument|Поддерживаются все методы|  
|System.Xml.Linq.XDocumentType|Поддерживаются все методы|  
|System.Xml.Linq.XElement|Поддерживаются все методы|  
|System.Xml.Linq.XName|Поддерживаются все методы|  
|System.Xml.Linq.XNamespace|Поддерживаются все методы|  
|System.Xml.Linq.XNode|Поддерживаются все методы|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Поддерживаются все методы|  
|System.Xml.Linq.XNodeEqualityComparer|Поддерживаются все методы|  
|System.Xml.Linq.XObject|Поддерживаются все методы|  
|System.Xml.Linq.XProcessingInstruction|Поддерживаются все методы|  
|System.Xml.Linq.XText|Поддерживаются все методы|  
|System.Xml.XmlNodeType|Все|  
  
##  <a name="ContextVariables"></a> Переменная контекста  
 Переменная с именем `context` неявно доступна в каждом [выражении](api-management-policy-expressions.md#Syntax) политики. Ее члены предоставляют сведения, относящиеся к переменной `\request`. Все члены `context` доступны только для чтения.  
  
|Переменная контекста|Допустимые методы, свойства и значения параметров|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> Развертывание<br /><br /> Elapsed: TimeSpan — интервал времени между значением Timestamp и текущим временем<br /><br /> LastError<br /><br /> Операция<br /><br /> Продукт<br /><br /> Запрос<br /><br /> RequestId: Guid — уникальный идентификатор запроса<br /><br /> Ответ<br /><br /> Подписка<br /><br /> Timestamp: DateTime — время получения запроса<br /><br /> Tracing: логическое значение — указывает, включена ли трассировка <br /><br /> Пользователь<br /><br /> Variables:IReadOnlyDictionary<строка, объект><br /><br /> void Trace(message: строка)|  
|context.Api|Id: строка<br /><br /> IsRevisionCurrent: логическое значение<br /><br />  Name: строка<br /><br /> Path: строка<br /><br /> Revision: строка<br /><br /> ServiceUrl: IUrl<br /><br /> Version: строка |  
|context.Deployment|Region: строка<br /><br /> ServiceName: строка<br /><br /> Certificates: IReadOnlyDictionary<строка, X509Certificate2>|  
|context.LastError|Source: строка<br /><br /> Reason: строка<br /><br /> Message: строка<br /><br /> Scope: строка<br /><br /> Section: строка<br /><br /> Path: строка<br /><br /> PolicyId: строка<br /><br /> Дополнительные сведения о переменной context.LastError см. в разделе [Error handling](api-management-error-handling-policies.md) (Обработка ошибок).|  
|context.Operation|Id: строка<br /><br /> Method: строка<br /><br /> Name: строка<br /><br /> UrlTemplate: строка|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: логическое значение<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: строка<br /><br /> Name: строка<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: целое число?<br /><br /> SubscriptionRequired: логическое значение|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: строка<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: строка<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: строка, defaultValue: строка)|headerName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения заголовков запросов, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: целое число<br /><br /> StatusReason: строка|  
|string context.Response.Headers.GetValueOrDefault(headerName: строка, defaultValue: строка)|headerName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения заголовков ответов, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|  
|context.Subscription|CreatedTime: дата и время<br /><br /> EndDate: дата и время?<br /><br /> Id: строка<br /><br /> Key: строка<br /><br /> Name: строка<br /><br /> PrimaryKey: строка<br /><br /> SecondaryKey: строка<br /><br /> StartDate: дата и время?|  
|context.User|Email: строка<br /><br /> FirstName: строка<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: строка<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: строка<br /><br /> Note: строка<br /><br /> RegistrationDate: дата и время|  
|IApi|Id: строка<br /><br /> Name: строка<br /><br /> Path: строка<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: строка<br /><br /> Name: строка|  
|IMessageBody|As<T\>(preserveContent: логическое значение = false). Where T: строка, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Методы `context.Request.Body.As<T>` и `context.Response.Body.As<T>` используются для чтения текста сообщения запроса или ответа в одном из заданных форматов `T`. По умолчанию метод использует исходный поток текста сообщения и делает его недоступным после возвращения. Чтобы избежать этого, задайте для параметра `preserveContent` значение `true`, чтобы метод работал с копией потока текста. Перейдите по [этой](api-management-transformation-policies.md#SetBody) ссылке, чтобы увидеть пример.|  
|IUrl|Host: строка<br /><br /> Path: строка<br /><br /> Port: целое число<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: строка<br /><br /> Scheme: строка|  
|IUserIdentity|Id: строка<br /><br /> Provider: строка|  
|ISubscriptionKeyParameterNames|Header: строка<br /><br /> Query: строка|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: строка, defaultValue: строка)|queryParameterName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает разделенные запятыми значения параметров запроса или значение `defaultValue`, если параметр не найден.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: строка, defaultValue: T)|variableName: строка<br /><br /> defaultValue: T<br /><br /> Возвращает значение переменной, приведенное к типу `T` или `defaultValue`, если переменная не найдена.<br /><br /> Этот метод выдает исключение, если указанный тип не соответствует фактическому типу возвращаемой переменной.|  
|BasicAuthCredentials AsBasic(input: this string)|input: строка<br /><br /> Если входной параметр содержит допустимое значение заголовка запроса авторизации для обычной проверки подлинности HTTP, метод возвращает объект типа `BasicAuthCredentials`; в противном случае метод возвращает значение NULL.|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: строка<br /><br /> result: выходное значение BasicAuthCredentials<br /><br /> Если входной параметр содержит допустимое значение заголовка запроса авторизации для обычной проверки подлинности и HTTP, метод возвращает значение `true`, а параметр результата содержит значение типа `BasicAuthCredentials`. В противном случае метод возвращает значение `false`.|  
|BasicAuthCredentials|Password: строка<br /><br /> UserId: строка|  
|Jwt AsJwt(input: this string)|input: строка<br /><br /> Если входной параметр содержит допустимое значение маркера JWT, метод возвращает объект типа `Jwt`; в противном случае метод возвращает значение `null`.|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: строка<br /><br /> result: выходное значение типа Jwt<br /><br /> Если входной параметр содержит допустимое значение маркера JWT, метод возвращает значение `true`, а параметр результата содержит значение типа `Jwt`; в противном случае метод возвращает значение `false`.|  
|Jwt|Algorithm: строка<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: дата и время?<br /><br /> Id: строка<br /><br /> Issuer: строка<br /><br /> NotBefore: дата и время?<br /><br /> Subject: строка<br /><br /> Type: строка|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения утверждений, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|
|byte[] Encrypt(input: this byte[], alg: строка, key:byte[], iv:byte[])|input — открытый текст, который нужно зашифровать<br /><br />alg — имя алгоритма симметричного шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input — открытый текст, который нужно зашифровать<br /><br />alg — алгоритм шифрования<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input — открытый текст, который нужно зашифровать<br /><br />alg — алгоритм шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Decrypt(input: this byte[], alg: строка, key:byte[], iv:byte[])|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — имя алгоритма симметричного шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — алгоритм шифрования<br /><br />Возвращает открытый текст.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — алгоритм шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст.|


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).   

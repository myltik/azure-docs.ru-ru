---
title: Политики междоменного доступа в службе управления API Azure | Документация Майкрософт
description: Сведения о политиках междоменного доступа, доступных для использования в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 590831454e8a18678e357b4824eb35a717d1fee0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129040"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Междоменные политики службы управления API).
В этой статье рассматриваются приведенные ниже политики управления API. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в управлении API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a> Политики междоменного доступа  
  
-   [Разрешение кросс-доменных вызовов](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – делает API доступным из клиентов на основе браузеров Adobe Flash и Microsoft Silverlight.  
-   [CORS](api-management-cross-domain-policies.md#CORS) – добавляет поддержку общего доступа к ресурсам независимо от источника (CORS) для операции или API, чтобы разрешить кросс-доменные вызовы от клиентов на основе браузера.  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) – добавляет поддержку JSON с заполнением (JSONP) для операции или API, чтобы разрешить кросс-доменные вызовы из браузерных клиентов JavaScript.  
  
##  <a name="AllowCrossDomainCalls"></a> Разрешение междоменных вызовов  
 Используйте политику `cross-domain`, чтобы разрешить доступ к API из браузерных клиентов на базе Adobe Flash и Microsoft Silverlight.  
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Пример  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Элементы  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|  
|----------|-----------------|--------------|  
|cross-domain|Корневой элемент. Дочерние элементы должны соответствовать [спецификации Adobe для файлов политики междоменного доступа](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Yes|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
-   **Области политики:** global.  
  
##  <a name="CORS"></a> CORS  
 Политика `cors` добавляет поддержку общего доступа к ресурсам независимо от источника (CORS) для операции или API, чтобы разрешить междоменные вызовы из браузерных клиентов.  
  
 CORS позволяет браузеру и серверу взаимодействовать друг с другом и определять, разрешать конкретные запросы независимо от источника или нет (то есть, вызовы XMLHttpRequests, отправляемые из кода JavaScript на веб-странице в другие домены). Это обеспечивает большую гибкость, чем просто разрешение запросов из одного источника, и более высокую защищенность, чем разрешение всех запросов независимо от источника.  
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<cors allow-credentials="false|true">  
    <allowed-origins>  
        <origin>origin uri</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="number of seconds">  
        <method>http verb</method>  
    </allowed-methods>  
    <allowed-headers>  
        <header>header name</header>  
    </allowed-headers>  
    <expose-headers>  
        <header>header name</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="example"></a>Пример  
 В этом примере показано, как обеспечить поддержку предварительных запросов, например с пользовательскими заголовками или методами, отличными от GET и POST. Для поддержки пользовательских заголовков и дополнительных команд HTTP используйте разделы `allowed-methods` и `allowed-headers`, как показано в следующем примере.  
  
```xml  
<cors allow-credentials="true">  
    <allowed-origins>  
        <!-- Localhost useful for development -->  
        <origin>http://localhost:8080/</origin>  
        <origin>http://example.com/</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="300">  
        <method>GET</method>  
        <method>POST</method>  
        <method>PATCH</method>  
        <method>DELETE</method>  
    </allowed-methods>  
    <allowed-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
        <header>x-zumo-version</header>  
        <header>x-zumo-auth</header>  
        <header>content-type</header>  
        <header>accept</header>  
    </allowed-headers>  
    <expose-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="elements"></a>Элементы  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|cors|Корневой элемент.|Yes|Недоступно|  
|allowed-origins|Содержит элементы `origin`, описывающие разрешенные источники для междоменных запросов. `allowed-origins` может содержать один элемент `origin` со значением `*`, если нужно разрешить любые источники, либо один или несколько элементов `origin`, содержащих URI источников.|Yes|Недоступно|  
|origin|В качестве значений допускается `*`, которое разрешает любые источники, или URI конкретного источника. URI-адрес должен включать схему, узел и порт.|Yes|Если в URI не указан порт, используется порт 80 для HTTP и порт 443 для HTTPS.|  
|allowed-methods|Этот элемент является обязательным, если разрешены методы, отличные от GET или POST. Содержит элементы `method`, перечисляющие поддерживаемые HTTP-команды.|Нет |Если этот раздел отсутствует, поддерживаются методы GET и POST.|  
|метод|Задает команду HTTP.|Если раздел `allowed-methods` присутствует, в нем обязательно должен быть по крайней мере один элемент `method`.|Недоступно|  
|allowed-headers|Этот элемент содержит элементы `header`, указывающие имена заголовков, которые могут быть включены в запрос.|Нет |Недоступно|  
|expose-headers|Этот элемент содержит элементы `header`, указывающие имена заголовков, которые будут доступны клиенту.|Нет |Недоступно|  
|Верхний колонтитул|Задает имя заголовка.|Если присутствует раздел `allowed-headers` или `expose-headers`, в нем обязательно должен быть по крайней мере один элемент `header`.|Недоступно|  
  
### <a name="attributes"></a>Атрибуты  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|allow-credentials|Заголовок `Access-Control-Allow-Credentials` в ответе на предварительный запрос получит значение, указанное в этом атрибуте. Этот заголовок определяет, может ли клиент передавать в междоменных запросах учетные данные.|Нет |false|  
|preflight-result-max-age|Заголовок `Access-Control-Max-Age` в ответе на предварительный запрос получит значение, указанное в этом атрибуте. Этот заголовок определяет, может ли клиент кэшировать ответы на предварительные запросы.|Нет |0|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
-   **Области политики:** API, operation.  
  
##  <a name="JSONP"></a> JSONP  
 Политика `jsonp` добавляет поддержку JSON с заполнением (JSONP) для операции или API, чтобы разрешить междоменные вызовы из браузерных клиентов на основе JavaScript. JSONP — это метод, который используется в программах JavaScript для запроса данных из сервера в другом домене. JSONP обходит ограничение, принудительно устанавливаемое большинством веб-браузеров, когда доступ к веб-страницам должен выполняться в том же домене.  
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Пример  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Если метод вызывается без параметра обратного вызова ?cb=XXX, будет возвращаться простой JSON (без оболочки для вызовов функции).  
  
 Если добавить к запросу параметр обратного вызова `?cb=XXX`, возвращается результат JSONP, заключающий в исходные результаты JSON функцию обратного вызова, например, `XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Элементы  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|  
|----------|-----------------|--------------|  
|jsonp|Корневой элемент.|Yes|  
  
### <a name="attributes"></a>Атрибуты  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|callback-parameter-name|Кросс-доменный вызов функции JavaScript, в качестве префикса в котором присутствует полное имя домена, в котором находится функция.|Yes|Недоступно|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** outbound.  
-   **Области политики:** global, product, API, operation.  
  
## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).   
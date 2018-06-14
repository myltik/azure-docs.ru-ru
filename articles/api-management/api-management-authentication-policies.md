---
title: Политики аутентификации в службе управления API Azure | Документация Майкрософт
description: Сведения о политиках аутентификации, доступных для использования в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4d13d9dbea9da9db5bfe9a9af85fdbf9eab1ae84
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
ms.locfileid: "26127765"
---
# <a name="api-management-authentication-policies"></a>Политики аутентификации в службе управления API
В этой статье рассматриваются приведенные ниже политики управления API. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в управлении API](http://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Политики аутентификации  
  
-   [Обычная проверка подлинности](api-management-authentication-policies.md#Basic) – обычная проверка подлинности внутренней службы.  
  
-   [Аутентификация с помощью сертификата клиента](api-management-authentication-policies.md#ClientCertificate) – аутентификация внутренней службы с помощью сертификатов клиентов.  
  
##  <a name="Basic"></a> Обычная проверка подлинности  
 Используйте политику `authentication-basic` для обычной проверки подлинности внутренней службы. Эта политика задает для заголовка авторизации HTTP значение, соответствующее учетным данным, предоставленным в политике.  
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Пример  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Элементы  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|  
|----------|-----------------|--------------|  
|authentication-basic|Корневой элемент.|Yes|  
  
### <a name="attributes"></a>Атрибуты  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|Имя пользователя|Задает имя пользователя для обычных учетных данных.|Yes|Недоступно|  
|password|Задает пароль для обычных учетных данных.|Yes|Недоступно|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики:** API.  
  
##  <a name="ClientCertificate"></a> Аутентификация с помощью сертификата клиента  
 Используйте политику `authentication-certificate` для аутентификации внутренней службы с помощью сертификата клиента. Сертификат сначала должен быть [установлен в службу управления API](http://go.microsoft.com/fwlink/?LinkID=511599) и идентифицирован по его отпечатку.  
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Пример  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Элементы  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|  
|----------|-----------------|--------------|  
|authentication-certificate|Корневой элемент.|Yes|  
  
### <a name="attributes"></a>Атрибуты  
  
|ИМЯ|ОПИСАНИЕ|Обязательно|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Отпечаток для сертификата клиента.|Yes|Недоступно|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики:** API.  
  

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).   

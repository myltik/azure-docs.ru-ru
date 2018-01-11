---
title: "Ошибка обработки советы и рекомендации для клиентов Azure Active Directory Authentication библиотеки (ADAL)"
description: "Обеспечивает обработку советы и рекомендации для ADAL клиентских приложений ошибок."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mtillman
ms.author: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.custom: 
ms.openlocfilehash: b6cf7bbb1ae41fcdf16601af87ec1b573866639a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Ошибка обработки советы и рекомендации для клиентов Azure Active Directory Authentication библиотеки (ADAL)

Эта статья содержит рекомендации от типа ошибки, разработчики могут возникнуть при использовании ADAL для проверки подлинности пользователей. При использовании ADAL, существует несколько случаев, когда разработчик может потребоваться шага в и обработки ошибок. Обработку ошибок обеспечивает значительные пользователей и ограничивает количество раз, когда конечный пользователь должен выполнить вход.

В этой статье мы исследуем конкретных случаев для каждой платформы, поддерживаемые ADAL и как приложение сможет обработать каждый случай должным образом. Ошибка инструкции разбивается на две категории широкой, на основе шаблонов получение токена ADAL API-интерфейсы, предоставляемые:

- **AcquireTokenSilent**: клиент пытается получить токен без вмешательства пользователя без пользовательского интерфейса и может завершиться ошибкой, если ADAL не выполнена. 
- **AcquireToken**: клиент может попытаться выполнить получение без уведомления, но можно также выполнить интерактивных запросов, которые требуется вход.

> [!TIP]
> Рекомендуется для входа всех ошибок и исключений при использовании ADAL и Azure AD. Журналы не только полезен для понимания общей работоспособности приложения, но также важны при отладке более широкой проблем. Хотя приложение может восстановиться после некоторых ошибок, они могут подсказывайте широкой проблем проектирования, которые требуют изменений кода, чтобы разрешить. 
> 
> При реализации условий ошибки, описанные в этом документе, необходимо войти в код ошибки и описание по причинам, уже было сказано ранее. В разделе [ведения журнала Справочник по ошибкам и](#error-and-logging-reference) примеры кода для ведения журнала. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent предпринимает попытку получить маркер с гарантии, что конечный пользователь не видит интерфейса (UI). Существует несколько случаев, где получение без уведомления может завершиться ошибкой и необходимо обработать с помощью интерактивных запросов или обработчик по умолчанию. Перейти в особенности когда и как использовать каждый вариант в последующих разделах.

Имеется ряд ошибок, создаваемых операционной системы, что может потребовать конкретного приложения обработки ошибок. Дополнительные сведения см. в разделе «Операционная система» раздел ошибок в [ведения журнала Справочник по ошибкам и](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Сценарии приложений

- [Собственный клиент](active-directory-dev-glossary.md#native-client) приложений (iOS, Android, рабочего стола .NET или Xamarin)
- [Веб-клиент](active-directory-dev-glossary.md#web-client) приложения, вызывающие [ресурсов](active-directory-dev-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Ошибка случаи и шаги пригодных к использованию

По существу существует два варианта AcquireTokenSilent ошибок:

| Случай | ОПИСАНИЕ |
|------|-------------|
| **Вариант 1**: Ошибка разрешается интерактивного входа в систему | Для ошибок, вызванных нехваткой допустимые токены интерактивный запрос не нужны. В частности поиск по кэшу и маркер обновления недопустимый либо просроченный необходимо вызывать AcquireToken для решения.<br><br>В таких случаях пользователю необходимо запрос на вход. Приложение можно сделать интерактивный запрос немедленно, после взаимодействия с пользователем (например, при нажатии кнопки входа) или более поздней версии. Выбор зависит от требуемого поведения приложения.<br><br>См. в следующем разделе для этого регистра, языка и ошибок, которые ее диагностики кода.|
| **Вариант 2**: ошибка не удается разрешить интерактивного входа в систему | Для сетевой и временной или временные ошибки или другого сбоя выполнение запроса интерактивный AcquireToken не разрешить проблему. Ненужные интерактивный вход запросы также можно упустить конечных пользователей. ADAL автоматически пытается один повторных попыток для большинства ошибок при сбоях AcquireTokenSilent.<br><br>Клиентское приложение также может попытаться выполнить повторную попытку позже, но когда и как это сделать зависит от поведением приложения и взаимодействие с пользователем требуемой. Например приложение можно сделать AcquireTokenSilent, повторите попытку через несколько минут или в ответ на какие-либо действия конечных пользователей. Немедленная повторная попытка приведет к регулируется приложения и не будет предпринято.<br><br>Последующей повторной попыткой же ошибка не означает, что клиент делать интерактивный запрос, с помощью AcquireToken, как ее устранить ошибку.<br><br>См. в следующем разделе для этого регистра, языка и ошибок, которые ее диагностики кода. |

### <a name="net"></a>.NET

В следующем руководстве представлена примеры для обработки событий в сочетании с методами ADAL: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [deprecated] acquireTokenSilent(...)
- [deprecated] acquireTokenByRefreshToken(...) 

Код будет реализован следующим образом:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request.  
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

В следующем руководстве представлена примеры для обработки событий в сочетании с методами ADAL: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [deprecated] acquireTokenSilent(...)

Код будет реализован следующим образом:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

В следующем руководстве представлена примеры для обработки событий в сочетании с методами ADAL: 

- acquireTokenSilentWithResource(...)

Код будет реализован следующим образом:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application.  

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken является ADAL метод по умолчанию, используемый для получения маркеров. В случаях, когда требуется удостоверение пользователя AcquireToken пытается получить токен автоматически первой, а затем отображает пользовательский Интерфейс при необходимости (Если передается PromptBehavior.Never). В случаях, когда требуется удостоверение приложения AcquireToken пытается получить маркер, но не отображать пользовательский Интерфейс, так как нет конечных пользователей.  

При обработке ошибок AcquireToken, обработки ошибок зависит от платформы и сценарий приложения требуется достичь.  

Операционная система также можно создать набор ошибок, требующих обработки зависит от конкретного приложения ошибок. Дополнительные сведения см. в разделе «Ошибки операционной системы» в [ведения журнала Справочник по ошибкам и](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Сценарии приложений

- Собственные клиентские приложения (iOS, Android, рабочего стола .NET или Xamarin)
- Веб-приложений, которые вызывают ресурсов API (.NET)
- Одностраничные приложения (Javascript)
- Приложения службы (.NET, Java)
  - Все сценарии, включая от имени представляет
  - От имени представляет конкретных сценариев

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Ошибка случаи и шаги пригодных к использованию: собственные клиентские приложения

При создании собственного клиентского приложения, существует несколько вариантов обработки ошибок необходимо учитывать которого связаны с проблемы с сетью, временные сбои и других ошибок, специфический для платформы. В большинстве случаев приложения не следует выполнять немедленных повторных попыток, но вместо ожидания для взаимодействия с пользователем, будет предложено войти.  

Существует несколько особых случаев, в которых один повторов может устранить проблему. Например, при пользователь должен включать данные на устройстве, или завершить broker Azure AD после сбоя начальной загрузки. 

В случае сбоя приложения могут обеспечивать пользовательского интерфейса, чтобы разрешить конечным пользователям выполнять некоторые взаимодействия, который запрашивает повторную попытку. Для экземпляра устройство не ошибка вне сети, запроса AcquireToken кнопки «Попробуйте выполнить вход еще раз» повторно вместо немедленно повторять сбоя. 

Обработка ошибок в собственных приложений можно определить в двух случаях:

|  |  |
|------|-------------|
| **Вариант 1**:<br>Non-Повторяемая ошибка (в большинстве случаев) | 1. Не пытайтесь немедленных повторных. Предоставить конечному пользователю пользовательского интерфейса в зависимости от конкретной ошибки, вызывает повторную попытку («Повторите попытку входа» «загрузить Azure AD broker приложения», и т. д). |
| **Вариант 2**:<br>Повторяемая ошибка | 1. Выполните один "Повторить", так как конечный пользователь ввел состояние, которое приведет к неудаче.<br><br>2. Если не удается повторить, присутствует конечным пользователем, пользовательского интерфейса в зависимости от конкретной ошибки, вызывает повторную попытку («Повторите попытку входа», «Загрузить Azure AD broker приложение», и т. д.). |

> [!IMPORTANT]
> Если учетная запись пользователя передается в ADAL автоматической вызова и завершается неудачей, последующие интерактивный запрос позволяет конечного пользователя для входа в другой учетной записью. После успешного AcquireToken, с помощью учетной записи пользователя приложение должно убедитесь, что вошедшего пользователя совпадает с объектом локального пользователя приложения. Несоответствие не вызывает исключение (за исключением Objective C), но следует учитывать, если известен пользователя является локально перед запросы проверки подлинности (например, ошибки автоматического вызова).
>

#### <a name="net"></a>.NET

Следующие рекомендации по примеры в сочетании с AcquireToken(...) все без автоматической обработки ошибок ADAL методы *за исключением*: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(...,UserAssertion,...)   

Код будет реализован следующим образом:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> ADAL .NET имеет это дополнительный фактор, влияющий, как он поддерживает PromptBehavior.Never, имеющая поведение, например AcquireTokenSilent.
>

В следующем руководстве представлена примеры для обработки событий в сочетании с методами ADAL: 

- acquireToken(..., PromptBehavior.Never)

Код будет реализован следующим образом:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Следующие рекомендации по примеры в сочетании с AcquireToken(...) все без автоматической обработки ошибок ADAL методы. 

Код будет реализован следующим образом:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Следующие рекомендации по примеры в сочетании с AcquireToken(...) все без автоматической обработки ошибок ADAL методы. 

Код будет реализован следующим образом:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Ошибка случаи и шаги пригодных к использованию: веб-приложений, которые вызывают ресурсов API (.NET)

При создании веб-приложении .NET, который вызывает возвращает маркер, с помощью кода авторизации для ресурса, обработчик по умолчанию в случае универсальных находится только код, необходимый. 

В следующем руководстве представлена примеры для обработки событий в сочетании с методами ADAL: 

- AcquireTokenByAuthorizationCodeAsync(...)

Код будет реализован следующим образом:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Ошибка случаи и шаги пригодных к использованию: приложений на одной странице (adal.js)

Если вы создаете приложение на одной странице с помощью adal.js с AcquireToken, код обработки ошибок аналогична типичные автоматического вызова.  В частности в adal.js AcquireToken никогда не отображает пользовательский Интерфейс. 

Не удалось AcquireToken состоит из следующих случаев.

|  |  |
|------|-------------|
| **Вариант 1**:<br>Разрешить интерактивный запрос | 1. Если происходит сбой login(), не выполняйте немедленных повторных. Только повторно после действия пользователя запрашивает повторную попытку.|
| **Вариант 2**:<br>Не Resolvable с интерактивного запроса. Ошибки можно выполнить повторно. | 1. Выполните один "Повторить", так как основной пользователь введенные состояние, которое приведет к неудаче.<br><br>2. В случае повтора предоставить пользователю действием конкретную ошибку, можно вызвать повторную попытку («попробуйте снова выполнить вход»). |
| **Вариант 3**:<br>Не Resolvable с интерактивного запроса. Ошибки действие повторить нельзя. | 1. Не пытайтесь немедленных повторных. Предоставить пользователю действием конкретную ошибку, можно вызвать повторную попытку («попробуйте снова выполнить вход»). |

Код будет реализован следующим образом:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Ошибка случаи и шаги пригодных к использованию: приложений служб (.NET)

При создании приложения службы, использующего AcquireToken существует несколько код должен обрабатывать ошибки ключа. Единственным средством для сбоя является возвращает ошибку, вернитесь к вызывающему приложению (для случаев от имени представляет) или применить стратегию повторов. 

#### <a name="all-scenarios"></a>Все сценарии

Для *все* сценариев приложений служб, включая от имени представляет:

- Не пытайтесь немедленная повторная попытка. ADAL попыток один повторите для некоторых неудачных запросов. 
- Только продолжайте повторять действие пользователя или приложения после приглашения повторить операцию. Например управляющая программа приложение, которое работает на некоторое время набора должно дождаться следующего интервала, чтобы повторить попытку.

В следующем руководстве представлена примеры для обработки событий в сочетании с методами ADAL: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)

Код будет реализован следующим образом:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>От имени представляет сценариев

Для *от имени представляет* сценарии приложений служб.

В следующем руководстве представлена примеры для обработки событий в сочетании с методами ADAL: 

- AcquireTokenAsync (..., UserAssertion,...)

Код будет реализован следующим образом:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Мы создали [полный пример](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) , демонстрирующий этот сценарий.

## <a name="error-and-logging-reference"></a>Справочник ошибок и ведение журнала

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Ошибки библиотеки ADAL

Для изучения конкретных ошибок ADAL, исходный код в [хранилища azure activedirectory библиотека для dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) является лучшим Справочник ошибок.

#### <a name="guidance-for-error-logging-code"></a>Руководство по код ведения журнала ошибок

Ведение журнала ADAL .NET изменяется в зависимости от платформы, которые обрабатываются. Ссылаться на [ведения журнала документации](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet#diagnostics) для кода, о том, как включить ведение журнала.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Ошибки библиотеки ADAL

Для изучения конкретных ошибок ADAL, исходный код в [хранилища azure — activedirectory библиотека for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) является лучшим Справочник ошибок.

#### <a name="operating-system-errors"></a>Ошибки операционной системы

Android ошибки операционной системы, предоставляются через AuthenticationException в ADAL, идентифицируются как «SERVER_INVALID_REQUEST» и может быть дополнительно детальные через описания ошибок. Приведены два Показательным сообщения, которые приложение может отображать пользовательский Интерфейс.

- Ошибки по протоколу SSL 
  - [Конечный пользователь использует Chrome 53](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/SSL-Certificate-Validation-Issue)
  - [Цепочка сертификатов имеет cert, помеченных как Дополнительные загрузки (пользователь должен обратитесь к ИТ-администратору)](https://vkbexternal.partners.extranet.microsoft.com/VKBWebService/ViewContent.aspx?scid=KB;EN-US;3203929)
  - Корневой центр сертификации не является доверенным для устройства. Обратитесь к ИТ-администратору. 
- Связанные ошибки сети 
  - [Сетевая проблема, потенциально связанных с проверкой сертификата SSL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/SSL-Certificate-Validation-Issue), попробуйте повторить операцию один

#### <a name="guidance-for-error-logging-code"></a>Руководство по код ведения журнала ошибок

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode.     
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Ошибки библиотеки ADAL

Для изучения конкретных ошибок ADAL, исходный код в [хранилища azure — activedirectory библиотека для objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) является лучшим Справочник ошибок.

#### <a name="operating-system-errors"></a>Ошибки операционной системы

Если пользователи используют веб-представления и характер проверки подлинности, во время входа могут возникнуть ошибки операций ввода-вывода. Это может быть вызвано условий, например SSL ошибок, время ожидания или ошибки сети:

- Для совместного использования правах имена входа не являются постоянными и кэш становится пустым. Можно разрешить, добавив следующий код к цепочке ключей:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Набор NsUrlDomain ошибок действие изменяется в зависимости от логики приложения. В разделе [NSURLErrorDomain справочной документации](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) для определенных экземпляров, которые могут быть обработаны.
- В разделе [ADAL распространенных проблем Obj-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) список типичных ошибок обслуживается команда ADAL для Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Руководство по код ведения журнала ошибок

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Рекомендации для ведения журнала ошибок кода - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```
## <a name="related-content"></a>Связанная информация

* [Руководство разработчика по Azure Active Directory][AAD-Dev-Guide]
* [Библиотеки проверки подлинности Azure AD][AAD-Auth-Libraries]
* [Сценарии проверки подлинности Azure AD][AAD-Auth-Scenarios]
* [Интеграция приложений с Azure Active Directory][AAD-Integrating-Apps]

Используйте раздел комментарии, ниже, чтобы отправить отзыв и помогают уточнить и отформатировать содержимое веб-узла.

<!--Reference style links -->
[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[! [Вход кнопки] [AAD-Sign-In]] [AAD-Sign-In] [AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png


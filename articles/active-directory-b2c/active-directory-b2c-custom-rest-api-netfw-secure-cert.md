---
title: "Azure Active Directory B2C. Защита служб RESTful с помощью сертификатов клиента"
description: "Пример действий по защите пользовательского обмена утверждениями REST API в системе Azure AD B2C с помощью сертификатов клиента"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C. Защита служб RESTful с помощью сертификатов клиента
После [создания службы RESTful](active-directory-b2c-custom-rest-api-netfw.md) для взаимодействия с Azure AD B2C нужно научиться ограничивать доступ к веб-приложениям Azure (REST API) с помощью сертификата клиента. Этот механизм известен как взаимная аутентификация TLS или **аутентификация по сертификату клиента**.  Доступ к защищенной службе смогут получить только службы (например, Azure AD B2C), у которых есть соответствующий сертификат.

> [!NOTE]
>
>Также для защиты службы RESTful можно [применить обычную аутентификацию HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Но не забывайте, что обычная аутентификация считается менее безопасной, чем использование сертификатов клиента. Рекомендуем применять для защиты службы RESTful аутентификацию по сертификату клиента, как описано в этой статье.

В эта статье описаны следующие действия:
1. Настройка веб-приложения для использования аутентификации по сертификату клиента.
1. Отправка сертификата в хранилище ключей политики Azure AD B2C.
1. Настройка пользовательских политик для использования сертификата клиента.

## <a name="prerequisites"></a>Предварительные требования
* Выполните все предварительные действия, как описано в статье об[интеграции обмена утверждениями REST API](active-directory-b2c-custom-rest-api-netfw.md).
* Получите действительный сертификат (PFX-файл с закрытым ключом).

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>Шаг 1. Настройка веб-приложения для аутентификации по сертификату клиента
Чтобы в **службе приложений Azure** подавался запрос на сертификат клиента, параметр `clientCertEnabled` для веб-приложения должен иметь значение true. В настоящее время этот параметр недоступен в интерфейсе управления на портале Azure и его можно изменить только при помощи REST API.

> [!NOTE]
>
>Ваш план службы приложений Azure должен иметь уровень "Стандартный" или выше. Дополнительные сведения см. в статье [Подробный обзор планов службы приложений Azure](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Используйте [обозреватель ресурсов Azure (предварительная версия)](https://resources.azure.com), чтобы установить значение true для свойства clientCertEnabled. На следующем снимке экрана показано, как задать значение для clientCertEnabled с помощью обозревателя ресурсов Azure. ![Параметр clientCertEnabled в интерфейсе обозревателя ресурсов Azure](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>Дополнительные сведения по атрибуту clientCertEnabled см. в статье [Настройка взаимной проверки подлинности TLS для веб-приложения](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth).


> [!TIP]
>
>Кроме того, для быстрого создания вызова REST API можно воспользоваться [средством ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Шаг 2. Отправка сертификата в хранилище ключей политики Azure AD B2C
Когда вы установите для `clientCertEnabled` значение `true`, обмен данными с RESTful API будет выполняться только при наличии сертификата клиента. Этот сертификат клиента нужно предоставить, то есть отправить в клиент Azure AD B2C и сохранить в нем.   
1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Отправить**.
5.  Для параметра **Имя** используйте значение `B2cRestClientCertificate`.  
    Префикс `B2C_1A_` добавляется автоматически.
6.  В поле **Отправка файлов** выберите PFX-файл сертификата с закрытым ключом. Также введите **пароль** сертификата.

    ![Отправка ключа политики](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  Нажмите кнопку **Создать**
8.  Чтобы проверить создание ключа `B2C_1A_B2cRestClientCertificate`, выберите раздел **Ключи политики** для просмотра всех доступных в клиенте ключей.

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>Шаг 3. Изменение `TechnicalProfile` для поддержки аутентификации по сертификату клиента в политике расширения
1.  Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога.
2.  Найдите узел `<TechnicalProfile>`, который включен с идентификатором `Id="REST-API-SignUp"`.
3.  Найдите элемент `<Metadata>`.
4.  Замените `AuthenticationType` на `ClientCertificate`.

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  Добавьте следующий XML-фрагмент сразу после завершения элемента `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

Когда вы добавите XML-фрагмент, узел `TechnicalProfile` должен выглядеть так:

![Настройка XML-элементов ClientCertificate для аутентификации](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Шаг 4. Отправка политики в клиент

1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и щелкните **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Щелкните **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Шаг 5. Тестирование настраиваемой политики с помощью команды Run Now (Запустить сейчас)
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.

    >[!NOTE]
    >
    >    Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. 
    >    Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.

3.  Попробуйте ввести Test в поле **Имя**. В верхней части страницы B2C отобразится сообщение об ошибке.

    ![Проверка API для идентификации](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Попробуйте ввести в поле **Имя** другое имя (не test). B2C зарегистрирует нового пользователя и отправит в ваше приложение номер loyaltyNumber. Найдите этот номер в примере для объекта JWT ниже.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

   > [!NOTE]
   >Если отобразится указанное выше сообщение об ошибке, значит, Azure AD B2C успешно обращается к службе RESTful и предоставляет сертификат клиента. Теперь нам нужно проверить этот сертификат.

## <a name="step-6-adding-certificate-validation"></a>Шаг 6. Добавление проверки сертификата
Сертификат клиента, который Azure AD B2C отправляет в службу RESTful, не проходит никаких проверок на платформе веб-приложений Azure (проверяется только наличие сертификата). За проверку этого сертификата отвечает веб-приложение. Ниже приведен пример кода ASP.NET, который проверяет свойства сертификата для проверки подлинности.

> [!NOTE]
>Дополнительные сведения о том, как настроить проверку подлинности сертификата клиента в приложении службы Azure, см. в статье [Настройка взаимной проверки подлинности TLS для веб-приложения](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1. Добавление параметров приложения в файл web.config проекта
Откройте созданный ранее проект Visual Studio и добавьте указанные ниже параметры приложения в файл web.config, который хранится в элементе `appSettings`.

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Замените эти значения сведениями о сертификате:
* имя субъекта;
* имя издателя;
* отпечаток сертификата.

### <a name="62-add-isvalidclientcertificate-function"></a>6.2. Добавление функции проверки сертификата (IsValidClientCertificate)
Откройте файл Controllers\IdentityController.cs и добавьте указанную ниже функцию в класс контроллера `Identity`. 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

В **примере выше** мы принимаем сертификат как допустимый только в том случае, если выполняются все перечисленные условия.
1. Сертификат не истек и является активным на момент текущего времени сервера.
2. Имя `Subject` сертификата имеет значение, совпадающее со значением параметра `ClientCertificate:Subject` для приложения.
3. Имя `Issuer` сертификата имеет значение, совпадающее со значением параметра `ClientCertificate:Issuer` для приложения.
4. Отпечаток `thumbprint` сертификата совпадает со значением параметра `ClientCertificate:Thumbprint` для приложения.

> [!IMPORTANT]
>
>В зависимости от уровня конфиденциальности службы могут потребоваться дополнительные проверки. Например, вы можете проверять связь сертификата с доверенным корневым центром сертификации, имя организации издателя и т. д.

### <a name="63-add-isvalidclientcertificate-function"></a>6.3. Добавление функции проверки клиента (IsValidClientClient)
Откройте файл Controllers\IdentityController.cs и добавьте приведенные ниже строки кода в начале функции `SignUp()`. 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Когда вы добавите указанный фрагмент кода, контроллер `Identity` должен выглядеть так:

![Добавление кода для проверки сертификата](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>Шаг 7. Публикация в Azure и тестирование
1. Щелкните правой кнопкой мыши проект **Contoso.AADB2C.API** в **обозревателе решений** и выберите **Опубликовать**.
2. Повторите шаг 6 и снова протестируйте пользовательскую политику с проверкой сертификата. Попробуйте запустить эту политику и убедитесь, что после добавления проверки все работает правильно.
3. Теперь в файле web.config измените значение `ClientCertificate:Subject` на **invalid**. Запустите политику еще раз. Должно появиться сообщение об ошибке.
4. Верните параметру значение **valid** и снова проверьте, успешно ли политика обращается к REST API.
5. Устранение неполадок за счет [сбора журналов с помощью Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>Скачивание полного текста файлов политики и кода (необязательно)
* Советуем создать свой сценарий и собственные файлы пользовательской политики, а не использовать эти примеры файлов. Для этого вам будет полезно изучить пошаговое руководство по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* Вы можете загрузить полный исходный код примера для Visual Studio [отсюда](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).
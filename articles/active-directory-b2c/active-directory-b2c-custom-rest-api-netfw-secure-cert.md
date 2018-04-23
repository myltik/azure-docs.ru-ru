---
title: Azure Active Directory B2C. Защита служб RESTful с помощью сертификатов клиента
description: Защита пользовательского обмена утверждениями REST API в системе Azure AD B2C с помощью сертификатов клиента.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.openlocfilehash: fb5c8a48a676a2909cce3c4d126218e48a4c8ce2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Защита служб RESTful с помощью сертификатов клиента

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В связанной статье вы [создадите службу RESTful](active-directory-b2c-custom-rest-api-netfw.md), взаимодействующую с Azure Active Directory B2C (Azure AD B2C).

Из этой статьи вы узнаете, как ограничить доступ к веб-приложению Azure (RESTful API) с помощью сертификата клиента. Этот механизм известен как взаимная проверка подлинности TLS или *проверка подлинности сертификата клиента*. Только службы, у которых есть соответствующие сертификаты, например Azure AD B2C, могут получить доступ к вашей службе.

>[!NOTE]
>Для защиты службы RESTful также можно [применить обычную проверку подлинности HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Тем не менее обычная проверка подлинности HTTP считается менее безопасной, чем использование сертификатов клиента. Рекомендуем применять для защиты службы RESTful проверку подлинности по сертификату клиента, как описано в этой статье.

В эта статье описаны следующие действия:
* Настройка веб-приложения для использования проверки подлинности по сертификату клиента.
* Отправка сертификата в хранилище ключей политики Azure AD B2C.
* Настройка пользовательских политик для использования сертификата клиента.

## <a name="prerequisites"></a>предварительным требованиям
* Выполните действия, описанные в статье [Azure Active Directory B2C. Интеграция обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных](active-directory-b2c-custom-rest-api-netfw.md).
* Получите действительный сертификат (PFX-файл с закрытым ключом).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Шаг 1. Настройка веб-приложения для проверки подлинности по сертификату клиента
Чтобы в **службе приложений Azure** подавался запрос на сертификат клиента, установите для параметра `clientCertEnabled` веб-приложения значение *True*. Чтобы внести это изменение, используйте REST API. Этот параметр доступен в интерфейсе управления на портале Azure. Чтобы найти параметр, в меню **Параметры** приложения RESTful в разделе **Средства разработки** выберите **Обозреватель ресурсов**.

>[!NOTE]
>Ваш план службы приложений Azure должен иметь уровень "Стандартный" или выше. Дополнительные сведения см. в статье [Подробный обзор планов службы приложений Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Используйте [обозреватель ресурсов Azure (предварительная версия)](https://resources.azure.com), чтобы установить значение *True* для свойства **clientCertEnabled**, как показано на следующем рисунке.

![Параметр clientCertEnabled в обозревателе ресурсов Azure](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>Дополнительные сведения об установке свойства **clientCertEnabled** см. в статье [Настройка взаимной проверки подлинности TLS для веб-приложения](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

>[!TIP]
>Кроме того, для быстрого создания вызова REST API можно воспользоваться средством [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Шаг 2. Отправка сертификата в хранилище ключей политики Azure AD B2C
Когда вы установите для `clientCertEnabled` значение *True*, обмен данными с RESTful API будет выполняться только при наличии сертификата клиента. Чтобы получить сертификат клиента, а также отправить его в клиент Azure AD B2C и сохранить в нем, сделайте следующее: 
1. В клиенте Azure AD B2C выберите **B2C Settings** (Параметры B2C)  >  **Identity Experience Framework**.

2. Чтобы просмотреть доступные в клиенте ключи, выберите **Ключи политики**.

3. Выберите **Добавить**.  
    Откроется окно **Создание ключа**.

4. В поле **Параметры** выберите **Отправить**.

5. В поле **Имя** введите **B2cRestClientCertificate**.  
    Префикс *B2C_1A_* добавляется автоматически.

6. В поле **Отправка файлов** выберите PFX-файл сертификата с закрытым ключом.

7. В поле **Пароль** введите пароль сертификата.

    ![Отправка ключа политики](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Нажмите кнопку **Создать**.

8. Чтобы просмотреть все доступные в клиенте ключи и проверить создание ключа `B2C_1A_B2cRestClientCertificate`, выберите **Ключи политики**.

## <a name="step-3-change-the-technical-profile"></a>Шаг 3. Изменение технического профиля
Для поддержи проверки подлинности по сертификату клиента в вашей пользовательской политике измените технический профиль, сделав следующее:

1. В рабочей папке откройте файл политики расширения *TrustFrameworkExtensions.xml*.

2. Найдите узел `<TechnicalProfile>`, содержащий `Id="REST-API-SignUp"`.

3. Найдите элемент `<Metadata>`.

4. Измените *AuthenticationType* на *ClientCertificate*, как показано ниже:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Сразу после завершения элемента `<Metadata>` добавьте следующий фрагмент XML-кода: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    После добавления фрагмента кода технический профиль должен выглядеть аналогично следующему XML-коду:

    ![Настройка XML-элементов ClientCertificate для аутентификации](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Шаг 4. Отправка политики в клиент

1. На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и выберите **Azure AD B2C**.

2. Выберите **Инфраструктура процедур идентификации**.

3. Выберите **Все политики**.

4. Щелкните **Отправить политику**.

5. Установите флажок **Перезаписать политику, если она существует**.

6. Отправьте файл *TrustFrameworkExtensions.xml* и немного подождите, чтобы удостовериться, что он прошел проверку.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Шаг 5. Тестирование настраиваемой политики с помощью команды Run Now (Запустить сейчас)
1. Откройте **параметры Azure AD B2C** и выберите **Identity Experience Framework**.

    >[!NOTE]
    >Для использования команды Run now (Запустить сейчас) необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

2. Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны, а затем выберите **Run Now** (Запустить сейчас).

3. Проверьте процесс, введя **Test** в поле **Имя**.  
    В верхней части страницы Azure AD B2C отобразится сообщение об ошибке.    

    ![Проверка API для идентификации](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. В поле **Имя** введите имя, отличное от Test.  
    Azure AD B2C зарегистрирует нового пользователя и отправит в ваше приложение номер лояльности. Найдите номер в этом примере JWT.

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

   >[!NOTE]
   >Если отобразится сообщение об ошибке *The name is not valid, please provide a valid name* (Недопустимое имя. Введите допустимое имя), это значит, что Azure AD B2C успешно вызвал службу RESTful во время предоставления сертификата клиента. Теперь нам нужно проверить этот сертификат.

## <a name="step-6-add-certificate-validation"></a>Шаг 6. Добавление проверки сертификата
Сертификат клиента, который Azure AD B2C отправляет в службу RESTful, не проходит никаких проверок на платформе веб-приложений Azure (проверяется только наличие сертификата). За проверку этого сертификата отвечает веб-приложение. 

В этом разделе вы добавите пример кода ASP.NET, который проверяет свойства сертификата для проверки подлинности.

> [!NOTE]
>Дополнительные сведения о настройке проверки подлинности сертификата клиента в службе приложений Azure см. в статье [Настройка взаимной проверки подлинности TLS для веб-приложения](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1. Добавление параметров приложения в файл web.config проекта
В созданном ранее проекте Visual Studio добавьте указанные ниже параметры приложения в файл *web.config* после элемента `appSettings`.

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Замените значения **имени субъекта**, **имени издателя** и **отпечатка сертификата** собственными значениями сертификата.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2. Добавление функции IsValidClientCertificate
Откройте файл *Controllers\IdentityController.cs* и добавьте указанную ниже функцию в класс контроллера `Identity`. 

```csharp
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

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
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
    
    // 3. Check the issuer name of the certificate
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

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
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

В предыдущем примере кода мы принимаем сертификат как допустимый, только если выполняются все перечисленные ниже условия.
* Сертификат не истек и является активным на момент текущего времени сервера.
* Имя `Subject` сертификата совпадает со значением параметра `ClientCertificate:Subject` для приложения.
* Имя `Issuer` сертификата совпадает со значением параметра `ClientCertificate:Issuer` для приложения.
* Отпечаток `thumbprint` сертификата совпадает со значением параметра `ClientCertificate:Thumbprint` для приложения.

>[!IMPORTANT]
>В зависимости от уровня конфиденциальности службы могут потребоваться дополнительные проверки. Например, может потребоваться проверить связь сертификата с доверенным корневым центром сертификации, имя организации издателя и т. д.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3. Вызов функции IsValidClientCertificate
Откройте файл *Controllers\IdentityController.cs*, а затем в начале функции `SignUp()` добавьте следующий фрагмент кода: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Когда вы добавите фрагмент кода, контроллер `Identity` должен выглядеть аналогично следующему коду:

![Добавление кода для проверки сертификата](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Шаг 7. Публикация проекта в Azure и его тестирование
1. В **обозревателе решений** щелкните правой кнопкой мыши проект **Contoso.AADB2C.API**, а затем выберите **Опубликовать**.

2. Повторите шаг 6 и снова протестируйте пользовательскую политику с проверкой сертификата. Попробуйте запустить эту политику и убедитесь, что после добавления проверки все работает правильно.

3. В файле *web.config* измените значение `ClientCertificate:Subject` на **invalid**. Запустите политику еще раз. Должно появиться сообщение об ошибке.

4. Верните параметру значение **valid** и снова проверьте, успешно ли политика обращается к REST API.

Если необходимо устранить неполадки на этом шаге, ознакомьтесь со статьей [Azure Active Directory B2C: сбор журналов](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>Скачивание полного текста файлов политики и кода (необязательно)
* После того как вы ознакомитесь с [пошаговым руководством по началу работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md), рекомендуем создать свой сценарий, используя собственные файлы пользовательской политики. Для справки мы предоставили [образцы файлов политики](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Вы можете загрузить полный код из примера решения Visual Studio [отсюда](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 

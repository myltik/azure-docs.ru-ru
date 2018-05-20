---
title: Отладка единого входа на основе SAML в приложения в Azure Active Directory | Документация Майкрософт
description: 'Узнайте, как выполнять отладку единого входа на основе SAML в приложения в Azure Active Directory. '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Отладка единого входа на основе SAML в приложения в Azure Active Directory

При отладке интеграции с приложениями на основе SAML будет полезен такой инструмент, как [Fiddler](http://www.telerik.com/fiddler), который позволяет просмотреть запрос SAML и ответ SAML с токеном SAML, который был выдан приложению. 

![Fiddler][1]

Проблемы зачастую связаны с неправильной настройкой Azure Active Directory или приложения. В зависимости от того, где отображается сообщение об ошибке, следует проверять запрос или ответ SAML:

- Сообщение об ошибке отображается на странице входа моей компании [ссылки на раздел].
- Сообщение об ошибке отображается на странице приложения после входа [ссылки на раздел].

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>Сообщение об ошибке отображается на странице входа моей компании

Коды ошибок и рекомендации по их устранению вы найдете в статье [Проблемы с входом в приложение из коллекции, для которого настроен федеративный единый вход](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Если сообщение об ошибке появляется на странице входа вашей компании, для отладки следует использовать сообщение об ошибке и запрос SAML.

### <a name="how-can-i-get-the-error--message"></a>Как получить это сообщение об ошибке?

Сообщение об ошибке отображается в нижнем правом углу страницы. Здесь содержится сообщение об ошибке со следующими данными:

- идентификатор корреляции;
- метка времени;
- текст сообщения.

![Ошибка][2] 

 
Идентификатор корреляции и метка времени образуют уникальный идентификатор, по которому можно выполнить поиск в журналах серверной части, имеющих отношение к ошибке входа. Также эти значения важно указать при обращении в службу поддержки корпорации Майкрософт. Это поможет нашим инженерам быстрее найти решение проблемы.

В сообщении описана основная причина проблем со входом в систему. 


### <a name="how-can-i-review-the-saml-request"></a>Как просмотреть запрос SAML?

Запрос SAML, отправленный приложением в Azure Active Directory, обычно будет последним ответом HTTP 200 от [https://login.microsoftonline.com](https://login.microsoftonline.com).
 
Этот запрос SAML вы можете просмотреть с помощью Fiddler, выбрав нужную строку, а затем последовательно выбрав **Inspectors (Инспекторы) > WebForms (Веб-формы)** на панели справа. Щелкните правой кнопкой мыши значение **SAMLResponse** и выберите **Send to TextWizard** (Отправить в TextWizard). В меню **Transform** (Преобразование) выберите **From Base64** (Из Base64), чтобы расшифровать токен и просмотреть его содержимое. 

Кроме того, вы можете скопировать значение из запроса SAML и применить любой другой декодер Base64.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

В декодированном запросе SAML проверьте следующие сведения:

1. Значение **Destination** в запросе SAML должно совпадать с URL-адресом службы единого входа SAML, полученным из Azure Active Directory.
 
2. Значение **Issuer** в запросе SAML должно иметь тот же **идентификатор**, который вы настроили для этого приложения в Azure Active Directory. В Azure AD значение Issuer используется для поиска приложения в каталоге.

3. **AssertionConsumerServiceURL** обозначает расположение, в котором приложение ожидает получить маркер SAML от Azure Active Directory. Это значение можно настроить в Azure Active Directory, но оно не является обязательным элементом запроса SAML.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>Сообщение об ошибке отображается на странице приложения после входа

Это означает, что приложение не принимает ответ, предоставленный AAD. В этом случае следует передать ответ AAD с токеном SAML поставщику приложения, чтобы он проверил, какая информация в нем отсутствует или является неверной. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Как получить и просмотреть ответ SAML?

Ответ AAD с токеном SAML обычно создается после перенаправления HTTP 302 с https://login.microsoftonline.com и отправляется по **URL-адресу ответа**, настроенному в приложении. 

Токен SAML можно просмотреть, выбрав эту строку и затем выбрав вкладку **Inspectors > WebForms** (Инспекторы > Веб-формы) на правой панели. После этого щелкните правой кнопкой мыши значение **SAMLResponse** и выберите **Send to TextWizard** (Отправить в TextWizard). Чтобы расшифровать токен и просмотреть его содержимое, в меню **Transform** (Преобразование) выберите **From Base64** (Из Base64) или примените любой другой декодер Base64. 

Вы можете скопировать значение из **запроса SAML** и применить любой другой декодер Base64.

Изучите руководство по [устранению неполадок на странице приложения после входа](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML), чтобы получить дополнительные сведения о том, что в ответе SAML может быть неправильным и чего может не хватать.

Сведения о том, как просмотреть ответ SAML, можно найти в статье [Протокол единого входа SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Связанные статьи
* [Указатель статьей по управлению приложениями в Azure Active Directory](../active-directory-apps-index.md)
* [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Настройка утверждений, выпущенных в маркере SAML для предварительно интегрированных приложений](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png

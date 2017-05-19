---
title: "Интеграция обмена утверждениями REST API в Azure AD B2C как метода проверки в настраиваемых политиках | Документация Майкрософт"
description: "В этой статье описываются пользовательские политики Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 46abe48c3c9a7aab3fe013811d088a63957fe500
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-validation-on-user-input"></a>Пошаговое руководство по интеграции обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как метода проверки входных данных

**Инфраструктура процедур идентификации**, лежащая в основе Azure AD B2C, позволяет разработчикам служб удостоверений интегрировать взаимодействие с REST API в пути взаимодействия пользователя.  

В конце этого пошагового руководства вы сможете создавать пути взаимодействия пользователей Azure AD B2C, которые взаимодействуют со службами RESTful.

Инфраструктура процедур идентификации отправляет и получает данные в утверждениях.  Взаимодействие с API можно реализовать как обмен утверждениями REST API или профиль проверки, выполняемой на этапе оркестрации.

- Обычно на этом этапе происходит проверка входных данных пользователя.
- Если полученное значение отклоняется, пользователь может повторно ввести допустимое значение с возможностью возвращения сообщения об ошибке.

Взаимодействие можно также реализовать на этапе оркестрации. Дополнительные сведения см. в [пошаговом руководстве по интеграции обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как этапа оркестрации](active-directory-b2c-rest-api-step-custom.md).

В качестве примера профиля проверки мы будем использовать путь взаимодействия пользователя с политикой изменения профиля в файле начального пакета ProfileEdit.xml.

Мы можем проверить, не входит ли имя, предоставленное пользователем в политике изменения профиля, в список исключенных.

## <a name="prerequisites"></a>Предварительные требования

- Клиент Azure AD B2C, необходимый для регистрации или входа с использованием локальной учетной записи, как описано в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).
- Конечная точка REST API, с которой устанавливается взаимодействие. Демонстрационный сайт [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/), настроенный с помощью службы REST API, которая будет использоваться в рамках этого руководства.

## <a name="step-1---prepare-the-rest-api-function"></a>Шаг 1. Подготовка функции REST API

> [!NOTE]
> В рамках этой статьи настройка функций REST API не рассматривается. [Приложения-функции Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) предоставляют отличный набор средств для создания служб RESTful в облаке.

Мы создали функцию Azure, которая получает утверждение, ожидаемое как playerTag, и проверяет, существует ли это утверждение. Полный код функции Azure доступен на сайте [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```
if (requestContentAsJObject.playerTag == null)
    {
        return request.CreateResponse(HttpStatusCode.BadRequest);
    }

    var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

    if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
    {
        return request.CreateResponse<ResponseContent>(
            HttpStatusCode.Conflict,
            new ResponseContent
            {
                version = "1.0.0",
                status = (int) HttpStatusCode.Conflict,
                userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
            },
            new JsonMediaTypeFormatter(),
            "application/json");
    }

    return request.CreateResponse(HttpStatusCode.OK);
```

Инфраструктура процедур идентификации ожидает, что функция Azure вернет утверждение `userMessage` и что оно будет в виде строки, если при проверке произойдет сбой, например при возвращении кода состояния 409 (конфликт) в примере выше.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Шаг 2. Настройка обмена утверждениями на основе REST API как технического профиля в файле TrustFrameworkExtensions.xml

Технический профиль представляет собой полную конфигурацию обмена, заданную с помощью службы RESTful. Откройте файл `TrustFrameworkExtensions.xml` и добавьте приведенный ниже фрагмент XML-кода в элемент `<ClaimsProviders>`.

> [!NOTE]
> Мы советуем использовать поставщик RESTful версии 1.0.0.0, описанный ниже, как протокол и функцию, которая будет взаимодействовать с внешней службой.  Полное определение схемы см. здесь: <!-- TODO: Link to RESTful Provider schema definition>-->.

```
<ClaimsProvider>
        <DisplayName>REST APIs</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">

                    <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
                    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                    <Metadata>
                        <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                        <Item Key="AuthenticationType">None</Item>
                        <Item Key="SendClaimsIn">Body</Item>
                    </Metadata>
                    <InputClaims>
                        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
                    </InputClaims>
                    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>


            <TechnicalProfile Id="SelfAsserted-ProfileUpdate">

                <ValidationTechnicalProfiles>       
                    <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />       
                </ValidationTechnicalProfiles>

            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
```

Элемент `InputClaims` определяет утверждения, отправляемые с подсистемы процедур идентификации в службу REST. В примере выше содержимое утверждений `givenName` отправляется в службу REST как `playerTag`. В этом примере подсистема идентификации не ожидает возвращения утверждений. Вместо этого она ожидает ответ от службы REST и действует на основе полученного кода состояния.

## <a name="step-3---include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-wish-to-validate-the-user-input"></a>Шаг 3. Включение обмена утверждениями службы REST в самоподтвержденном техническом профиле, в котором необходимо реализовать проверку входных данных пользователя

Чаще всего этап проверки используется при взаимодействии с пользователем.  Все взаимодействия, где пользователь должен предоставить входные данные, выполняются с использованием **самоподтвержденных технических профилей**. В этом примере мы добавим этап проверки в технический профиль **Self-Asserted-ProfileUpdate**.  Именно этот технический профиль используется в файле политики проверяющей стороны `Profile Edit`.

Чтобы добавить обмен утверждениями в самоподтвержденный технический профиль, сделайте следующее:

1. Откройте файл TrustFrameworkBase и найдите `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Проверьте конфигурацию этого технического профиля и просмотрите, как определен обмен утверждениями, запрашивающими данные пользователя (входные утверждения), и утверждениями, возвращаемыми самоподтвержденным поставщиком (исходящие утверждения).
3. Найдите `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`. Обратите внимание, что это профиль, вызванный как этап оркестрации (6) `<UserJourney Id="ProfileEdit">`.

## <a name="step-4---upload-and-test-the-profile-edit-rp-policy-file"></a>Шаг 4. Отправка и тестирование файла политики изменения профиля проверяющей стороны

1. Загрузите новую версию файла `TrustframeworkExtensions`.
2. Проверьте файл политики изменения профиля проверяющей стороны с помощью кнопки **Запустить**.
3. Выполните проверку, указав одно из имеющихся имен (например, mcvinny) в поле **Заданное имя**. Если все настроено правильно, вы получите сообщение, указывающее, что `player tag` уже используется.

## <a name="next-steps"></a>Дальнейшие действия

[Azure Active Directory B2C. Создание и использование настраиваемых атрибутов в пользовательской политике изменения профиля](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Пошаговое руководство по интеграции обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как этапа оркестрации](active-directory-b2c-rest-api-step-custom.md)


---
title: "Настройка проверки подлинности и авторизации для пользовательского приложения, которое вызывает API Azure Time Series Insights | Документация Майкрософт"
description: "В этом руководстве описывается настройка проверки подлинности и авторизации для пользовательского приложения, которое вызывает API Azure Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: dmdenmsft
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/24/2017
ms.author: dmden
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 3b7d0811b718d4702786572b953fb27a9521d390
ms.contentlocale: ru-ru
ms.lasthandoff: 06/10/2017

---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этой статье описывается настройка пользовательского приложения, которое вызывает API Azure Time Series Insights.

## <a name="service-principal"></a>Субъект-служба

В этом разделе описывается настройка приложения для доступа к API Time Series Insights от имени приложения. Приложение может запрашивать данные или публиковать эталонные данные в среде Time Series Insights с учетными данными приложения, а не учетными данными пользователя.

При наличии приложения, для которого нужен доступ к Time Series Insights, необходимо установить приложение Azure Active Directory и назначить политики доступа к данным в среде Time Series Insights. Этот подход предпочтительнее запуска приложения с вашими учетными данными по следующим причинам:

* Для удостоверения приложения можно назначить разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Например, можно разрешить приложению только читать данные в определенной среде Time Series Insights.
* Не требуется изменять учетные данные приложения в случае изменения ваших обязанностей.
* Вы можете использовать сертификат или ключ приложения, чтобы автоматизировать проверку подлинности при выполнении автоматического скрипта.

В этой статье рассказывается, как это сделать с помощью портала Azure. Здесь рассматривается однотенантное приложение — решение, используемое в пределах одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации.

Процесс установки состоит из трех пошаговых действий.

1. Создайте приложение в Azure Active Directory.
2. Предоставьте этому приложению доступ к среде Time Series Insights.
3. Используйте идентификатор и ключ приложения, чтобы получить маркер безопасности для аудитории или ресурса `"https://api.timeseries.azure.com/"`. С помощью маркера безопасности можно вызывать API Time Series Insights.

Ниже описаны подробные инструкции:

1. На портале Azure выберите **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

   ![Регистрация нового приложения в Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Присвойте приложению имя, выберите тип **Веб-приложение или API**, выберите любой допустимый универсальный код ресурса (URI) в поле **URL-адрес входа** и нажмите кнопку **Создать**.

   ![Создание приложения в Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Выберите только что созданное приложение и скопируйте его идентификатор в привычный текстовый редактор.

   ![Копирование идентификатора приложения](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Выберите **Ключи**, введите имя ключа, выберите дату истечения срока действия и нажмите кнопку **Сохранить**.

   ![Выбор ключей приложения](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Ввод имени ключа и даты истечения срока действия и нажатие кнопки "Сохранить"](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Скопируйте ключ в предпочитаемый текстовый редактор.

   ![Копирование ключа приложения](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Для среды Time Series Insights выберите **Политики доступа к данным** и щелкните **Добавить**.

   ![Добавление новой политики доступа к данным в среде Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. В диалоговом окне **Выбор пользователя** вставьте имя приложения (из шага 2) или идентификатор приложения (из шага 3).

   ![Поиск приложения в диалоговом окне "Выбор пользователя"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Выберите роль (**Читатель** для запроса данных, **Участник** для запроса данных и изменения эталонных данных) и нажмите кнопку **ОК**.

   ![Выбор роли "Читатель" или "Участник" в диалоговом окне "Выбор роли"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Сохраните политику, нажав кнопку **ОК**.

10. Используйте идентификатор приложения (из шага 3) и ключ приложения (из шага 5), чтобы получить маркер безопасности от имени приложения. Затем маркер безопасности можно передать в заголовок `Authorization`, когда приложение вызывает API Time Series Insights.

    При использовании C# с помощью следующего кода можно получить маркер безопасности от имени приложения. Полный пример см. в статье [Запрос данных из среды Azure Time Series Insights с помощью C##](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

## <a name="next-steps"></a>Дальнейшие действия

Используйте идентификатор и ключ приложения в приложении. Пример кода, который вызывает API Time Series Insights, см. в статье [Запрос данных из среды Azure Time Series Insights с помощью C##](time-series-insights-query-data-csharp.md).

## <a name="see-also"></a>См. также

* Дополнительные сведения об API запроса см. в [этой статье](/rest/api/time-series-insights/time-series-insights-reference-queryapi)
* [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md)


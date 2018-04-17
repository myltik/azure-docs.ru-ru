---
title: Защита API с помощью протокола OAuth 2.0 и службы управления API в Azure Active Directory | Документация Майкрософт
description: Информация о защите внутренней службы веб-API с помощью Azure Active Directory и управления API
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 2c05407d761a8848f9e032aa219960cd7ea6fa93
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Защита API с помощью протокола OAuth 2.0 и службы управления API в Azure Active Directory

В этом руководстве показано, как настроить имеющийся экземпляр управления API (APIM) для защиты API с помощью протокола OAuth 2.0 в Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Предварительные требования
Чтобы выполнить шаги в этой статье, необходимо иметь следующее:
* экземпляр APIM;
* API, опубликованный с помощью экземпляра APIM;
* клиент Azure AD;

## <a name="overview"></a>Обзор

В этом руководстве показано, как защитить API с помощью OAuth 2.0 в APIM. В этой статье используется Azure Active Directory в качестве сервера авторизации (сервер OAuth). Ниже приведен краткий обзор шагов.

1. Регистрация приложения (серверного приложения) в Azure Active Directory для представления API.
2. Регистрация другого приложения (клиентского приложения) в Azure Active Directory для представления клиентского приложения, необходимого, чтобы вызвать API.
3. Предоставление разрешения в Azure Active Directory, чтобы разрешить клиентскому приложению вызывать серверное.
4. Настройка консоли разработчика для использования авторизации пользователя по протоколу OAuth 2.0.
5. Добавление политики validate-jwt для проверки токена OAuth для каждого входящего запроса.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Регистрация приложения в Azure Active Directory для представления API

Первым шагом в защите API в Azure Active Directory является регистрация приложения в Azure Active Directory, которое представляет API. 

Перейдите к своему клиенту Azure Active Directory, а затем — к **регистрации приложения**.

Выберите **Регистрация нового приложения**. 

Введите имя приложения. В этом примере используется `backend-app`.  

Выберите в качестве **типа приложения** **Web app / API** (Веб-приложение или API). 

Для **URL-адреса входа** можно использовать `https://localhost` в качестве заполнителя.

Нажмите кнопку **Создать**.

После создания приложения запишите **идентификатор приложения** для использования на следующем шаге. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Регистрация другого приложения в Azure Active Directory для представления клиентского приложения

Каждое клиентское приложение, необходимое для вызова API, должно быть зарегистрировано в Azure Active Directory. В этом руководстве в качестве примера клиентского приложения мы будем использовать консоль разработчика на портале разработчика APIM. 

Нам нужно зарегистрировать другое приложение в Azure Active Directory для представления консоли разработчика.

Снова щелкните **Регистрация нового приложения**. 

Укажите имя приложения и выберите в качестве **типа приложения** **Web app / API** (Веб-приложение или API). В этом примере используется `client-app`.  

Для **URL-адреса входа** можно использовать `https://localhost` в качестве заполнителя или URL-адрес входа экземпляра APIM. В этом примере используется `https://contoso5.portal.azure-api.net/signin`.

Нажмите кнопку **Создать**.

После создания приложения запишите **идентификатор приложения** для использования на следующем шаге. 

Теперь нужно создать секрет клиента для этого приложения. Вы будет использовать его на следующем шаге.

Снова щелкните **Параметры** и перейдите к разделу **Ключи**.

В разделе **Пароли** укажите **описание ключа**, выберите период окончания срока действия ключа и щелкните **Сохранить**.

Запишите значение ключа. 

## <a name="grant-permissions-in-aad"></a>Предоставление разрешений в AAD

Теперь мы зарегистрировали два приложения для представления API (то есть серверные приложения) и консоль разработчика (то есть клиентское приложение). Необходимо предоставить разрешения, чтобы клиентское приложение могло вызывать серверное приложение.  

Снова перейдите к **регистрации приложения**. 

Щелкните `client-app` и перейдите к **параметрам**.

Щелкните **Необходимые разрешения**, затем — **Добавить**.

Щелкните **Select an API** (Выбор API) и найдите `backend-app`.

В разделе **Делегированные разрешения** найдите `Access backend-app`. 

Щелкните **Выбрать**, а затем — **Done** (Готово). 

> [!NOTE]
> Если приложение **Microsoft** **Azure Active Directory** не указано в списке разрешений для других приложений, нажмите кнопку **Добавить** и добавьте его из списка.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Включение авторизации пользователей по протоколу OAuth 2.0 в консоль разработчика

На данный момент мы создали приложения в Azure Active Directory и предоставили соответствующие разрешения, чтобы клиентское приложение вызывало серверное. 

В этом руководстве мы будем использовать консоль разработчика в качестве клиентского приложения. В представленных ниже шагах описано включение авторизации пользователя по протоколу OAuth 2.0 в консоли разработчика. 

Перейдите к экземпляру службы управления API.

Щелкните **OAuth 2.0**, а затем — **Добавить**.

Укажите **отображаемое имя** и **описание**.

В поле URL-адреса страницы регистрации клиента** введите значение заполнителя, например `http://localhost`.  **URL-адрес страницы регистрации клиента** указывает на страницу, на которой пользователи могут создавать и настраивать собственные учетные записи для поставщиков OAuth 2.0, поддерживающих пользовательское управление учетными записями. В этом примере пользователи не создают и не настраивают собственные учетные записи, поэтому используется заполнитель.

Выберите **Код авторизации** для **типа предоставления авторизации**.

Затем укажите **URL-адрес конечной точки авторизации** и **URL-адрес конечной точки маркера**.

Эти значения можно извлечь на странице **конечных точек** в клиенте Azure Active Directory. Чтобы получить доступ к конечным точкам, снова перейдите на страницу **регистрации приложения** и щелкните **Конечные точки**.

Скопируйте значение **конечной точки авторизации OAuth 2.0** и вставьте его в текстовое поле **URL-адрес конечной точки авторизации**.

Скопируйте значение **конечной точки маркера OAuth 2.0** и вставьте его в текстовое поле **URL-адрес конечной точки маркера**.

Помимо вставки в поле конечной точки маркера добавьте дополнительный параметр с именем **ресурс**, а для значения используйте **идентификатор приложения** (серверного приложения).

Затем укажите учетные данные клиента. Это должны быть учетные данные клиентского приложения.

Для **идентификатора клиента** используйте **идентификатор приложения** (клиентского приложения).

Для **секрета клиента** используйте ранее созданный для клиентского приложения ключ. 

Далее в качестве типа предоставления кода авторизации указан **redirect_url**.

Запишите этот URL-адрес.

Нажмите кнопку **Создать**.

Вернитесь на страницу **параметров** клиентского приложения.

Щелкните **URL-адреса ответа** и вставьте **redirect_url** в первую строку. В этом примере мы заменили `https://localhost` на URL-адрес в первой строке.  

Теперь, когда мы настроили сервер авторизации OAuth 2.0, консоль разработчика должна получать маркеры доступа из Azure Active Directory. 

Следующим шагом является включение авторизации пользователей по протоколу OAuth 2.0 для API, чтобы консоль разработчика получала маркер доступа от имени пользователя перед вызовом API.

Перейдите к экземпляру службы управления API, а затем — к **API**.

Щелкните API, который необходимо защитить. В этом примере используется `Echo API`.

Перейдите в меню **Параметры**.

В разделе **безопасности** выберите **OAuth 2.0**, а также настроенный ранее сервер OAuth 2.0. 

Щелкните **Save**(Сохранить).

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Удачный вызов API с портала разработчика

Теперь, когда авторизация пользователя по протоколу OAuth 2.0 включена в `Echo API`, консоль разработчика будет получать маркер доступа от имени пользователя перед вызовом API.

Перейдите к любой операции в разделе `Echo API` на портале разработчика и щелкните **Try it** (Опробовать). После этого вы вернетесь к консоли разработчика.

Обратите внимание на новый элемент в разделе **Авторизация**, который соответствует только что добавленному серверу авторизации.

Выберите **Код авторизации**в раскрывающемся списке авторизации. После этого вам будет предложено войти в клиент Azure Active Directory. Если вы уже вошли с помощью учетной записи, запрос на ввод учетных данных не появится.

После успешного входа в систему заголовок `Authorization` будет добавлен в запрос маркера доступа из Azure Active Directory. 

Пример маркера выглядит следующим образов. Он закодирован методом Base64.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Щелкните **Отправить**, и выполнится успешный вызов API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Настройка политики проверки JWT для запросов предварительной авторизации

На этом этапе, если пользователь попытается выполнить вызов из консоли разработчика, ему будет предложено войти в систему, а консоль разработчика получит маркер доступа от имени пользователя. Все работает правильно. Однако что делать, если кто-то вызывает API без маркера или используя недопустимый маркер? Например, вы попытались удалить заголовок `Authorization`, но все еще можете вызывать API. Причина в том, что на этом этапе экземпляр службы управления API не проверяет маркер доступа. Он просто передает заголовок `Auhtorization` в API серверной части.

Используйте политику [Проверка JWT](api-management-access-restriction-policies.md#ValidateJWT) для предварительной авторизации запросов путем проверки маркеров доступа каждого входящего запроса. Запрос, имеющий недопустимый маркер, будет заблокирован управлением API. Его невозможно будет передать на сервер. Например, можно добавить приведенную ниже политику в раздел политики `<inbound>` интерфейса `Echo API`. Она проверяет наличие утверждения "Аудитория" в маркере доступа и возвращает сообщение об ошибке, если маркер является недопустимым. Сведения о настройке политик см. в статье [How to set or edit Azure API Management policies](set-edit-policies.md) (Как настроить или изменить политики в службе управления API Azure).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Создание приложения для вызова API

В этом руководстве мы использовали консоль разработчика в APIM как пример клиентского приложения для вызова `Echo API`, защищенного OAuth 2.0. Чтобы узнать больше о том, как создать приложение и реализовать поток OAuth 2.0, изучите [примеры кода Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше об [Azure Active Directory и OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* См. другие [видео](https://azure.microsoft.com/documentation/videos/index/?services=api-management) об управлении API.
* Другие способы защиты внутренней службы см. в разделе [Взаимная аутентификация на основе сертификатов](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md

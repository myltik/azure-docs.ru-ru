---
title: Защита API с помощью протокола OAuth 2.0 и службы управления API в Azure Active Directory | Документация Майкрософт
description: Информация о защите внутренней службы веб-API с помощью Azure Active Directory и службы управления API.
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
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928042"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Защита API с помощью протокола OAuth 2.0 и службы управления API в Azure Active Directory

В этом руководстве показано, как настроить имеющийся экземпляр управления API Azure для защиты API с помощью протокола OAuth 2.0 в Azure Active Directory. 

## <a name="prerequisites"></a>предварительным требованиям
Чтобы выполнить шаги в этой статье, необходимо иметь следующее:
* Экземпляр управления API.
* Публикуемый API, использующий экземпляр управления API (APIM).
* клиент Azure AD;

## <a name="overview"></a>Обзор

Ниже приведен краткий обзор шагов.

1. Регистрация приложения (серверного приложения) в Azure Active Directory для представления API.
2. Регистрация другого приложения (клиентского приложения) в Azure Active Directory для представления клиентского приложения, которое будет вызывать API.
3. Предоставление разрешения в Azure Active Directory, чтобы разрешить клиентскому приложению вызывать серверное.
4. Настройка консоли разработчика для использования авторизации пользователя по протоколу OAuth 2.0.
5. Добавление политики **validate-jwt** для проверки токена OAuth для каждого входящего запроса.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Регистрация приложения в Azure Active Directory для представления API

Первым шагом в защите API в Azure Active Directory является регистрация приложения в Azure Active Directory, которое представляет API. 

1. Перейдите к своему клиенту Azure Active Directory, а затем — к пункту **Регистрация приложений**.

2. Выберите **Регистрация нового приложения**. 

3. Введите имя приложения. (В этом примере используется имя `backend-app`.)  

4. Выберите в качестве **типа приложения** **Web app / API** (Веб-приложение или API). 

5. Для **URL-адреса входа** можно использовать `https://localhost` в качестве заполнителя.

6. Нажмите кнопку **Создать**.

После создания приложения запишите **идентификатор приложения** для использования на следующем шаге. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Регистрация другого приложения в Azure Active Directory для представления клиентского приложения

Каждое клиентское приложение, вызывающее API, должно быть также зарегистрировано в Azure Active Directory. В этом примере в качестве клиентского приложения используется консоль разработчика на портале разработчика службы управления API. Ниже показано, как зарегистрировать другое приложение в Azure Active Directory для представления консоли разработчика.

1. Выберите **Регистрация нового приложения**. 

2. Введите имя приложения. (В этом примере используется имя `client-app`.)

3. Выберите в качестве **типа приложения** **Web app / API** (Веб-приложение или API).  

4. Для **URL-адреса входа** можно использовать `https://localhost` в качестве заполнителя или URL-адрес входа экземпляра управления API. (В этом примере используется URL-адрес `https://contoso5.portal.azure-api.net/signin`.)

5. Нажмите кнопку **Создать**.

После создания приложения запишите **идентификатор приложения** для использования на следующем шаге. 

Теперь нужно создать секрет клиента для этого приложения. Он будет использоваться на следующем шаге.

1. Снова выберите **Параметры** и перейдите к разделу **Ключи**.

2. В разделе **Пароли** введите **описание ключа**. Выберите период окончания срока действия ключа и щелкните **Сохранить**.

Запишите значение ключа. 

## <a name="grant-permissions-in-azure-ad"></a>Предоставление разрешений в AAD

Теперь, когда зарегистрированы два приложения для представления API и консоли разработчика, необходимо предоставить разрешения, позволяющие клиентскому приложению вызывать серверное приложение.  

1. Перейдите к **Регистрация приложений**. 

2. Выберите `client-app` и перейдите к разделу **Параметры**.

3. Выберите **Необходимые разрешения** > **Добавить**.

4. Щелкните **Select an API** (Выбор API) и выполните поиск по запросу `backend-app`.

5. В разделе **Делегированные разрешения** выберите `Access backend-app`. 

6. Нажмите кнопку **Выбор**, а затем — **Готово**. 

> [!NOTE]
> Если приложение **Azure Active Directory** не указано в списке разрешений для других приложений, нажмите кнопку **Добавить** и добавьте из списка.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Включение авторизации пользователей по протоколу OAuth 2.0 в консоли разработчика

На данный момент мы создали приложения в Azure Active Directory и предоставили соответствующие разрешения, позволяющие клиентскому приложению вызывать серверное. 

В этом примере консоль разработчика является клиентским приложением. В следующих шагах описано включение авторизации пользователя по протоколу OAuth 2.0 в консоли разработчика. 

1. Перейдите к экземпляру службы управления API.

2. Выберите **OAuth 2.0** > **Добавить**.

3. Укажите **отображаемое имя** и **описание**.

4. В поле **URL-адреса страницы регистрации клиента** введите значение заполнителя, например `http://localhost`. **URL-адрес страницы регистрации клиента** указывает на страницу, на которой пользователи могут создавать и настраивать собственные учетные записи для поставщиков OAuth 2.0, поддерживающих пользовательское управление учетными записями. В этом примере пользователи не создают и не настраивают собственные учетные записи, поэтому используется заполнитель.

5. Выберите **Код авторизации** для **типов предоставления авторизации**.

6. Затем укажите **URL-адрес конечной точки авторизации** и **URL-адрес конечной точки маркера**. Эти значения можно извлечь на странице **конечных точек** в клиенте Azure Active Directory. Снова перейдите на страницу **Регистрация приложений** и выберите **Конечные точки**.

7. Скопируйте значение **конечной точки авторизации OAuth 2.0** и вставьте его в текстовое поле **URL-адрес конечной точки авторизации**.

8. Скопируйте значение **конечной точки маркера OAuth 2.0** и вставьте его в текстовое поле **URL-адрес конечной точки маркера**. Помимо вставки в поле конечной точки маркера добавьте дополнительный параметр с именем **ресурс**. В качестве значения этого параметра используйте **идентификатор приложения** (серверного приложения).

9. Затем укажите учетные данные клиента. Это должны быть учетные данные клиентского приложения.

10. Для **идентификации клиента** используйте **идентификатор приложения** (клиентского приложения).

11. Для **секрета клиента** используйте ранее созданный для клиентского приложения ключ. 

12. Далее в качестве типа предоставления кода авторизации указан **redirect_url**. Запишите этот URL-адрес.

13. Нажмите кнопку **Создать**.

14. Вернитесь на страницу **параметров** клиентского приложения.

15. Выберите **URL-адреса ответа** и вставьте **redirect_url** в первую строку. В этом примере мы заменили `https://localhost` на URL-адрес в первой строке.  

Теперь, когда сервер авторизации OAuth 2.0 настроен, консоль разработчика должна получать маркеры доступа от Azure Active Directory. 

Следующим шагом является включение авторизации пользователей по протоколу OAuth 2.0 для API, чтобы консоль разработчика получала маркер доступа от имени пользователя перед вызовом API.

1. Перейдите к экземпляру службы управления API и выберите раздел **API**.

2. Выберите API, который нужно защитить. В этом примере используется `Echo API`.

3. Перейдите в меню **Параметры**.

4. В разделе **безопасности** выберите **OAuth 2.0**, а также настроенный ранее сервер OAuth 2.0. 

5. Щелкните **Сохранить**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Удачный вызов API с портала разработчика

Теперь, когда авторизация пользователя по протоколу OAuth 2.0 включена в `Echo API`, консоль разработчика получает маркер доступа от имени пользователя перед вызовом API.

1. Перейдите к любой из операций в разделе `Echo API` на портале разработчика и выберите **Попробовать**. Откроется консоль разработчика.

2. Обратите внимание на новый элемент в разделе **Авторизация**, соответствующий только что добавленному серверу авторизации.

3. Выберите **Код авторизации** в раскрывающемся списке авторизации, после чего будет предложено войти в клиент Azure Active Directory. Если вы уже вошли с помощью учетной записи, запрос на ввод учетных данных не появится.

4. После успешного входа в систему заголовок `Authorization` будет добавлен в запрос с маркером доступа из Azure Active Directory. Ниже приведен пример маркера (в кодировке Base64).

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Выберите **Отправить**. После этого вы сможете успешно вызывать API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Настройка политики проверки JWT для запросов предварительной авторизации

На этом этапе, когда пользователь пытается выполнить вызов из консоли разработчика, ему предлагается выполнить вход. Консоль разработчика получает маркер доступа от имени пользователя.

Однако, что делать, если кто-то вызывает API без маркера или используя недопустимый маркер? Например, можно по-прежнему вызвать API даже в том случае, если удален заголовок `Authorization`. Причина в том, что на этом этапе экземпляр службы управления API не проверяет маркер доступа. Он просто передает заголовок `Authorization` в API серверной части.

Используйте политику [Проверка JWT](api-management-access-restriction-policies.md#ValidateJWT) для предварительной авторизации запросов в службе управления API путем проверки маркеров доступа каждого входящего запроса. Если у запроса нет допустимого маркера, управление API блокирует его. Например, можно добавить приведенную ниже политику в раздел политик `<inbound>` интерфейса `Echo API`. Она проверяет наличие утверждения "Аудитория" в маркере доступа и возвращает сообщение об ошибке, если маркер является недопустимым. Сведения о настройке политик см. в статье [How to set or edit Azure API Management policies](set-edit-policies.md) (Как настроить или изменить политики в службе управления API Azure).

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

В этом руководстве мы использовали консоль разработчика в APIM как пример клиентского приложения для вызова `Echo API`, защищенного OAuth 2.0. Чтобы узнать больше о том, как создать приложение и реализовать OAuth 2.0, изучите [примеры кода Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше об [Azure Active Directory и OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* См. другие [видео](https://azure.microsoft.com/documentation/videos/index/?services=api-management) об управлении API.
* Другие способы защиты внутренней серверной службы см. в разделе [Взаимная проверка подлинности на основе сертификата](api-management-howto-mutual-certificates.md).

* [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md)

* [Импорт и публикация первого API](import-and-publish.md)

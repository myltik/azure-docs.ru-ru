---
title: "Защита пользовательских соединителей в Azure Logic Apps с помощью Azure AD | Документация Майкрософт"
description: "Узнайте, как повысить уровень безопасности и аутентификации API и соединителя с помощью Azure Active Directory (Azure AD)."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: dae76a4230a1cfbe8970bbb1015041fde7765f49
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2018
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Повышение уровня безопасности API и соединителя с помощью Azure Active Directory (Azure AD)

Чтобы защитить вызовы между API и пользовательским соединителем, добавьте в веб-API и соединитель функцию аутентификации Azure AD. Для этого сценария мы создадим два приложения Azure Active Directory (Azure AD): одно приложение обеспечивает защиту веб-API, а второе — защищает регистрацию соединителя и добавляет делегированный доступ. 

В этом руководстве для примера используется API Azure Resource Manager. Azure Resource Manager помогает управлять компонентами решения, созданного в Azure, такими как базы данных, виртуальные машины и веб-приложения. Пользовательский соединитель для Azure Resource Manager можно применять для управления ресурсами Azure из рабочих процессов. в статье [Обзор диспетчера ресурсов Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас нет подписки, вы можете для начала использовать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/). Или зарегистрируйтесь для получения [подписки с оплатой по мере использования](https://azure.microsoft.com/pricing/purchase-options/).

* В этом руководстве используется [пример файла OpenAPI для Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

  > [!NOTE] 
  > Пример файла OpenAPI не определяет все операции Azure Resource Manager и в настоящее время содержит только операцию [Вывод списка всех подписок](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Вы можете изменить этот или создать другой файл OpenAPI с помощью [онлайн-редактора OpenAPI](http://editor.swagger.io/).
  >
  > Это руководство можно применять к любому интерфейсу RESTful API, в котором будет использоваться аутентификация Azure AD.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Создание первого приложения Azure AD для защиты веб-API

Первое приложение Azure AD выполняет аутентификацию, когда пользовательский соединитель вызывает API (в данном примере — API Azure Resource Manager).

1. Войдите на [портале Azure](https://portal.azure.com). Если у вас несколько клиентов Azure Active Directory, то убедитесь, что выполнен вход в правильный каталог (имя каталога отображается под именем пользователя). 

   ![Подтверждение каталога](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Чтобы сменить каталог, выберите имя пользователя, а затем выберите необходимый каталог.

2. В главном меню Azure выберите **Azure Active Directory**, чтобы просмотреть текущий каталог.

   ![Выбор элемента Azure Active Directory](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Если в главном меню Azure не отображается **Azure Active Directory**, то выберите **Все службы**. В поле **Фильтр** введите "Azure Active Directory", а затем выберите элемент **Azure Active Directory**.

3. В разделе **Управление** в меню каталога выберите **Регистрация приложений**. В списке зарегистрированных приложений выберите **Регистрация нового приложения**.

   ![Выбор "Регистрация приложений", "Регистрация нового приложения"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. В разделе **Создание** введите сведения о своем приложении Azure AD, как описано в таблице, а затем нажмите кнопку **Создать**. 

   ![Создание приложения Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Параметр | Рекомендуемое значение | Описание | 
   | ------- | --------------- | ----------- | 
   | **Имя** | *имя-приложения-веб-api* | Имя для вашего приложения Azure AD веб-API | 
   | **Тип приложения** | **Веб-приложение или API** | Тип вашего приложения | 
   | **URL-адрес входа** | `https://login.windows.net` | | 
   |||| 

5. При возврате к списку **Регистрация приложений** каталога выберите свое приложение Azure AD.

   ![Выбор приложения Azure AD из списка](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Когда отобразится страница сведений о приложении, обязательно **скопируйте *идентификатор приложения* и сохраните его в надежном месте**. Этот идентификатор будет необходим для последующих действий.

   ![Сохранение идентификатора приложения для последующих действий](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Теперь укажите URL-адрес ответа для своего приложения Azure AD. В меню **Параметры** приложения выберите **URL-адреса ответа**. Введите этот URL-адрес, а затем выберите **Сохранить**.

   ![URL-адреса ответа](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Параметр | Рекомендуемое значение | ОПИСАНИЕ | 
   | ------- | --------------- | ----------- | 
   | **URL-адреса ответа** | Для своего API введите этот URL-адрес: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Делегированные разрешения** | {необязательно} | | 
   | **Клиентский ключ** | {необязательно} | | 
   |||| 

   > [!TIP]
   > Если меню **Параметры** ранее не отображалось, то выберите **Параметры** здесь:
   >
   > ![Выбор меню "Параметры"](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Создание второго приложения Azure AD для пользовательского соединителя

Второе приложение Azure AD защищает регистрацию пользовательского соединителя и добавляет делегированный доступ к веб-API, защищенному первым приложением. 

> [!IMPORTANT]
> Убедитесь, что оба приложения Azure AD находятся в одном каталоге.

1. Вернитесь к списку **Регистрация приложений** и снова выберите **Регистрация нового приложения**.

   ![Выбор "Регистрация приложений", "Регистрация нового приложения"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. В разделе **Создание** введите сведения о своем втором приложении Azure AD, как описано в таблице, а затем нажмите кнопку **Создать**. 

   ![Создание приложения Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Параметр | Рекомендуемое значение | Описание | 
   | ------- | --------------- | ----------- | 
   | **Имя** | *имя-соединителя* | Имя для вашего приложения Azure AD соединителя | 
   | **Тип приложения** | **Веб-приложение или API** | Тип вашего приложения | 
   | **URL-адрес входа** | `https://login.windows.net` | | 
   |||| 

3. При возврате к списку **Регистрация приложений** каталога выберите свое второе приложение Azure AD.

   ![Выбор второго приложения Azure AD из списка](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Когда отобразится страница сведений о приложении, также обязательно **скопируйте *идентификатор этого приложения* и также сохраните его в надежном месте**. Этот идентификатор будет необходим для последующих действий.

   ![Сохранение идентификатора приложения для последующих действий](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Теперь укажите URL-адрес ответа для своего приложения Azure AD. В меню **Параметры** приложения выберите **URL-адреса ответа**. Введите этот URL-адрес, а затем выберите **Сохранить**.

   | Параметр | Рекомендуемое значение | 
   | ------- | --------------- | 
   | **URL-адреса ответа** | Для пользовательского соединителя Azure Resource Manager введите этот URL-адрес: `https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Если меню **Параметры** ранее не отображалось, то выберите **Параметры** здесь:
   >
   > ![Выбор меню "Параметры"](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. Вернувшись в меню **Параметры**, выберите **Необходимые разрешения** > **Добавить**.

   ![Необходимые разрешения > Добавить](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. Когда откроется меню **Добавить доступ через API**, выберите **Выбор API** > 
**API управления службами Microsoft Azure** > **Выбрать**.

   ![Выбор API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. В меню **Добавить доступ через API** выберите **Выбор разрешений**. В разделе **Делегированные разрешения** выберите пункт **Access Azure Service Management as organization users** (Доступ к управлению службами Azure в качестве пользователей организации)  > **Выбрать**.

   ![Выбор пунктов "Делегированные разрешения" > "Access Azure Service Management as organization users" (Доступ к управлению службами Azure в качестве пользователей организации).](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Для других вариантов:

   | Параметр | Рекомендуемое значение | ОПИСАНИЕ | 
   | ------ | --------------- | ----------- | 
   | **Делегированные разрешения** | | Выберите разрешения для делегированного доступа к веб-API | 
   |||| 

9. Затем в меню **Добавить доступ через API** выберите **Готово**.

   ![Меню "Добавить доступ через API" > "Готово"](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Теперь создайте *клиентский ключ*, или "секрет", для своего приложения Azure AD соединителя. 

    1. Вернувшись в меню **Параметры**, выберите **Ключи**. 
    Введите имя ключа (не более 16 знаков), выберите срок действия, а затем щелкните **Сохранить**.

       ![Создание клиентского ключа](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Когда отобразится созданный ключ, **обязательно скопируйте его и сохраните в надежном месте**, прежде чем покидать страницу **Ключи**.
    
       ![Копирование и сохранение ключа вручную](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. После сохранения ключа можно безопасно закрыть меню **Параметры**.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Добавление аутентификации Azure AD в приложение веб-API

Теперь включите аутентификацию для веб-API с помощью первого приложения Azure AD. Дополнительные сведения см. в статье [Настройка приложения службы приложений для использования службы входа Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. На [портале Azure](https://portal.azure.com), найдите приложение веб-API, которое вы ранее опубликовали (как описано в разделе [Создание соединителей с помощью интерфейсов веб-API](../logic-apps/custom-connector-build-web-api-app-tutorial.md)).

2. В разделе **Параметры** выберите **Аутентификация или авторизация**. 

   1. В разделе **Проверка подлинности службы приложений** выберите **Вкл.**
   2. В раскрывающемся списке **Предпринимаемое действие, если проверка подлинности для запроса не выполнена** выберите **Войти с использованием Azure Active Directory**.
   3. В списке **Поставщики проверки подлинности** выберите **Azure Active Directory**. 

   ![Выбор пункта "Аутентификация или авторизация"](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. На странице **Параметры Azure Active Directory** выполните следующие действия:

   1. В разделе **Режим управления** выберите **Экспресс**.

   2. Теперь выберите приложение Azure AD, которое используется вашим приложением веб-API для аутентификации. 
   Выберите **Выбрать существующее приложение AD** > **Приложение Azure AD**.

   3. В разделе **Приложения Azure AD** выберите приложение Azure AD, которое вы ранее создали для своего приложения веб-API. 
   
   4. Нажимайте кнопку **ОК**, пока не вернетесь на страницу **Аутентификация или авторизация**.

   Например: 

   ![Выбор приложения Azure AD, которое представляет приложение веб-API](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. На странице **Аутентификация или авторизация** щелкните **Сохранить**.

   ![Сохранение параметров аутентификации](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Теперь ваше приложение веб-API может использовать Azure AD для аутентификации.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Настройка аутентификации Azure AD в файле OpenAPI веб-API

Откройте файл OpenAPI для своего приложения веб-API, чтобы можно было добавить объект `securityDefintions` и аутентификацию Azure AD в свойство `host`. Ниже приведен пример, показывающий свойство `host` и объект `securityDefinitions`:

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
Теперь вы можете создать и зарегистрировать свой пользовательский соединитель.

## <a name="next-steps"></a>Дополнительная информация

* [Регистрация соединителя](../logic-apps/logic-apps-custom-connector-register.md)
* [Часто задаваемые вопросы о настраиваемых соединителях](../logic-apps/custom-connector-faq.md)

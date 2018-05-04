---
title: Azure Active Directory B2C. Перемещение пользователей с удостоверениями социальных сетей
description: В этой статье рассматриваются основные понятия перемещения пользователей с удостоверениями социальных сетей в Azure Active Directory B2C с помощью API Graph.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.openlocfilehash: a150d8255171c83334ac4c1f81cbcee39c1df70a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C. Перемещение пользователей с удостоверениями социальных сетей
При планировании миграции поставщика удостоверений в Azure Active Directory B2C также может потребоваться перенести пользователей с удостоверениями социальных сетей. В этой статье объясняется, как переносить в Azure AD B2C имеющиеся учетные записи с удостоверениями социальных сетей, такие как Facebook, LinkedIn, Майкрософт, и учетные записи Google. Приведенные здесь сведения также применимы к федеративным удостоверениям, однако такие миграции менее распространены.

## <a name="prerequisites"></a>предварительным требованиям
Эта статья является продолжением статьи о перемещении пользователей и посвящена перемещению социальных удостоверений. Прежде чем начать, ознакомьтесь со статьей [Azure Active Directory B2C. Миграция пользователей](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Общие сведения о переносе социальных удостоверений

* В Azure AD B2C имена для входа **локальных учетных записей** (имя пользователя или адрес электронной почты) хранятся в коллекции `signInNames` в записи пользователя. Коллекция `signInNames` содержит одну или несколько записей `signInName`, которые определяют имена входа для пользователя. Каждое имя должно быть уникальным в клиенте.

* Удостоверения **учетных записей социальных сетей** хранятся в коллекции `userIdentities`. В записи указывается атрибут `issuer` (имя поставщика удостоверений), например facebook.com, и атрибут `issuerUserId`, который является уникальным идентификатором пользователя для издателя. Атрибут `userIdentities` содержит одну или несколько записей UserIdentity, которые определяют тип учетной записи социальной сети и уникальный идентификатор пользователя от поставщика удостоверений социальных сетей.

* **Объедините локальную учетную запись с удостоверением социальной сети**. Как уже упоминалось, имена входа локальных учетных записей и удостоверения учетных записей социальных сетей хранятся в разных атрибутах. Атрибут `signInNames` используется для локальной учетной записи, а `userIdentities` — для учетной записи социальной сети. Единая учетная запись Azure AD B2C может быть только локальной, только учетной записью социальной сети или же сочетать локальную учетную запись с удостоверением социальной сети в одной записи пользователя. Таким образом вы будете управлять единой учетной записью, в то время как пользователь может войти в систему с помощью учетных данных локальной учетной записи или удостоверения социальной сети.

* Тип `UserIdentity` содержит информацию об идентификаторе пользователя учетной записи социальной сети в клиенте Azure AD B2C.
    * `issuer` — строковое представление поставщика удостоверений, выдавшего идентификатор пользователя, например facebook.com.
    * `issuerUserId` — уникальный идентификатор пользователя, используемый поставщиком удостоверений социальных сетей в формате base64.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* В зависимости от поставщика удостоверений **идентификатор пользователя социальной сети** является уникальным значением для пользователя определенного приложения или учетной записи разработки. Настройте политику Azure AD B2C с тем же идентификатором приложения, который ранее был назначен поставщиком удостоверений социальных сетей. Или используйте другое приложение в той же учетной записи для разработки.

## <a name="use-graph-api-to-migrate-users"></a>Перенос пользователей с помощью API Graph
Создайте учетную запись пользователя Azure AD B2C с помощью [API Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Для взаимодействия с API Graph сначала необходимо иметь учетную запись службы с правами администратора. В Azure AD зарегистрируйте приложение и выполните аутентификацию в Azure AD. Учетные данные приложения такие: идентификатор приложения и секрет приложения. Приложение вызывает API Graph, действуя само по себе, а не под именем конкретного пользователя. Выполните инструкции в разделе [Шаг 1. Миграция пользователей с помощью API Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users).

## <a name="required-properties"></a>Обязательные свойства
Ниже приведен список свойств, которые являются обязательными при создании пользователя.
* **accountEnabled** — значение true.
* **displayName** — имя, которое будет отображаться для пользователя в адресной книге.
* **passwordProfile** — пароль профиля пользователя. 

> [!NOTE]
> Для учетной записи социальной сети (без учетных данных локальной учетной записи) все равно необходимо указать пароль. Azure AD B2C игнорирует пароль, указанный для учетных записей социальных сетей.

* **userPrincipalName** — имя участника-пользователя (someuser@contoso.com). Имя участника-пользователя должно содержать один из проверенных доменов для клиента. Чтобы указать имя участника-пользователя, создайте значение GUID и соедините его с `@` и именем вашего клиента.
* **mailNickname** — псевдоним почты для пользователя. Это значение может быть тем же идентификатором, который вы используете для userPrincipalName. 
* **signInNames** — одна или несколько записей SignInName, которые определяют имена входа для пользователя. Каждое имя входа должно быть уникальным для всей компании или клиента. Это свойство можно не указывать (только для учетной записи социальной сети).
* **userIdentities** — одна или несколько записей UserIdentity, которые определяют тип учетной записи социальной сети и уникальный идентификатор пользователя от поставщика удостоверений социальных сетей.
* **otherMails** (необязательно) — адреса электронной почты пользователя (только для учетной записи социальной сети). 

Дополнительные сведения см. в разделе [Create a user (local or social account)](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser) (Создание пользователя (локальная учетная запись или учетная запись социальной сети)).

## <a name="migrate-social-account-only"></a>Перенос учетной записи социальной сети (только)
Чтобы создать только учетную запись социальной сети (без учетных данных локальной учетной записи), отправьте запрос HTTPS POST в API Graph. Текст запроса содержит свойства пользователя учетной записи социальной сети, которого нужно создать. Необходимо указать по меньшей мере обязательные свойства. 


**POST** https://graph.windows.net/tenant-name.onmicrosoft.com/users

Отправьте следующие данные формы. 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Перенос учетной записи социальной сети с локальной учетной записью
Чтобы создать объединенную локальную учетную запись с удостоверениями социальных сетей, отправьте запрос HTTPS POST в API Graph. Текст запроса содержит свойства пользователя учетной записи социальной сети, которого нужно создать. Необходимо указать по меньшей мере обязательные свойства. 

**POST** https://graph.windows.net/tenant-name.onmicrosoft.com/users

Отправьте следующие данные формы. 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
### <a name="how-can-i-know-the-issuer-name"></a>Как узнать имя издателя?
Имя издателя или имя поставщика удостоверений настроено в вашей политике. Если вы не знаете значение, которое нужно указать в `issuer`, выполните следующую процедуру.
1. Войдите в одну из учетных записей социальных сетей.
2. Из маркера JWT скопируйте значение `sub`. Оно обычно содержит идентификатор объекта пользователя в Azure AD B2C. Кроме того, можно открыть свойства пользователя на портале Azure и скопировать идентификатор объекта.
3. Откройте [обозреватель Azure AD Graph](https://graphexplorer.azurewebsites.net).
4. Войдите в систему с правами администратора. Нет
5. Выполните следующий запрос GET. Замените значение userObjectId скопированным идентификатором пользователя. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Найдите элемент `userIdentities` в возвращенном из Azure AD B2C фрагменте JSON.
7. При необходимости можно декодировать значение `issuerUserId`.

> [!NOTE]
> Используйте учетную запись администратора клиента B2C, которая является локальной по отношению к клиенту B2C. Синтаксис имени учетной записи выглядит следующим образом: admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Можно ли добавить удостоверение социальной сети в имеющуюся локальную учетную запись?
Да. Вы можете добавить удостоверение социальной сети после создания локальной учетной записи. Выполните запрос HTTPS PATCH. Замените значение userObjectId идентификатором пользователя, который необходимо обновить. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Отправьте следующие данные формы. 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Можно ли добавить несколько удостоверений социальных сетей?
Да. Вы можете добавить несколько удостоверений социальных сетей для одной учетной записи Azure AD B2C. Выполните запрос HTTPS PATCH. Замените значение userObjectId на идентификатор пользователя. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Отправьте следующие данные формы. 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>Пример приложения для переноса пользователей (необязательно)
[Загрузка и запуск примера приложения V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Пример приложения V2 использует файл JSON, содержащий фиктивные пользовательские данные, в том числе: локальную учетную запись, учетную запись социальной сети, а также локальные учетные данные и удостоверения социальной сети в отдельной учетной записи.  Чтобы изменить JSON-файл, откройте решение Visual Studio `AADB2C.UserMigration.sln`. В проекте `AADB2C.UserMigration` откройте файл `UsersData.json`. Файл содержит список сущностей пользователей. Каждая сущность пользователя имеет следующие свойства.
* **signInName**. Адрес электронной почты для входа в систему (для локальной учетной записи).
* **displayName**. Отображаемое имя пользователя.
* **firstName**. Имя пользователя.
* **lastName**. Фамилия пользователя.
* **password**. Пароль пользователя для локальной учетной записи (может быть пустым).
* **issuer**. Имя поставщика удостоверений для учетной записи социальной сети.
* **issuerUserId**. Уникальный идентификатор пользователя, используемый поставщиком удостоверений социальных сетей, для учетной записи социальной сети. Значение должно быть в виде обычного текста. Пример приложения кодирует это значение в формат base64.
* **email**. Адрес электронной почты пользователя только для учетной записи социальной сети (не объединенной).

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Если вы не обновите файл UsersData.json в примере, указав свои данные, вход в систему можно выполнить с помощью примера учетных данных локальной учетной записи, а не примера удостоверения учетной записи социальной сети. Чтобы перенести учетные записи социальной сети, укажите реальные данные.

Дополнительные сведения об использовании этого примера приложения см. в статье [Azure Active Directory B2C. Миграция пользователей](active-directory-b2c-user-migration.md).

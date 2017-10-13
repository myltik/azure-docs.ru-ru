---
title: "Создание удостоверения для приложения Azure с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Использование Azure CLI для создания приложения Azure Active Directory и субъекта-службы с последующим предоставлением доступа к ресурсам посредством управления доступом на основе ролей. В статье показано, как выполнять проверку подлинности приложения с помощью пароля или сертификата."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.openlocfilehash: 3c5826d58887ff1af4df8e66999d9c1a1643bcc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам

При наличии приложения или сценария, которому требуется доступ к ресурсам, можно настроить удостоверение для приложения и аутентифицировать его с помощью собственных учетных данных. Этот идентификатор известен как субъект-служба. Такой подход позволяет выполнить следующие действия:

* Назначить удостоверению приложения разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы.
* Использовать сертификат для аутентификации при выполнении автоматического сценария.

В этой статье показано, как настроить использование собственных учетных данных и удостоверения для приложения с помощью [Azure CLI 1.0](../cli-install-nodejs.md). Установите последнюю версию интерфейса командной строки [Azure CLI 1.0](../cli-install-nodejs.md), чтобы ваша среда соответствовала примерам в этой статье.

## <a name="required-permissions"></a>Необходимые разрешения
Для работы с этой статьей у вас должен быть достаточный уровень разрешений в подписке в Azure Active Directory и Azure. В частности, вы должны иметь право на создание приложения в Azure Active Directory и назначение роли субъекту-службе. 

Проверить, есть ли у вас соответствующие разрешения, проще всего на портале. Ознакомьтесь с [проверкой наличия необходимых разрешений на портале](resource-group-create-service-principal-portal.md#required-permissions).

Теперь перейдите к соответствующему разделу, чтобы выполнить проверку подлинности на основе [пароля](#create-service-principal-with-password) или [сертификата](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Создание субъекта-службы с использованием пароля
В этом разделе содержатся инструкции, которые помогут вам создать приложение AD с использованием пароля и назначить роль читателя субъекту-службе.

1. Войдите в свою учетную запись.
   
   ```azurecli
   azure login
   ```
2. Чтобы создать удостоверение приложения, укажите имя приложения и введите пароль, как показано в следующей команде:
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта. Идентификатор GUID, приведенный в именах субъектов-служб, требуется для входа в систему. Значение этого идентификатора такое же, как и значение идентификатора приложения. В примерах приложений это значение называется `Client ID`. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере показано, как назначить субъекту-службе роль читателя, которая дает разрешение на чтение всех ресурсов в подписке. Сведения о других ролях см. в статье [RBAC: встроенные роли](../active-directory/role-based-access-built-in-roles.md). Для параметра objectid укажите значение ObjectId, которое использовалось при создании приложения. Перед выполнением этой команды нужно подождать некоторое время, чтобы данные нового субъекта-службы распространились в Azure Active Directory. При выполнении этих команд вручную между задачами обычно проходит достаточно времени. В сценарий следует добавить шаг ожидания между этими командами (например, `sleep 15`). Если отображается ошибка и сообщение о том, что субъект в каталоге не существует, выполните команду еще раз.
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
Вот и все! Приложение AD и субъект-служба настроены. В следующем разделе показано, как выполнить вход с использованием учетных данных с помощью Azure CLI. Если необходимо использовать учетные данные в коде приложения, выполнять действия, описанные ниже, не требуется. Вы можете перейти к разделу [Примеры приложений](#sample-applications) , где приведены примеры входа с использованием идентификатора приложения и пароля. 

### <a name="provide-credentials-through-azure-cli"></a>Предоставление учетных данных через Azure CLI
Теперь следует войти в систему от имени приложения для выполнения операций.

1. При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Azure Active Directory. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, используйте следующую команду:
   
   ```azurecli
   azure account show
   ```
   
   Возвращаемые данные:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     Если необходимо получить идентификатор клиента другой подписки, используйте следующую команду:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Если необходимо получить идентификатор клиента для входа в систему, используйте следующую команду.
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     Значение, используемые для входа в систему, — это идентификатор GUID, приведенный в именах субъектов-служб.
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. Выполните вход как субъект-служба.
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    Появится запрос на ввод пароля. Введите пароль, который вы задали при создании приложения AD.
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

Теперь вы прошли проверку подлинности от имени субъекта-службы для созданного вами приложения.

Кроме того, чтобы выполнить вход, можно вызывать операции REST из командной строки. Из ответа службы аутентификации можно получить маркер доступа, пригодный для использования в других операциях. Пример получения маркера доступа путем вызова операции REST приведен в разделе [Создание маркера доступа](resource-manager-rest-api.md#generating-an-access-token).

## <a name="create-service-principal-with-certificate"></a>Создание субъекта-службы с использованием сертификата
В этом разделе содержатся инструкции, которые помогут вам:

* создать самозаверяющий сертификат;
* создать приложение AD с сертификатом и субъектом-службой;
* назначить роль "Читатель" субъекту-службе.

Для выполнения этих действий в системе должен быть установлен [OpenSSL](http://www.openssl.org/) .

1. Создать самозаверяющий сертификат.
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. На предыдущем шаге были созданы два файла — privkey.pem и cert.pem. Объедините открытый и закрытый ключи в один файл.

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. Откройте файл **examplecert.pem** и найдите длинную последовательность символов между тегами **-----BEGIN CERTIFICATE-----** и **-----END CERTIFICATE-----**. Скопируйте данные сертификата. Во время создания субъекта-службы эти данные передаются в качестве параметра.

4. Войдите в свою учетную запись.

   ```azurecli
   azure login
   ```
5. Чтобы создать субъект-службу, укажите имя приложения и данные сертификата, как показано в следующей команде:
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта. Идентификатор GUID, приведенный в именах субъектов-служб, требуется для входа в систему. Значение этого идентификатора такое же, как и значение идентификатора приложения. В примерах приложений это значение называется идентификатором клиента. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере показано, как назначить субъекту-службе роль читателя, которая дает разрешение на чтение всех ресурсов в подписке. Сведения о других ролях см. в статье [RBAC: встроенные роли](../active-directory/role-based-access-built-in-roles.md). Для параметра objectid укажите значение ObjectId, которое использовалось при создании приложения. Перед выполнением этой команды нужно подождать некоторое время, чтобы данные нового субъекта-службы распространились в Azure Active Directory. При выполнении этих команд вручную между задачами обычно проходит достаточно времени. В сценарий следует добавить шаг ожидания между этими командами (например, `sleep 15`). Если отображается ошибка и сообщение о том, что субъект в каталоге не существует, выполните команду еще раз.
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>Предоставление сертификата с помощью автоматизированного сценария для интерфейса командной строки Azure
Теперь следует войти в систему от имени приложения для выполнения операций.

1. При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Azure Active Directory. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, используйте следующую команду:
   
   ```azurecli
   azure account show
   ```
   
   Возвращаемые данные:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   Если необходимо получить идентификатор клиента другой подписки, используйте следующую команду:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Чтобы получить отпечаток сертификата и удалить ненужные символы, используйте такую команду:
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   Возвращается значение отпечатка следующего вида:
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. Если необходимо получить идентификатор клиента для входа в систему, используйте следующую команду.
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   Значение, используемые для входа в систему, — это идентификатор GUID, приведенный в именах субъектов-служб.
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. Выполните вход как субъект-служба.
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

После этого субъект-служба для созданного вами приложения Azure Active Directory пройдет проверку подлинности.

## <a name="change-credentials"></a>Изменение учетных данных

Чтобы изменить учетные данные для приложения AD из-за нарушения безопасности или истечения их срока действия, используйте команду `azure ad app set`.

Чтобы изменить пароль, используйте следующую команду.

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

Чтобы изменить значение сертификата, используйте следующую команду.

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>Отладка

При создании субъекта-службы могут возникнуть следующие ошибки:

* **"Authentication_Unauthorized"** (Аутентификация не выполнена) или **"No subscription found in the context"** (В контексте не удалось найти подписку). — Эта ошибка может отобразиться, если учетная запись не имеет [необходимых разрешений](#required-permissions) в Azure Active Directory для регистрации приложения. Как правило, эта ошибка возникает в тех случаях, когда регистрировать приложения в Azure Active Directory могут только администраторы, а ваша учетная запись не является административной. Попросите администратора назначить вам роль администратора или разрешить пользователям регистрировать приложения.

* Ваша учетная запись **"не авторизована для выполнения действия 'Microsoft.Authorization/roleAssignments/write' с областью '/subscriptions/{guid}'."** Эта ошибка возникает, когда учетная запись не имеет достаточно разрешений для назначения роли удостоверению. Попросите администратора подписки назначить вам роль администратора доступа пользователей.

## <a name="sample-applications"></a>Примеры приложений
Сведения о входе в систему в качестве приложения с помощью различных платформ см. в следующих разделах:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Дальнейшие действия
* Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [Управление ресурсами клиента с помощью Azure Active Directory и Resource Manager](resource-manager-api-authentication.md).
* Дополнительные сведения об использовании сертификатов и Azure CLI см. в статье [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Проверка подлинности на основе сертификата для субъектов-служб Azure из командной строки Linux). 
* Список доступных действий, которые можно разрешить или запретить пользователям, см. в разделе [Операции поставщиков ресурсов Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).

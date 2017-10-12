---
title: "Использование MSI виртуальной машины Linux для доступа к Azure Key Vault"
description: "В рамках этого руководства вы узнаете, как получить доступ к Azure Resource Manager с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: elkuzmen
ms.openlocfilehash: dd2dfe20f86b3fac28871b27a1c2b66c2b4a4cd6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-azure-key-vault"></a>Получение доступа к Azure Key Vault с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

В этой статье описывается активация управляемого удостоверения службы (MSI) виртуальной машины Windows и его использование для получения доступа к API Azure Key Vault. Управляемыми удостоверениями службы автоматически управляет Azure. Они позволяют проходить аутентификацию в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Включение MSI на виртуальной машине Linux. 
> * предоставлять виртуальной машине доступ к секрету в Key Vault; 
> * Получение маркера доступа с помощью удостоверения виртуальной машины и его использование для получения секрета из Key Vault. 
 


Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


## <a name="sign-in-to-azure"></a>Вход в Azure
Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Linux. Вы также можете активировать MSI на имеющейся виртуальной машине.

1. Щелкните **Создать** в верхнем левом углу портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В раскрывающемся списке выберите **подписку** для виртуальной машины.
5. Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". На странице параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. На самом деле активация MSI необходима для установки расширения виртуальной машины MSI и непосредственно активации MSI на виртуальной машине.  

1. Выберите **виртуальную машину**, на которой нужно активировать MSI.
2. В левой области навигации щелкните **Конфигурация**.
3. Появится страница **Managed Service Identity** (Управляемое удостоверение службы). Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Если вы хотите проверить расширения на этой **виртуальной машине Linux**, щелкните раздел **Расширения**. Если удостоверение MSI активировано, вы увидите в списке **ManagedIdentityExtensionforLinux**.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Предоставление виртуальной машине доступа к секрету в Key Vault  

С помощью MSI код может получить маркеры доступа, чтобы пройти аутентификацию и получить доступ к ресурсам, поддерживающим аутентификацию Azure Active Directory. Однако не все службы Azure поддерживают аутентификацию Azure AD. Чтобы использовать MSI со службами, не поддерживающими ее, можно сохранить в Azure Key Vault учетные данные, необходимые для этих служб, и использовать MSI, чтобы пройти аутентификацию в Key Vault для извлечения учетных данных. 

Сначала необходимо создать хранилище Key Vault и предоставить удостоверению виртуальной машины доступ к этому хранилищу.   

1. В верхней части левой области навигации выберите **+ Создать**, **Безопасность и идентификация** и **Хранилище ключей**.  
2. Укажите **имя** для нового хранилища Key Vault. 
3. Найдите хранилище Key Vault в той же подписке и группе ресурсов, где ранее была создана виртуальная машина. 
4. Выберите **Политики доступа** и щелкните **Добавить**. 
5. В раскрывающемся списке Configure from template (Настроить на основе шаблона) выберите **Управление секретами**. 
6. Выберите **Выбор субъекта** и в поле поиска введите имя созданной ранее виртуальной машины.  Выберите виртуальную машину в списке результатов и щелкните **Выбрать**. 
7. Чтобы завершить добавление новой политики доступа, нажмите кнопку **ОК**, а затем нажмите кнопку **ОК**, чтобы завершить выбор политики доступа. 
8. Чтобы завершить создание хранилища ключей, нажмите кнопку **Создать**. 

    ![Замещающий текст](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Затем добавьте секрет в хранилище Key Vault, чтобы позже извлечь его с помощью кода, который вы запустите на виртуальной машине: 

1. Выберите **Все ресурсы**, а затем найдите и выберите только что созданное хранилище Key Vault. 
2. Выберите **Секреты** и щелкните **Добавить**. 
3. В разделе **Параметры отправки** выберите **Вручную**. 
4. Введите имя и значение для секрета.  Значение может быть любым. 
5. Не указывайте дату активации и окончания срока действия и для параметра **Включено** оставьте значение **Да**. 
6. Щелкните **Создать**, чтобы создать секрет. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-retrieve-the-secret-from-the-key-vault"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и его использование для извлечения секрета из хранилища Key Vault  

Для выполнения этих действий вам потребуется клиент SSH.  Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about).   
 
1. На портале перейдите на виртуальную машину Linux и в разделе **Обзор** щелкните **Подключиться**. 
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала с помощью cURL выполните запрос к локальной конечной точке MSI, чтобы получить маркер доступа для Azure Key Vault.  
 
    Запрос cURL маркера доступа приведен ниже.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    Ответ включает маркер доступа, необходимый для доступа к Resource Manager. 
    
    Ответ:  
    
    ```bash
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEPfpXZRLwrBNd607AzX0KVmLFrwA1vYJnCV-sSV8bwTh2t6CVEj240t0iyeVWVc2usJ0NY2rxPzKd_UckQ_zzrECG3kS4vuYePKz6GqNJFVzm2w2c61lX0-O1CwvQ9w","refresh_token":"","expires_in":"3599","expires_on":"1504130527","not_before":"1504126627","resource":"https://vault.azure.net","token_type":"Bearer"} 
    ```
    
    Этот маркер доступа можно использовать для аутентификации в Azure Key Vault.  В следующем запросе cURL показано, как считать секрет из хранилища Key Vault с помощью cURL и REST API для Key Vault.  Вам потребуется URL-адрес вашего хранилища ключей, который находится в разделе **Основные компоненты** на странице **Обзор** в этом хранилище.  а также маркер доступа, полученный ранее. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Ответ будет выглядеть следующим образом: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
После получения секрета из хранилища Key Vault, его можно использовать для аутентификации в службе, требующей имя пользователя и пароль.


## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](../active-directory/msi-overview.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.





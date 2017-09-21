---
title: "Использование MSI виртуальной машины Linux для доступа к службе хранилища Azure"
description: "В рамках этого руководства вы узнаете, как получить доступ к службе хранилища Azure с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux."
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
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>Получение доступа к учетным данным службы хранилища с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

В этой статье описывается активация управляемого удостоверения службы (MSI) виртуальной машины Linux и его использование для получения ключей к хранилищу данных. Вы можете использовать ключи к хранилищу данных стандартным образом при выполнении операций с хранилищем, например при использовании пакета SDK службы хранилища Azure. В рамках этого руководства мы отправим и скачаем большие двоичные объекты с помощью Azure CLI. Вы научитесь:


> [!div class="checklist"]
> * Включение MSI на виртуальной машине Linux. 
> * создавать учетную запись хранения;
> * предоставлять виртуальной машине доступ к службе хранилища;
> * Получение маркера доступа учетной записи хранения с помощью удостоверения виртуальной машины для доступа к ключам к хранилищу данных. 


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
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. На самом деле активация MSI необходима для установки расширения виртуальной машины MSI и непосредственно активации MSI на виртуальной машине.  

1. Выберите **виртуальную машину**, на которой нужно активировать MSI.
2. В левой области навигации щелкните **Конфигурация**.
3. Появится страница **Managed Service Identity** (Управляемое удостоверение службы). Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Если вы хотите проверить расширения на этой **виртуальной машине Linux**, щелкните раздел **Расширения**. Если удостоверение MSI активировано, вы увидите в списке **ManagedIdentityExtensionforLinux**.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="create-a-new-storage-account"></a>Создание учетной записи хранения 

Ключи к хранилищу данных можно использовать в обычном режиме при выполнении операций с хранилищем. В этом примере мы подробно рассмотрим отправку и скачивание больших двоичных объектов с помощью Azure CLI. 

1. Перейдите на боковую панель и выберите **Хранилище**.  
2. Создайте **учетную запись хранения**.  
3. В области **Модель развертывания** введите **диспетчер ресурсов** и **тип учетной записи** с **общим назначением**.  
4. Проверьте, чтобы были выбраны те же **подписка** и **группа ресурсов**, которые использовались при создании **виртуальной машины Linux**.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Предоставление удостоверению виртуальной машины доступа к ключам к хранилищу данных 

С помощью MSI код может получить маркеры доступа, чтобы пройти аутентификацию и получить доступ к ресурсам, поддерживающим аутентификацию Azure AD.   

1. Перейдите на вкладку **Хранилище**.  
2. Выберите **учетную запись хранения**, созданную ранее.   
3. Перейдите к элементу **Управление доступом (IAM)** на панели слева.  
4. Затем **добавьте** новое назначение роли виртуальной машины, выберите для параметра **Роль** значение **Роль службы оператора ключей учетных записей хранения**.  
5. В раскрывающемся списке далее в поле **Назначение доступа к** выберите ресурс **Виртуальная машина**.  
6. Проверьте, чтобы в раскрывающемся списке **Подписка** была выбрана нужная подписка. В поле **Группа ресурсов** выберите **Все группы ресурсов**.  
7. И наконец, в поле **Выбрать** выберите свою виртуальную машину Linux в раскрывающемся списке, а затем нажмите кнопку **Сохранить**. 
    ![Замещающий текст](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью

Вам понадобится использовать терминал Bash. Дистрибутив Linux можно выбрать и скачать [здесь](https://msdn.microsoft.com/commandline/wsl/install_guide).


1. На портале перейдите на виртуальную машину Linux и в разделе **Обзор** щелкните **Подключиться**. Вам предложат использовать Bash. Запишите значение ключа SSH и IP-адрес виртуальной машины из оповещения. 
2. Откройте Bash и выполните подключение.  
3. В окне терминала введите значение **SSH** и **IP-адрес виртуальной машины**, к которой нужно подключиться, например ssh admin@12.61.219.35.  
4. После этого вам предложат ввести **пароль**, добавленный при создании **виртуальной машины Linux**. Вы должны без проблем войти в систему.  
5. Затем можно выполнить запрос с помощью CURL, чтобы получить токен аутентификации виртуальной машины Linux, к которой вы подключились. Конечная точка **Azure Resource Manager** — https://management.azure.com.  

    Запрос cURL маркера доступа приведен ниже:
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > URL-адрес ресурса, доступ к которому вы пытаетесь запросить, должен быть определенного формата (косая черта в конце, например https:<RESOURCE>/).
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>Запрос cURL для получения ключей к хранилищу данных из Azure Resource Manager  

> [!NOTE]
> Текст, который содержит URL-адрес, чувствителен к регистру, поэтому проверьте регистр в названиях групп ресурсов, чтобы текст отображался соответствующим образом. Кроме того, убедитесь, что используется запрос POST, а не GET, а также, что вы передали значение для регистрации максимальной длины с помощью параметра -d, который может иметь значение NULL.  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

В ответе cURL будет содержаться список ключей:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


Создайте файл для отправки. Это будет пример файла большого двоичного объекта, который вы сможете отправить с помощью ключей к хранилищу данных учетной записи хранения в созданном контейнере. 

На виртуальной машине Linux это можно сделать с помощью следующей команды. 

```bash
echo "This is a test file." > test.txt
```
 Затем можно отправить файл с помощью Azure CLI и пройти аутентификацию с помощью ключа к хранилищу данных.
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

Ответ: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Кроме того, можно скачать файл с помощью Azure CLI и пройти аутентификацию с помощью ключа к хранилищу данных. 

Запрос: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
```

Ответ: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-12T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>Связанная информация

Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](../active-directory/msi-overview.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.



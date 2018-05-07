---
title: Использование MSI виртуальной машины Linux для доступа к службе хранилища Azure с учетными данными SAS
description: Руководство по использованию удостоверений управляемой службы (MSI) виртуальной машины Linux для доступа к хранилищу Azure с учетными данными SAS вместо ключа доступа учетной записи хранения.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fc7c5b4ab025666fc7fa1d9073198ec90d8e71c3
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Использование управляемого удостоверения службы виртуальной машины Linux для доступа к службе хранилища Azure с учетными данными SAS

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Из этого руководства вы узнаете, как использовать управляемое удостоверение службы (MSI) с виртуальной машиной Linux и как с его помощью получить учетные данные SAS (подписанный URL-адрес). Для этого используются [учетные данные SAS службы](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

SAS службы предоставляет определенной службе (в нашем случае службе BLOB-объектов) ограниченный доступ к объектам в учетной записи хранения на ограниченный срок, не передавая ей ключ доступа к учетной записи. Подписанный URL-адрес можно использовать стандартным образом для операций с хранилищем, например при использовании пакета SDK для службы хранилища Azure. В этом руководстве описано, как отправлять и скачивать большой двоичный объект с помощью интерфейса командной строки хранилища Azure. Вы научитесь:


> [!div class="checklist"]
> * Включение MSI на виртуальной машине Linux. 
> * предоставлять виртуальной машине доступ к подписанным URL-адресам учетной записи хранения в Resource Manager; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для извлечения SAS из Resource Manager. 

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure
Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Linux. Вы также можете активировать MSI на имеющейся виртуальной машине.

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В раскрывающемся списке выберите **подписку** для виртуальной машины.
5. Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. На самом деле активация MSI необходима для установки расширения виртуальной машины MSI и непосредственно активации MSI на виртуальной машине.  

1. Перейдите в группу ресурсов новой виртуальной машины и выберите виртуальную машину, созданную на предыдущем шаге.
2. В разделе настроек виртуальной машины слева щелкните **Конфигурация**.
3. Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Если вы хотите проверить расширения на этой виртуальной машине, щелкните **Расширения**. Если удостоверение MSI активировано, вы увидите в списке **ManagedIdentityExtensionforLinux**.

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения. 

Если у вас еще нет учетной записи хранения, создайте ее.  Этот шаг можно пропустить и предоставить виртуальной машине доступ с помощью MSI к ключам имеющейся учетной записи хранения. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Щелкните **Хранилище**, а затем **Учетная запись хранения**, после чего отобразится новая панель "Создание учетной записи хранения".
3. Введите **имя** учетной записи хранения, которая будет использоваться далее.  
4. Для параметра **Модель развертывания** необходимо выбрать "Resource Manager", а для параметра **Account kind** (Тип учетной записи) — "Общее назначение". 
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.
6. Нажмите кнопку **Создать**.

    ![Создание учетной записи хранения](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Создание контейнера больших двоичных объектов в учетной записи хранения

Позже мы отправим и скачаем файл в новую учетную запись хранения. Так как файлам необходимо хранилище BLOB-объектов, нужно создать контейнер больших двоичных объектов, в котором будет храниться файл.

1. Вернитесь к только что созданной учетной записи хранения.
2. Щелкните ссылку **Контейнеры** в разделе "Служба BLOB-объектов" на панели слева.
3. Щелкните **+ Контейнер** в верхней части страницы, после чего появится панель "Новый контейнер".
4. Присвойте контейнеру имя, выберите уровень доступа, а затем нажмите кнопку **ОК**. Указанное имя будет использоваться далее в этом руководстве. 

    ![Создание контейнера хранилища](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Предоставление прав на использование SAS хранилища для MSI виртуальной машины 

В службе хранилища Azure не встроена поддержка проверки подлинности Azure AD.  Тем не менее можно использовать MSI для извлечения SAS хранилища из Resource Manager и для доступа к хранилищу с помощью SAS.  На этом шаге вы предоставите виртуальной машине доступ к использованию SAS учетной записи хранения.   

1. Вернитесь к только что созданной учетной записи хранения.   
2. Щелкните ссылку **Управление доступом (IAM)** на панели слева.  
3. Щелкните **+ Добавить** в верхней части страницы, чтобы добавить новое назначение роли для виртуальной машины.
4. Для параметра **Роль** в правой части страницы выберите значение "Участник учетных записей хранения". 
5. Рядом с ним в раскрывающемся списке **Назначение доступа к** выберите ресурс "Виртуальная машина".  
6. Убедитесь, что нужная подписка присутствует в раскрывающемся списке **Подписки**, и установите для параметра **Группы ресурсов** значение "Все группы ресурсов".  
7. И, наконец, в разделе **Выбрать** выберите в раскрывающемся списке нужную виртуальную машину Linux и нажмите кнопку **Сохранить**.  

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Linux и вверху в разделе **Обзор** щелкните **Подключить**. Скопируйте строку подключения к виртуальной машине. 
2. Подключитесь к виртуальной машине c помощью клиента SSH.  
3. После этого вам предложат ввести **пароль**, добавленный при создании **виртуальной машины Linux**. Вы должны без проблем войти в систему.  
4. Используйте cURL для получения маркера доступа для Azure Resource Manager.  

    Запрос CURL маркера доступа и соответствующий ответ приведены ниже:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > В предыдущем запросе значение параметра resource должно точно совпадать со значением, которое ожидает Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    > В следующем ответе элемент access_token сокращен для удобства восприятия.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Получение учетных данных SAS из Azure Resource Manager для обращения к хранилищу

Теперь мы с помощью CURL вызовем Resource Manager, используя полученный на предыдущем этапе маркер доступа, и создадим учетные данные SAS для хранилища. Получив учетные данные SAS, мы сможем выполнять операции отправки и (или) скачивания в хранилище.

Для этого мы будем использовать следующие параметры HTTP-запроса, чтобы создать учетные данные SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Эти параметры включены в тело запроса POST на получение учетных данных SAS. Дополнительные сведения о параметрах для создания учетных данных SAS см. в [справочнике по выводу SAS службы с использованием REST](/rest/api/storagerp/storageaccounts/listservicesas).

Используйте следующий запрос CURL для получения учетных данных SAS. Не забудьте заменить значения параметров `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` и `<EXPIRATION TIME>` своими значениями. Замените значение `<ACCESS TOKEN>` маркером доступа, который вы получили ранее:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Текст в приведенном выше URL-адресе чувствителен к регистру, поэтому используйте строчные и заглавные буквы, как указано в названиях групп ресурсов. Кроме того, учитывайте, что это запрос POST, а не GET.

Ответ от службы CURL возвращает учетные данные SAS:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Создайте образец файла большого двоичного объекта, чтобы отправить его в контейнер хранилища BLOB-объектов. На виртуальной машине Linux это можно сделать с помощью следующей команды. 

```bash
echo "This is a test file." > test.txt
```

Теперь выполните аутентификацию с помощью команды CLI `az storage`, указав в ней учетные данные SAS, и отправьте файл в контейнер больших двоичных объектов. Для выполнения этого шага нужно [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) на виртуальной машине, если вы не сделали этого ранее.

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Ответ: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Кроме того, можно скачать файл с помощью Azure CLI и выполнить аутентификацию, используя учетные данные SAS. 

Запрос: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
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
    "lastModified": "2017-09-21T03:58:56+00:00",
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

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- Чтобы узнать, как выполнить шаги из этого руководства с помощью ключа учетной записи хранения, см. статью [Доступ к службе хранилища Azure с помощью управляемого удостоверения службы виртуальной машины Linux](msi-tutorial-linux-vm-access-storage.md).
- Дополнительные сведения о возможностях SAS для учетной записи службы хранилища Azure см. в следующих статьях:
  - [Использование подписанных URL-адресов (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Создание подписанного URL-адреса уровня службы](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

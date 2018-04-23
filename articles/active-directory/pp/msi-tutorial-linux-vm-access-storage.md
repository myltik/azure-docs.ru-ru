---
title: Использование назначаемого пользователем MSI в виртуальной машине Linux для получения доступа к службе хранилища Azure
description: В рамках этого руководства вы узнаете, как получить доступ к службе хранилища Azure с помощью назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Linux.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4a1a2d0c40012649f6cd89193fd3f704f325e38a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Получение доступа к службе хранилища Azure с помощью назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Linux

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве показано, как создавать и использовать назначаемые пользователем управляемые удостоверения службы (MSI) из виртуальной машины Linux, а затем использовать их для получения доступа к службе хранилища Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание назначаемого пользователем управляемого удостоверения службы (MSI).
> * Присвоение назначаемого пользователем MSI виртуальной машине Linux.
> * Предоставление доступа к экземпляру службы хранилища Azure для MSI.
> * Получение маркера доступа с помощью назначаемого пользователем удостоверения MSI и его использование для получения доступа к службе хранилища Azure.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Запустить примеры сценариев CLI в этом руководстве можно двумя способами:

- использовать службу [Azure Cloud Shell](~/articles/cloud-shell/overview.md) на портале Azure или с помощью кнопки "Попробовать", расположенной в правом верхнем углу каждого блока кода;
- [Установить последнюю версию интерфейса командной строки (CLI) 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более позднюю версию), если вы предпочитаете использовать локальную консоль CLI.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

Сначала создайте виртуальную машину Linux. При желании также можно активировать MSI на имеющейся виртуальной машине.

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В раскрывающемся списке выберите **подписку** для виртуальной машины.
5. Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

## <a name="create-a-user-assigned-msi"></a>Создание назначаемого пользователем удостоверения MSI

1. Если вы используете консоль CLI (вместо сеанса Azure Cloud Shell), сначала войдите в Azure. Используйте учетную запись, связанную с подпиской Azure, в рамках которой нужно создать удостоверение MSI:

    ```azurecli
    az login
    ```

2. Создайте назначаемое пользователем MSI с помощью команды [az identity create](/cli/azure/identity#az_identity_create). Параметр `-g` указывает группу ресурсов, в которой создается удостоверение MSI, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<MSI NAME>` собственными:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Ответ содержит подробные сведения о созданном назначаемом пользователем удостоверения MSI, подобные следующему примеру. Запишите значение `id` удостоверения MSI, так как оно будет использоваться на следующем шаге:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Назначение пользовательского удостоверения MSI виртуальной машине Linux

В отличие от удостоверения MSI, назначаемого системой, назначаемое пользователем удостоверение MSI может использоваться клиентами в множестве ресурсов Azure. В этом руководстве мы назначим его отдельной виртуальной машине. Вы также можете назначить удостоверение нескольким виртуальным машинам.

Назначьте пользовательское удостоверение MSI виртуальной машине Linux с помощью команды [az vm assign-identity](/cli/azure/vm#az-vm-identity-assign). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. Используйте свойство `id`, возвращенное на предыдущем шаге, в качестве значения параметра `--identities`:

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения. 

Если у вас еще нет учетной записи хранения, создайте ее. Этот шаг можно пропустить и при желании использовать имеющуюся учетную запись хранения. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Щелкните **Хранилище**, а затем **Учетная запись хранения**, после чего отобразится новая панель "Создать учетную запись хранения".
3. Введите **имя** учетной записи хранения, которая будет использоваться далее.  
4. Для параметра **Модель развертывания** необходимо выбрать "Resource Manager", а для параметра **Account kind** (Тип учетной записи) — "Общее назначение". 
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.
6. Нажмите кнопку **Создать**.

    ![Создание учетной записи хранения](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Создание контейнера больших двоичных объектов в учетной записи хранения

Так как файлам необходимо хранилище BLOB-объектов, нужно создать контейнер больших двоичных объектов, в котором будет храниться файл. Затем файл отправляется и скачивается в контейнер больших двоичных объектов в новой учетной записи хранения.

1. Вернитесь к только что созданной учетной записи хранения.
2. Щелкните ссылку **Контейнеры** слева в разделе "Служба BLOB-объектов".
3. Щелкните **+ Контейнер** в верхней части страницы, после чего появится панель "Новый контейнер".
4. Присвойте контейнеру имя, выберите уровень доступа, а затем нажмите кнопку **ОК**. Указанное имя также будет использоваться далее в этом руководстве. 

    ![Создание контейнера хранилища](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Передайте файл в только что созданный контейнер. Для этого щелкните имя контейнера, а затем — **Отправить**, выберите файл и нажмите кнопку **Отправить**.

    ![Отправка текстового файла](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Предоставление доступа к контейнеру службы хранилища Azure для назначаемого пользователем MSI

С помощью MSI код может получить маркеры доступа, чтобы пройти аутентификацию и получить доступ к ресурсам, поддерживающим аутентификацию Azure AD. В этом руководстве используется служба хранилища Azure.

Сначала идентификатору MSI предоставляется доступ к контейнеру службы хранилища Azure. В этом случае используется контейнер, созданный ранее. Обновите `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>` и `<CONTAINER NAME>` значениями, соответствующими вашей среде. Также замените `<MSI PRINCIPALID>` свойством `principalId`, возвращенным командой `az identity create` в разделе [Создание назначаемого пользователем удостоверения MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

Ответ включает сведения о созданных назначениях ролей:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Получение маркера доступа с помощью назначаемого пользователем удостоверения MSI, которое используется для доступа к службам хранилища Azure

В оставшейся части руководства вы будете работать с виртуальной машиной, созданной ранее.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Linux и вверху в разделе **Обзор** щелкните **Подключить**. Скопируйте строку подключения к виртуальной машине.
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала с помощью CURL выполните запрос к локальной конечной точке MSI, чтобы получить маркер доступа для службы хранилища Azure.

   В следующем примере показан запрос CURL для получения маркера доступа. Не забудьте заменить `<CLIENT ID>` свойством `clientId`, возвращенным командой `az identity create` в разделе [Создание назначаемого пользователем MSI](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > В предыдущем запросе значение параметра `resource` должно точно совпадать со значением, которое ожидает Azure AD. Если используется идентификатор ресурса службы хранилища Azure, добавьте косую черту после универсального кода ресурса (URI).
   > В следующем ответе элемент access_token сокращен для удобства восприятия.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Теперь для получения доступа к службе хранилища Azure используйте маркер доступа, например, чтобы прочитать содержимое примера файла, предварительно отправленного в контейнер. Замените значения `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` и `<FILE NAME>` значениями, указанными ранее, а `<ACCESS TOKEN>` — маркером, возвращенным на предыдущем шаге.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   В ответе содержится содержимое файла:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- Чтобы узнать, как выполнить шаги из этого руководства с помощью учетных данных SAS, см. руководство по [использованию управляемого удостоверения службы виртуальной машины Linux для получения доступа к службе хранилища Azure c помощью учетных данных SAS](msi-tutorial-linux-vm-access-storage-sas.md).
- Дополнительные сведения о возможностях SAS для учетной записи службы хранилища Azure см. в следующих статьях:
  - [Использование подписанных URL-адресов (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Создание подписанного URL-адреса уровня службы](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.






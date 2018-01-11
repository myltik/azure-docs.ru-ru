---
title: "Использовать MSI-ФАЙЛ на виртуальной Машине Linux, пользователь для доступа к службе хранилища Azure"
description: "Учебник, в котором описывается процесс использования пользователя назначенный управляемые службы удостоверений (MSI) на виртуальной Машине Linux для доступа к службе хранилища Azure."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c02eb32fd12e28d79dcc0340811f4ced48ed4be
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Использовать назначенный пользователем управляемые службы удостоверений (MSI) на виртуальной Машине Linux для доступа к службе хранилища Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Этого учебника показано, как создать и использовать назначенный пользователем управляемые службы удостоверений (MSI) на виртуальной машине Linux, а затем использовать его для доступа к службе хранилища Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создайте учетную запись пользователя, назначенный идентификаторы управляемых служб (MSI)
> * Назначить MSI назначенный пользователем к виртуальной машине Linux
> * Предоставьте доступ MSI в экземпляр хранилища Azure
> * Получить маркер доступа с использованием удостоверения MSI назначенное пользователем и использовать его для доступа к службе хранилища Azure

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Чтобы запустить примеры сценариев CLI в этом учебнике, имеются две возможности:

- Используйте [оболочки облако Azure](~/articles/cloud-shell/overview.md) на портале Azure или посредством «Попробовать» кнопки, расположенные в правом верхнем углу каждого блока кода.
- [Установите последнюю версию CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более поздней версии) Если вы предпочитаете использовать локальные CLI консоли.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

Сначала создается новая виртуальная машина Linux. При желании можно также включить MSI на существующей виртуальной Машины.

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В раскрывающемся списке выберите **подписку** для виртуальной машины.
5. Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

## <a name="create-a-user-assigned-msi"></a>Создание MSI, назначенный пользователем

1. Если вы используете консоль CLI (вместо сеанс оболочки облако Azure), запустите при входе Azure. Используйте учетную запись, которая связана с подпиской Azure, с которой вы хотите создать новый файл MSI:

    ```azurecli
    az login
    ```

2. Создайте назначенное пользователем MSI-ФАЙЛ с помощью [создать удостоверение az](/cli/azure/identity#az_identity_create). `-g` Параметр указывает группу ресурсов, где создается MSI-ФАЙЛ, и `-n` параметр указывает его имя. Обязательно замените `<RESOURCE GROUP>` и `<MSI NAME>` значений параметра со своими собственными значениями:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Ответ содержит подробные назначенный пользователем MSI-ФАЙЛ создан, аналогичный приведенному ниже. Примечание `id` значение для MSI-ФАЙЛЕ, как он будет использоваться в следующем шаге:

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Назначьте пользователю назначен MSI-ФАЙЛЕ ВМ Linux

В отличие от MSI, назначенный системой назначенный пользователем MSI-ФАЙЛ может использоваться клиентами на несколько ресурсов Azure. В этом учебнике можно назначить его одной виртуальной Машины. Можно также назначить более одной виртуальной машине.

Назначьте ВМ Linux с помощью MSI, назначенный пользователем [Назначение идентификаторов виртуальной машины az](/cli/azure/vm#az_vm_assign_identity). Обязательно замените `<RESOURCE GROUP>` и `<VM NAME>` значений параметра со своими собственными значениями. Используйте `id` свойство возвращается на предыдущем шаге для `--identities` значение параметра:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения. 

Если у вас еще нет одного теперь создать учетную запись хранилища. Можно пропустить этот шаг и использовать существующую учетную запись хранилища, если вы предпочитаете. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Нажмите кнопку **хранения**, затем **учетной записи хранилища**, и отображает новой панели «Создание учетной записи хранилища».
3. Введите **имя** учетной записи хранилища, которое можно использовать позже.  
4. Для параметра **Модель развертывания** необходимо выбрать "Resource Manager", а для параметра **Account kind** (Тип учетной записи) — "Общее назначение". 
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.
6. Нажмите кнопку **Создать**.

    ![Создание учетной записи хранения](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Создание контейнера больших двоичных объектов в учетной записи хранения

Поскольку файлы требуют хранилища больших двоичных объектов, необходимо создать контейнер больших двоичных объектов, в котором будет храниться файл. Затем вы загружаете и загрузить файл в контейнере больших двоичных объектов в учетной записи хранения.

1. Вернитесь к только что созданной учетной записи хранения.
2. Щелкните ссылку **Контейнеры** слева в разделе "Служба BLOB-объектов".
3. Щелкните **+ Контейнер** в верхней части страницы, после чего появится панель "Новый контейнер".
4. Присвойте контейнеру имя, выберите уровень доступа, а затем нажмите кнопку **ОК**. Указанное имя также используется далее в этом учебнике. 

    ![Создание контейнера хранилища](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Передайте файл в только что созданный контейнер, затем щелкнув имя контейнера **отправить**, затем выберите файл, а затем нажмите кнопку **отправить**.

    ![Отправка текстового файла](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Предоставить доступ пользователя назначенный MSI в контейнер хранилища Azure

С помощью MSI, ваш код может получить токены доступа для проверки подлинности к ресурсам, которые поддерживают проверку подлинности Azure AD. В этом учебнике используется хранилище Azure.

Можно предоставить доступ удостоверение MSI к контейнер службы хранилища Azure. В этом случае используется контейнера, созданного ранее. Обновите значения для `<MSI CLIENTID>`, `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, и `<CONTAINER NAME>` соответствующим образом для вашей среды. Замените `<CLIENT ID>` с `clientId` свойство, возвращенное `az identity create` в [создания MSI, назначенный пользователем](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI CLIENTID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/<CONTAINER NAME>"
```

Ответ включает сведения для создания назначения ролей:

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

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Получение маркера доступа с помощью MSI назначенный пользователем идентификаторов и использовать его для вызова службы хранилища Azure

Для оставшейся части руководства необходимые для работы от виртуальной Машины, созданной ранее.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Linux и вверху в разделе **Обзор** щелкните **Подключить**. Скопируйте строку подключения к виртуальной машине.
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала с помощью ПЕРЕЛИСТЫВАНИЕ, сделать запрос к локальной конечной точке MSI-ФАЙЛ, чтобы получить маркер доступа для хранилища Azure.

   В следующем примере показан ПЕРЕЛИСТЫВАНИЕ запрос для получения маркера доступа. Обязательно замените `<CLIENT ID>` с `clientId` свойство, возвращенное `az identity create` в [создания MSI, назначенный пользователем](#create-a-user-assigned-msi):

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > В предыдущем запросе значение `resource` параметр должен быть точное соответствие ожидаемого Azure AD. При использовании идентификатор ресурса хранилища Azure, необходимо включить конечную косую черту в URI.
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

4. Теперь можно используйте токен доступа для доступа к службе хранилища Azure, например для считывания содержимого образец файла, который ранее отправленные в контейнер. Замените значения `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, и `<FILE NAME>` со значениями, указанный ранее, и `<ACCESS TOKEN>` с токен, возвращенный на предыдущем шаге.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Ответ содержит содержимое файла:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- Чтобы узнать, как выполнить шаги из этого руководства с помощью учетных данных SAS, см. руководство по [использованию управляемого удостоверения службы виртуальной машины Linux для получения доступа к службе хранилища Azure c помощью учетных данных SAS](msi-tutorial-linux-vm-access-storage-sas.md).
- Дополнительные сведения о возможностях SAS для учетной записи службы хранилища Azure см. в следующих статьях:
  - [Использование подписанных URL-адресов (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Создание подписанного URL-адреса уровня службы](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.






---
title: Получение доступа к Azure Cosmos DB с помощью назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Linux
description: В рамках этого руководства вы узнаете, как получить доступ к Azure Cosmos DB с помощью назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Linux.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dbb5e9e8f9accd618599010ab2bbb4a8760e534f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Получение доступа к Azure Cosmos DB с помощью назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Linux 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве показано, как создавать и использовать назначаемые пользователем управляемые удостоверения службы (MSI) из виртуальной машины Linux, а затем использовать их для получения доступа к Azure Cosmos DB. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание назначаемого пользователем управляемого удостоверения службы (MSI).
> * Присвоение назначаемого пользователем MSI виртуальной машине Linux.
> * Предоставление доступа к экземпляру Azure Cosmos DB для MSI.
> * Получение маркера доступа с помощью назначаемого пользователем удостоверения MSI и его использование для получения доступа к Azure Cosmos DB.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Запустить примеры сценариев CLI в этом руководстве можно двумя способами:

- Использовать службу [Azure Cloud Shell](~/articles/cloud-shell/overview.md) на портале Azure или с помощью кнопки **Попробовать**, расположенной в правом верхнем углу каждого блока кода.
- [Установить последнюю версию интерфейса командной строки (CLI) 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более позднюю версию), если вы предпочитаете использовать локальную консоль CLI.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Linux. Вы также можете активировать MSI на имеющейся виртуальной машине.

1. Выберите **+ Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Вычисление**, а затем — **Ubuntu Server 16.04 VM** (Виртуальная машина Ubuntu Server 16.04 LTS).
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Управляемое удостоверение службы виртуальной машины Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В раскрывающемся списке выберите **подписку** для виртуальной машины.
5. В разделе **Группа ресурсов** выберите **Создать**, а затем введите имя группы ресурсов для виртуальной машины. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". В колонке **параметров** оставьте значения по умолчанию и нажмите кнопку **ОК**.

## <a name="create-a-user-assigned-msi"></a>Создание назначаемого пользователем удостоверения MSI

1. Если вы используете консоль CLI (вместо сеанса Azure Cloud Shell), сначала войдите в Azure. Используйте учетную запись, связанную с подпиской Azure, в рамках которой нужно создать удостоверение MSI:

    ```azurecli
    az login
    ```

2. Создайте назначаемое пользователем MSI с помощью команды [az identity create](/cli/azure/identity#az_identity_create). Параметр `-g` указывает группу ресурсов, в которой создается удостоверение MSI, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<MSI NAME>` собственными:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Ответ содержит подробные сведения о созданном назначаемом пользователем удостоверении MSI, таком как в указанном ниже примере. Запомните значения `clientId` и `id` удостоверения MSI, так как они будут использоваться на следующих этапах.

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Назначение пользовательского удостоверения MSI виртуальной машине Linux

В отличие от удостоверения MSI, назначаемого системой, назначаемое пользователем удостоверение MSI может использоваться клиентами в множестве ресурсов Azure. В этом руководстве мы назначим его отдельной виртуальной машине. Вы также можете назначить удостоверение нескольким виртуальным машинам.

Назначьте пользовательское удостоверение MSI виртуальной машине Linux с помощью команды [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. Используйте свойство `id`, возвращенное на предыдущем шаге, в качестве значения параметра `--identities`:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-cosmos-db-account"></a>Создание учетной записи Cosmos DB 

Если у вас еще нет учетной записи Cosmos DB, создайте ее. Этот шаг можно пропустить и при желании использовать имеющуюся учетную запись Cosmos DB. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Выберите **Databases** (Базы данных),а затем — **Azure Cosmos DB**, после чего отобразится панель New account (Новая учетная запись).
3. Введите **идентификатор** учетной записи Cosmos DB, которая будет использоваться далее.  
4. **API** должно иметь значение SQL. Подход, описанный в этом руководстве, можно использовать с другими доступными типами API, но указанные в этом руководстве действия подходят только для API SQL.
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.  Выберите **расположение**, в котором доступен Cosmos DB.
6. Нажмите кнопку **Создать**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Создание коллекции в учетной записи Cosmos DB

Затем добавьте коллекцию данных в учетную запись Cosmos DB, с помощью которой можно создавать запросы на последующих шагах.

1. Перейдите к созданной учетной записи Cosmos DB.
2. На вкладке **Обзор** нажмите кнопку **Добавить коллекцию**. Откроется панель "Добавить коллекцию".
3. Присвойте коллекции идентификатор базы данных, идентификатор коллекции, выберите емкость хранилища, введите ключ секции и значение пропускной способности, а затем нажмите кнопку **ОК**.  Для этого руководства в качестве идентификатора базы данных и коллекции достаточно будет использовать значение Test. Выберите фиксированное значение емкости хранилища и самую низкую пропускную способность (400 ЕЗ/с).

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Предоставление пользовательским MSI доступа к ключам доступа учетной записи Cosmos DB

В Cosmos DB не встроена поддержка аутентификации Azure AD.  Но вы можете использовать MSI для извлечения ключей доступа к Cosmos DB из Resource Manager, а затем применить эти ключи для получения доступа к Cosmos DB.  На этом шаге вы предоставите пользовательским MSI доступ к ключам учетной записи Cosmos DB.

Сначала предоставьте идентификатору MSI доступ к учетной записи Cosmos DB в Azure Resource Manager с помощью Azure CLI. Обновите `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<COSMOS DB ACCOUNT NAME>` значениями для вашей среды. Замените `<MSI PRINCIPALID>` свойством `principalId`, возвращенным командой `az identity create` в разделе [Создание назначаемого пользователем удостоверения MSI](#create-a-user-assigned-msi).  Cosmos DB поддерживает два уровня детализации при использовании ключей доступа: доступ на чтение и запись для учетной записи и доступ только для чтения для учетной записи.  Назначьте роль `DocumentDB Account Contributor`, если вы хотите получить ключи для записи и чтения для учетной записи, или назначьте роль `Cosmos DB Account Reader Role`, чтобы получить ключи только для чтения для учетной записи.

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Ответ включает сведения о созданных назначениях ролей:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью пользовательского MSI и вызов Azure Resource Manager с его помощью

Далее в этом руководстве мы будем работать с виртуальной машиной, созданной ранее.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Linux и вверху в разделе **Обзор** щелкните **Подключить**. Скопируйте строку подключения к виртуальной машине. 
2. Подключитесь к виртуальной машине c помощью клиента SSH.  
3. После этого вам предложат ввести **пароль**, добавленный при создании **виртуальной машины Linux**. Вы должны без проблем войти в систему.  
4. Используйте cURL для получения маркера доступа для Azure Resource Manager.  

    Запрос CURL маркера доступа и соответствующий ответ приведены ниже.  Замените <CLIENT ID> значением clientId пользовательского MSI. 
    
    ```bash
    curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>" 
    ```
    
    > [!NOTE]
    > В предыдущем запросе значение параметра resource должно точно совпадать со значением, которое ожидает Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    > В следующем ответе элемент access_token сокращен для удобства восприятия.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Получение ключей доступа из Azure Resource Manager для создания вызовов Cosmos DB  

Теперь мы с помощью CURL обратимся к Resource Manager, используя полученный на предыдущем этапе маркер доступа, и получим ключ доступа к учетной записи Cosmos DB. Получив ключ доступа, можно создать запрос к Cosmos DB. Не забудьте заменить значения параметров `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<COSMOS DB ACCOUNT NAME>` своими значениями. Замените значение `<ACCESS TOKEN>` маркером доступа, который вы получили ранее.  Если вы хотите получить ключи для чтения и записи, используйте тип операции ключа `listKeys`.  Если вы хотите получить ключи только для чтения, используйте тип операции ключа `readonlykeys`.

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Текст в приведенном выше URL-адресе чувствителен к регистру, поэтому используйте строчные и заглавные буквы, как указано в названиях групп ресурсов. Кроме того, убедитесь, что используется запрос POST, а не GET, а также, что вы передали значение для регистрации максимальной длины с помощью параметра -d, который может иметь значение NULL.  

В ответе CURL будет содержаться список ключей.  Например, при получении ключей только для чтения вы увидите следующее:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Теперь, когда у вас есть ключ доступа для учетной записи Cosmos DB, вы можете передать его в пакет SDK Cosmos DB и выполнять вызовы для получения доступа к учетной записи.  Чтобы получить краткий пример, вы можете передать ключ доступа в Azure CLI.  Вы можете получить <COSMOS DB CONNECTION URL> на вкладке **Обзор** в колонке учетной записи Cosmos DB на портале Azure.  Замените <ACCESS KEY> полученным выше значением.

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Эта команда CLI возвращает сведения о коллекции.

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об MSI см. в статье [Управляемое удостоверение службы (MSI) для ресурсов Azure](msi-overview.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

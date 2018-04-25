---
title: Доступ к Azure Cosmos DB с помощью управляемого удостоверения службы виртуальной машины Windows
description: В рамках этого руководства вы узнаете, как получить доступ к Azure Cosmos DB с помощью назначаемого системой управляемого удостоверения службы (MSI) на виртуальной машине Windows.
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
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: 0e62e11e0f86714b79cf2bc84bbb505b03df1ba6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="use-a-windows-vm-msi-to-access-azure-cosmos-db"></a>Доступ к Azure Cosmos DB с помощью управляемого удостоверения службы виртуальной машины Windows

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве показано, как создавать и использовать управляемое удостоверение службы (MSI) виртуальной машины Windows для доступа к Cosmos DB. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Windows с включенным MSI. 
> * Создание учетной записи Cosmos DB
> * Предоставление MSI виртуальной машины Windows доступа к ключам доступа учетной записи Cosmos DB.
> * Получение маркера доступа с помощью MSI виртуальной машины Windows для вызова Azure Resource Manager
> * Получение ключей доступа из Azure Resource Manager для создания вызовов Cosmos DB

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Windows в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Windows.  Вы также можете активировать MSI на имеющейся виртуальной машине.

1. Нажмите кнопку **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Вычисления**, а затем — **Windows Server 2016 Datacenter**. 
3. Введите сведения о виртуальной машине. **Имя пользователя** и **пароль**, созданные здесь, используются для входа на виртуальную машину.
4. В раскрывающемся списке выберите нужную **подписку** для виртуальной машины.
5. Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр **Supported disk type** (Поддерживаемые типы диска). На странице параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

   ![Замещающий текст](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине 

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. На самом деле при включении MSI на виртуальной машине с помощью портала Azure выполняются две задачи: регистрация виртуальной машины в Azure AD для создания управляемого удостоверения и его настройка на этой виртуальной машине.

1. Выберите **виртуальную машину**, на которой нужно активировать MSI.  
2. В левой области навигации щелкните **Конфигурация**. 
3. Появится страница **Managed Service Identity** (Управляемое удостоверение службы). Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет". 
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.  
   ![Замещающий текст](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-cosmos-db-account"></a>Создание учетной записи Cosmos DB 

Если у вас еще нет учетной записи Cosmos DB, создайте ее. Этот шаг можно пропустить и использовать имеющуюся учетную запись Cosmos DB. 

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

## <a name="grant-windows-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Предоставление MSI виртуальной машины Windows доступа к ключам доступа учетной записи Cosmos DB.

В Cosmos DB не встроена поддержка аутентификации Azure AD. Но вы можете использовать MSI для извлечения ключей доступа к Cosmos DB из Resource Manager и применить эти ключи для получения доступа к Cosmos DB. На этом шаге вы предоставите MSI доступ к ключам учетной записи Cosmos DB.

Чтобы предоставить MSI доступ к учетной записи Cosmos DB в Azure Resource Manager с помощью PowerShell, укажите значения `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<COSMOS DB ACCOUNT NAME>` для своей среды. Замените `<MSI PRINCIPALID>` свойством `principalId`, возвращенным командой `az resource show` при [получении principalID MSI виртуальной машины Linux](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB поддерживает два уровня детализации при использовании ключей доступа: доступ на чтение и запись для учетной записи и доступ только для чтения для учетной записи.  Назначьте роль `DocumentDB Account Contributor`, если вы хотите получить ключи для записи и чтения для учетной записи, или назначьте роль `Cosmos DB Account Reader Role`, чтобы получить ключи только для чтения для учетной записи.

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-msi-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью MSI виртуальной машины Windows для вызова Azure Resource Manager

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали. 

На этом этапе вы примените командлеты PowerShell для Azure Resource Manager.  Если у вас он не установлен, [скачайте последнюю версию](https://docs.microsoft.com/powershell/azure/overview), прежде чем продолжить.

Вам также потребуется установить последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) на виртуальной машине Windows.

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и вверху в разделе **Обзор** щелкните **Подключить**. 
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте PowerShell в удаленном сеансе.
4. С помощью команды Invoke-WebRequest PowerShell сделайте запрос к локальной конечной точке MSI, чтобы получить маркер доступа к Azure Resource Manager.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    
    Затем извлеките элемент content, который хранится в виде форматированной строки JSON в объекте $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Затем извлеките маркер доступа из ответа.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Получение ключей доступа из Azure Resource Manager для создания вызовов Cosmos DB

Теперь мы с помощью PowerShell обратимся к Resource Manager, используя полученный на предыдущем этапе маркер доступа, и получим ключ доступа к учетной записи Cosmos DB. Получив ключ доступа, можно создать запрос к Cosmos DB. Не забудьте заменить значения параметров `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<COSMOS DB ACCOUNT NAME>` своими значениями. Замените значение `<ACCESS TOKEN>` маркером доступа, который вы получили ранее.  Если вы хотите получить ключи для чтения и записи, используйте тип операции ключа `listKeys`.  Если вы хотите получить ключи только для чтения, используйте тип операции ключа `readonlykeys`.

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
В ответе будет содержаться список ключей.  Например, при получении ключей только для чтения вы увидите следующее:

```powershell
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

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md).

---
title: Доступ к Azure Resource Manager с помощью назначаемого пользователем MSI виртуальной машины Linux
description: Из этого руководства вы узнаете, как получить доступ к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Linux.
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
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2b6d70e441dc3d300f49adff1c02d7cc65788d2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Получение доступа к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Linux

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве объясняется, как создать назначаемое пользователем управляемое удостоверение службы (MSI), назначить его виртуальной машине Linux и затем с помощью этого удостоверения получить доступ к API Azure Resource Manager. 

Управляемые удостоверения службы автоматически управляются платформой Azure. Они обеспечивают проверку подлинности для служб, поддерживающих проверку подлинности Azure AD, без необходимости внедрения учетных данных в код.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание назначаемого пользователем удостоверения MSI
> * Назначение MSI виртуальной машине Linux 
> * Предоставление MSI доступа к группе ресурсов в Azure Resource Manager 
> * Получение маркера доступа с помощью MSI и вызов Azure Resource Manager с его помощью 

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Запустить примеры сценариев CLI в этом руководстве можно двумя способами:

- использовать службу [Azure Cloud Shell](~/articles/cloud-shell/overview.md) на портале Azure или с помощью кнопки "Попробовать", расположенной в правом верхнем углу каждого блока кода;
- [Установить последнюю версию интерфейса командной строки (CLI) 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более позднюю версию), если вы предпочитаете использовать локальную консоль CLI.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В рамках этого руководства мы сначала создадим виртуальную машину Linux. Вы также можете использовать существующую виртуальную машину.

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Создание виртуальной машины Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
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

Назначьте пользовательское удостоверение MSI виртуальной машине Linux с помощью команды [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. Используйте свойство `id`, возвращенное на предыдущем шаге, в качестве значения параметра `--identities`:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Предоставление назначаемому пользователем удостоверению MSI доступа к группе ресурсов в Azure Resource Manager 

MSI предоставляет вашему коду маркер доступа, позволяющий пройти проверку подлинности и получить доступ к API-интерфейсам ресурсов, поддерживающим проверку подлинности Azure AD. В этом руководстве код получает доступ к API Azure Resource Manager. 

Но чтобы ваш код мог получить доступ к API, необходимо предоставить удостоверению MSI доступ к ресурсу в Azure Resource Manager. В этом случае — к группе ресурсов, в которой содержится виртуальная машина. Обновите `<SUBSCRIPTION ID>` и `<RESOURCE GROUP>` значениями, соответствующими вашей среде. Также замените `<MSI PRINCIPALID>` свойством `principalId`, возвращенным командой `az identity create` в разделе [Создание назначаемого пользователем удостоверения MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Ответ содержит подробные сведения о созданном назначении роли, подобные следующему примеру.

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Resource Manager с его помощью 

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Linux и вверху в разделе **Обзор** щелкните **Подключить**. Скопируйте строку подключения к виртуальной машине.
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH.  
3. В окне терминала с помощью cURL выполните запрос к локальной конечной точке MSI, чтобы получить маркер доступа для Azure Resource Manager.  

   В следующем примере показан запрос CURL для получения маркера доступа. Не забудьте заменить `<CLIENT ID>` свойством `clientId`, возвращенным командой `az identity create` в разделе [Создание назначаемого пользователем удостоверения MSI](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Значение параметра `resource` должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI). 
    
    Ответ включает маркер доступа, необходимый для доступа к Azure Resource Manager. 
    
    Пример ответа:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. Теперь используйте маркер для доступа к Azure Resource Manager и считайте свойства группы ресурсов, к которой вы ранее предоставили доступ назначаемому пользователем удостоверению MSI. Не забудьте заменить `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` значениями, указанными ранее, а `<ACCESS TOKEN>` — маркером, возвращенным на предыдущем шаге.

    > [!NOTE]
    > URL-адрес указывается с учетом регистра, поэтому убедитесь, что используется тот же регистр, который использовался при присвоении имени группе ресурсов. Также проверьте, чтобы в значении `resourceGroups` была указана прописная буква "G".  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Ответ содержит сведения об определенной группе ресурсов, подобные следующему примеру: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

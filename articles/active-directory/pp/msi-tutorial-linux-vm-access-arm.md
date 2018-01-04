---
title: "Использование виртуальных Машин Linux назначенный пользователем MSI-ФАЙЛ для доступа к диспетчера ресурсов Azure"
description: "Учебник, в котором описывается процесс использования User-Assigned управляемые службы удостоверений (MSI) на виртуальной Машине Linux, для доступа к диспетчеру ресурсов Azure."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a51d0bdf092893288f2e1cc31a4dcc4117b041c2
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Использовать назначенный пользователем управляемые службы удостоверений (MSI) на виртуальной Машине Linux, для доступа к диспетчеру ресурсов Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Этот учебник способы создания назначенный пользователем управляемые службы удостоверений (MSI), назначьте его для Linux виртуальной машины (VM) и затем использовать его для доступа к API диспетчера ресурсов Azure. 

Управляемые удостоверения службы автоматически управляются Azure. Они позволяют проверку подлинности для служб, которые поддерживают проверку подлинности Azure AD, без необходимости внедрения учетных данных в коде.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание MSI, назначенный пользователем
> * Назначить MSI-ФАЙЛ для виртуальной Машины Linux 
> * Предоставьте доступ MSI в группу ресурсов диспетчера ресурсов Azure 
> * Получение маркера доступа с помощью MSI-ФАЙЛ и использовать его для вызова диспетчера ресурсов Azure 

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Чтобы запустить примеры сценариев CLI в этом учебнике, имеются две возможности:

- Используйте [оболочки облако Azure](~/articles/cloud-shell/overview.md) на портале Azure или посредством «Попробовать» кнопки, расположенные в правом верхнем углу каждого блока кода.
- [Установите последнюю версию CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более поздней версии) Если вы предпочитаете использовать локальные CLI консоли.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В этом учебнике сначала создайте новую ВМ Linux. Вы можете также выбрать использование существующей виртуальной Машины.

1. Щелкните **Создать** в верхнем левом углу портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Создание виртуальной Машины Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Назначьте пользователю назначен MSI-ФАЙЛЕ ВМ Linux

В отличие от MSI, назначенный системой назначенный пользователем MSI-ФАЙЛ может использоваться клиентами на несколько ресурсов Azure. В этом учебнике можно назначить его одной виртуальной Машины. Можно также назначить более одной виртуальной машине.

Назначьте ВМ Linux с помощью MSI, назначенный пользователем [Назначение идентификаторов виртуальной машины az](/cli/azure/vm#az_vm_assign_identity). Обязательно замените `<RESOURCE GROUP>` и `<VM NAME>` значений параметра со своими собственными значениями. Используйте `id` свойство возвращается на предыдущем шаге для `--identities` значение параметра:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Предоставить доступ пользователю назначен MSI в группу ресурсов диспетчера ресурсов Azure 

MSI-ФАЙЛ содержит код с помощью токена доступа для проверки подлинности ресурсов API, которые поддерживают проверку подлинности Azure AD. В этом учебнике код обращается к API диспетчера ресурсов Azure. 

Код может получить доступ к API менее, необходимо предоставить MSI идентификации доступ к ресурсу в диспетчере ресурсов Azure. В этом случае группы ресурсов, в которой содержится виртуальной Машины. Не забудьте заменить значения параметров `<CLIENT ID>`, `<SUBSCRIPTION ID>` и `<RESOURCE GROUP>` своими значениями. Замените `<CLIENT ID>` с `clientId` свойство, возвращенное `az identity create` в [создания MSI, назначенный пользователем](#create-a-user-assigned-msi): 

```azurecli-interactive
az role assignment create --assignee <CLIENT ID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Ответ содержит сведения для назначения ролей, созданных, аналогичный приведенному ниже:

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

   В следующем примере показан ПЕРЕЛИСТЫВАНИЕ запрос для получения маркера доступа. Обязательно замените `<CLIENT ID>` с `clientId` свойство, возвращенное `az identity create` в [создания MSI, назначенный пользователем](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Значение `resource` параметр должен быть точное соответствие ожидаемого Azure AD. При использовании диспетчера ресурсов идентификатор ресурса, необходимо включить конечную косую черту в URI. 
    
    Ответ включает маркер доступа, необходимый для доступа к Azure Resource Manager. 
    
    Пример ответа.  

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

4. Теперь использовать маркер доступа для доступа к диспетчеру ресурсов Azure и прочитать свойства группы ресурсов, к которому ранее предоставлен доступ к MSI назначенный пользователем. Обязательно замените `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` со значениями, указанный ранее, и `<ACCESS TOKEN>` с токен, возвращенный на предыдущем шаге.

    > [!NOTE]
    > URL-адрес с учетом регистра, поэтому следует обязательно использовать точный регистр же вы использовали ранее, когда имя группы ресурсов и верхний регистр «G» в `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Ответ со сведениями о определенной группы ресурсов, аналогичный приведенному ниже: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.
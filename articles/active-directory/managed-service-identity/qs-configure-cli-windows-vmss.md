---
title: Настройка удостоверений, назначаемых системой и пользователем, в масштабируемом наборе виртуальных машин Azure с помощью интерфейса командной строки Azure
description: Пошаговые инструкции по настройке удостоверений, назначаемых системой и пользователем, в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: a0e05543734ae0604149d18564ae1bc1eff1892b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714635"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Настройка управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с Управляемым удостоверением службы в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI:
- включать и отключать назначенное системой удостоверение в масштабируемом наборе виртуальных машин Azure;
- добавлять и удалять назначенное пользователем удостоверение в масштабируемом наборе виртуальных машин Azure.


## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.

Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:

- использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
- использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
- [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать локальную консоль интерфейса командной строки. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Системное удостоверение

В этом разделе вы узнаете, как включить и отключить назначенное системой удостоверение в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Включение назначенного системой удостоверения во время создания масштабируемого набора виртуальных машин Azure

Чтобы создать масштабируемый набор виртуальных машин с включенным удостоверением, назначенным системой, выполните указанные ниже действия.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы сохранить и развернуть масштабируемый набор виртуальных машин и связанные с ним ресурсы, создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью команды [az group create](/cli/azure/group/#az_group_create). Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss/#az_vmss_create). В приведенном ниже примере создается масштабируемый набор виртуальных машин *myVMSS* с назначенным системой удостоверением в соответствии с запросом параметра `--assign-identity`. В параметрах `--admin-username` и `--admin-password` определяются имя и пароль учетной записи администратора для входа в виртуальную машину. Подставьте соответствующие значения для своей среды: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Включение назначенного системой удостоверения в существующем масштабируемом наборе виртуальных машин Azure

Если нужно включить назначенное системой удостоверение в существующем масштабируемом наборе виртуальных машин Azure, выполните указанные ниже действия.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы включить назначенное системой удостоверение в имеющейся виртуальной машине, используйте команду [az vmss identity assign](/cli/azure/vmss/identity/#az_vmss_identity_assign):

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение назначенного системой удостоверения в масштабируемом наборе виртуальных машин Azure

> [!NOTE]
> Отключение управляемого удостоверения службы в масштабируемом наборе виртуальных машин сейчас не поддерживается. А пока вы можете переключаться между назначенным системой и пользовательским удостоверениями. Загляните сюда позже, чтобы проверить наличие новой информации.

Если в масштабируемом наборе виртуальных машин больше не требуется назначенное системой удостоверение, но по-прежнему требуется удостоверение, назначенное пользователем, используйте следующую команду:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Чтобы удалить расширение MSI для виртуальной машины, используйте команду [az vmss identity remove](/cli/azure/vmss/identity/#az_vmss_remove_identity) для удаления назначенного системой удостоверения из масштабируемого набора виртуальных машин:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы узнаете, как добавить и удалить назначенное пользователем удостоверение с помощью Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Добавление назначенного пользователем удостоверения во время создания масштабируемого набора виртуальных машин Azure

В этом разделе описывается создание масштабируемого набора виртуальных машин и присвоение ему назначенного пользователем удостоверения. Если у вас уже есть масштабируемый набор виртуальных машин, который можно использовать, пропустите этот раздел и перейдите к следующему.

1. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить. Создайте [группу ресурсов](~/articles/azure-resource-manager/resource-group-overview.md#terminology) для хранения и развертывания назначенного пользователем удостоверения, используя команду [az group create](/cli/azure/group/#az_group_create). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<LOCATION>` собственными. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Создайте назначенное пользователем удостоверение с помощью команды [az identity create](/cli/azure/identity#az-identity-create).  Параметр `-g` указывает группу ресурсов, в которой создается назначенное пользователем удостоверение, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Ответ содержит подробные сведения о созданном назначенном пользователем удостоверении, подобные приведенным ниже. Значение `id` ресурса, присвоенное назначенному пользователем удостоверению, используется в следующем шаге.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss/#az-vmss-create). В приведенном ниже примере создается масштабируемый набор виртуальных машин, связанный с новым назначенным пользователем удостоверением, в соответствии с параметром `--assign-identity`. Не забудьте заменить значения параметров `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` и `<USER ASSIGNED IDENTITY ID>` своими значениями. В качестве `<USER ASSIGNED IDENTITY ID>` используйте свойство ресурса `id` назначенного пользователем удостоверения, созданное в предыдущем шаге: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Присвоение назначенного пользователем удостоверения имеющейся виртуальной машине Azure

1. Создайте назначенное пользователем удостоверение с помощью команды [az identity create](/cli/azure/identity#az-identity-create).  Параметр `-g` указывает группу ресурсов, в которой создается назначенное пользователем удостоверение, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными:

    > [!IMPORTANT]
    > При создании назначенных пользователем удостоверений не поддерживается использование специальных символов (например, символа подчеркивания) в имени. Используйте буквенно-цифровые символы. Загляните сюда позже, чтобы проверить наличие новой информации.  Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Ответ содержит подробные сведения о созданном назначенном пользователем удостоверении, подобные приведенным ниже. Значение `id` ресурса, присвоенное назначенному пользователем удостоверению, используется в следующем шаге.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Присвойте назначенное пользователем удостоверение масштабируемому набору виртуальных машин с помощью команды [az vmss identity assign](/cli/azure/vmss/identity#az_vm_assign_identity). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<USER ASSIGNED IDENTITY ID>` будет свойством ресурса `id` назначенного пользователем удостоверения, созданным в предыдущем шаге:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Удаление назначенного пользователем удостоверения из масштабируемого набора виртуальных машин Azure

> [!NOTE]
>  В настоящее время удалить все назначенные пользователем удостоверения из масштабируемого набора виртуальных машин можно только при наличии назначенного системой удостоверения. 

Если у масштабируемого набора виртуальных машин несколько назначенных пользователем удостоверений, с помощью команды [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove) можно удалить все удостоверения, кроме последнего. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<MSI NAME>` — это имя назначенного пользователем удостоверения, которое можно найти в разделе удостоверений виртуальной машины с помощью команды `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <MSI NAME>
```
Если у масштабируемого набора виртуальных машин есть как назначенное системой, так и пользовательское удостоверения, вы можете удалить все пользовательские удостоверения, переключившись на использование только назначенного системой. Используйте следующую команду: 

```azurecli-interactive
az vmss update -n <VMSS NAME> -g <RESOURCE GROUP> --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>Дополнительная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md)
- Полное руководство по созданию масштабируемого набора виртуальных машин см. здесь: 

  - [Создание масштабируемого набора виртуальных машин с помощью CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)


















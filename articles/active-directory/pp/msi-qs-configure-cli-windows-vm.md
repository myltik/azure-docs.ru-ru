---
title: "Настройка MSI, назначенный пользователем для ВМ Azure с помощью Azure CLI"
description: "Пошагово, пошаговые инструкции по настройке назначенный пользователем управляемые службы удостоверений (MSI) для виртуальной Машины Azure, с помощью Azure CLI."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2aed60c2b35d750c892bc61c0e2693d6407c641f
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Настройка назначенный пользователем управляемые службы удостоверений (MSI) для виртуальной Машины, с помощью Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет служб Azure с использованием управляемого удостоверения Azure Active Directory. Этот идентификатор можно использовать для проверки подлинности для служб, которые поддерживают проверку подлинности Azure AD, без использования учетных данных в коде. 

В этой статье вы узнаете, как включить и удалить MSI, назначенный пользователем для виртуальной Машины Azure, с помощью Azure CLI.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Чтобы запустить примеры сценариев CLI в этом учебнике, имеются две возможности:

- Используйте [оболочки облако Azure](~/articles/cloud-shell/overview.md) на портале Azure или посредством «Попробовать» кнопки, расположенные в правом верхнем углу каждого блока кода.
- [Установите последнюю версию CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более поздней версии) Если вы предпочитаете использовать локальные CLI консоли. Затем войдите в Azure с помощью [входа az](/cli/azure/#login). Используйте учетную запись, которая связана с подпиской Azure, под которой вы хотите развернуть MSI, назначенный пользователем и виртуальной Машины:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Включение MSI во время создания виртуальной машины Azure

В этом разделе описывается создание виртуальной машины и назначение назначенный пользователем MSI-ФАЙЛ для виртуальной Машины. При наличии виртуальной Машины требуется использовать, пропустите этот раздел и перейти к следующему.

1. Этот шаг можно пропустить, если уже есть группа ресурсов, которые вы хотите использовать. Создание [группы ресурсов](~/articles/azure-resource-manager/resource-group-overview.md#terminology) для включения и развертывания MSI-ФАЙЛЕ, с помощью [Создание группы az](/cli/azure/group/#create). Обязательно замените `<RESOURCE GROUP>` и `<LOCATION>` значений параметра со своими собственными значениями. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Создайте назначенное пользователем MSI-ФАЙЛ с помощью [создать удостоверение az](/cli/azure/identity#az_identity_create).  `-g` Параметр указывает группу ресурсов, где создается MSI-ФАЙЛ, и `-n` параметр указывает его имя. Обязательно замените `<RESOURCE GROUP>` и `<MSI NAME>` значений параметра со своими собственными значениями:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Ответ содержит подробные назначенный пользователем MSI-ФАЙЛ создан, аналогично приведенным ниже. Ресурс `id` значение, присвоенное MSI-ФАЙЛ используется в следующем шаге.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm/#create). В следующем примере создается виртуальная машина, связанный с новой назначенный пользователем MSI-ФАЙЛ, в соответствии с `--assign-identity` параметра. Обязательно замените `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, и `<`MSI-ID >` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `идентификатор "Свойства, созданного на предыдущем шаге: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Включение MSI на имеющейся виртуальной машине Azure

1. Создайте назначенное пользователем MSI-ФАЙЛ с помощью [создать удостоверение az](/cli/azure/identity#az_identity_create).  `-g` Параметр указывает группу ресурсов, где создается MSI-ФАЙЛ, и `-n` параметр указывает его имя. Обязательно замените `<RESOURCE GROUP>` и `<MSI NAME>` значений параметра со своими собственными значениями:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Ответ содержит подробные назначенный пользователем MSI-ФАЙЛ создан, аналогично приведенным ниже. Ресурс `id` значение, присвоенное MSI-ФАЙЛ используется в следующем шаге.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Назначьте виртуальной Машины с помощью MSI, назначенный пользователем [Назначение идентификаторов виртуальной машины az](/cli/azure/vm#az_vm_assign_identity). Обязательно замените `<RESOURCE GROUP>` и `<VM NAME>` значений параметра со своими собственными значениями. `<MSI ID>` Будет MSI назначенный пользователем ресурсов `id` свойства, как на предыдущем шаге:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Удаление MSI с виртуальной машины Azure

1. Удалите из виртуальной Машины с помощью MSI, назначенный пользователем [az remove удостоверение виртуальной машины](/cli/azure/vm#az_vm_remove_identity). Обязательно замените `<RESOURCE GROUP>` и `<VM NAME>` значений параметра со своими собственными значениями. `<MSI NAME>` Будет MSI назначенный пользователем `name` свойства, заданной во время `az identity create` команды (см. Примеры в предыдущих разделах):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>Дальнейшие действия

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure: 

  - [Создание виртуальной машины Windows с помощью Azure CLI](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Создание виртуальной машины Linux с помощью Azure CLI](~/articles/virtual-machines/linux/quick-create-cli.md) 

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

















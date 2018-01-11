---
title: "Как назначить доступ назначенный пользователем MSI-ФАЙЛ в ресурс Azure, с помощью Azure CLI"
description: "Шаг за шагом инструкции для назначения MSI, назначенный пользователем, на один ресурс, доступ к другим ресурсом, с помощью Azure CLI."
services: active-directory
documentationcenter: 
author: bryanLa
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
ms.openlocfilehash: 732fc1981bdf95e7548ea0ebe0ca8ece00f483ce
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Назначить назначенный пользователем управляемые службы удостоверений (MSI) доступ к ресурсу, с помощью Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

После создания MSI, назначенный пользователем, можно предоставить доступ MSI-ФАЙЛ на другой ресурс, так же, как и любого участника безопасности. В этом примере показано, как для предоставления доступа пользователю назначен MSI-ФАЙЛ учетной записи хранилища Azure, с помощью Azure CLI.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Чтобы запустить примеры сценариев CLI в этом учебнике, имеются две возможности:

- Используйте [оболочки облако Azure](~/articles/cloud-shell/overview.md) на портале Azure или посредством «Попробовать» кнопки, расположенные в правом верхнем углу каждого блока кода.
- [Установите последнюю версию CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более поздней версии) Если вы предпочитаете использовать локальные CLI консоли. Затем войдите в Azure с помощью [входа az](/cli/azure/#login). Используйте учетную запись, которая связана с подпиской Azure, под которой вы хотите развернуть MSI, назначенный пользователем и виртуальной Машины:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Использование RBAC для назначения доступа на основе MSI другому ресурсу

Чтобы использовать MSI-ФАЙЛ с ресурса узла (например, виртуальной Машины), для доступа на вход или ресурса, MSI-ФАЙЛ необходимо сначала предоставить доступ к ресурсам через назначение ролей. Это можно сделать, прежде чем связывать MSI-ФАЙЛ с узлом или после. Все шаги по созданию и связывание с виртуальной Машиной, в разделе [Настройка MSI, назначенный пользователем для виртуальной Машины, с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

В следующем примере MSI, назначенный пользователь получает доступ к учетной записи хранилища.  

1. Чтобы предоставить доступ MSI, требуется идентификатор клиента (также известный как идентификатор приложения) MSI участника-службы. Клиент указал идентификатор [создать удостоверение az](/cli/azure/identity#az_identity_create), после подготовки MSI-ФАЙЛ и его участника-службы (показано ниже для справки):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Успешный ответ содержит идентификатор клиентского MSI назначенный пользователем службы субъекта в `clientId` свойство:

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. После получения идентификатора клиента, используйте [создать назначение ролей az](/cli/azure/role/assignment#az_role_assignment_create) назначен доступ MSI-ФАЙЛ на другой ресурс. Не забудьте использовать идентификатор клиента для `--assignee` параметр и сопоставления идентификатор подписки группы ресурсов имя, возвращаемое при выполнении `az identity create`. Здесь MSI назначена «Чтения» доступа учетной записи хранилища, называется «myStorageAcct»:

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Успешный ответ выглядит примерно следующие результаты:

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- Чтобы MSI, назначенный пользователем, на Виртуальной машине Azure, см. [настроить назначенное пользователем управляемые службы удостоверений (MSI) для виртуальной Машины, с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.


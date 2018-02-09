---
title: "Как назначить доступ на основе назначаемого пользователем удостоверения MSI к ресурсу Azure с помощью Azure CLI"
description: "Пошаговые инструкции по назначению пользовательского удостоверения MSI для одного ресурса и предоставлению доступа к другому ресурсу с помощью Azure CLI."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5bea41999f59fe8be7ae0a0bd5b726527beeddd5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Назначение доступа на основе пользовательского управляемого удостоверения службы (MSI) к ресурсу с помощью Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

После создания пользовательского MSI можно предоставить доступ на основе MSI к другому ресурсу, как и любому субъекту безопасности. В этом примере показано, как предоставить доступ на основе пользовательского MSI к учетной записи хранения Azure с помощью Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Запустить примеры сценариев CLI в этом руководстве можно двумя способами:

- использовать службу [Azure Cloud Shell](~/articles/cloud-shell/overview.md) на портале Azure или с помощью кнопки "Попробовать", расположенной в правом верхнем углу каждого блока кода;
- [Установить последнюю версию интерфейса командной строки (CLI) 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 или более позднюю версию), если вы предпочитаете использовать локальную консоль CLI. Затем войдите в Azure с помощью команды [az login](/cli/azure/#az_login). Используйте учетную запись, которая связана с подпиской Azure, с помощью которой нужно развернуть назначаемое пользователем MSI и виртуальную машину:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Использование RBAC для назначения доступа на основе MSI другому ресурсу

Чтобы использовать MSI с ресурсом узла (например, виртуальной машиной) для доступа на вход или к ресурсу, сначала необходимо предоставить MSI доступ к ресурсам, назначив роль. Это можно сделать до или после связывания MSI с узлом. Все шаги по созданию и связыванию с виртуальной машиной см. в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

В следующем примере на основе пользовательского MSI предоставляется доступ к учетной записи хранилища.  

1. Чтобы предоставить доступ на основе MSI, вам потребуется идентификатор клиента (также называется идентификатором приложения) субъекта-службы MSI. Идентификатор клиента предоставляется командой [az identity create](/cli/azure/identity#az_identity_create) во время подготовки MSI и его субъекта-службы (показано ниже для справки):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Ответ об успешном выполнении содержит идентификатор клиента субъекта-службы пользовательского MSI в свойстве `clientId`.

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

2. Получив идентификатор клиента, выполните команду [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), чтобы назначить доступ на основе MSI к другому ресурсу. Обязательно укажите идентификатор клиента для параметра `--assignee`, а также соответствующий идентификатор подписки и имя группы ресурсов, возвращенные при выполнении команды `az identity create`. В этом примере на основе MSI назначается доступ на чтение к учетной записи хранения с именем myStorageAcct:

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Ответ об успешном выполнении выглядит примерно так:

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

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- Инструкции по включению пользовательского MSI на виртуальной машине Azure см. в статье [Настройка назначаемого пользователем управляемого удостоверения службы (MSI) для виртуальной машины с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.


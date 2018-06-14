---
title: Как назначить доступ на основе MSI к ресурсу Azure с помощью Azure CLI
description: Пошаговые инструкции по назначению MSI для одного ресурса и предоставлению доступа другому ресурсу с помощью Azure CLI.
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
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: 947e0140c7943954be5eb285bb7ec514b74e9022
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929648"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Назначение доступа на основе управляемого удостоверения службы (MSI) для ресурса с помощью Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

После настройки MSI для ресурса Azure можно предоставить доступ на основе MSI другому ресурсу, как и любому субъекту безопасности. В этом примере показано, как предоставить виртуальной машине или масштабируемому набору виртуальных машин Azure доступ на основе MSI к учетной записи хранения Azure с помощью Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:

- использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
- использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
- [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать локальную консоль интерфейса командной строки. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Использование RBAC для назначения доступа на основе MSI другому ресурсу

После включения MSI в ресурсе Azure, например на [виртуальной машине Azure](qs-configure-cli-windows-vm.md) или в [масштабируемом наборе виртуальных машин Azure](qs-configure-cli-windows-vmss.md), сделайте следующее: 

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть виртуальную машину или масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. В этом примере мы предоставляем виртуальной машине Azure доступ к учетной записи хранения. Сначала мы используем команду [az resource list](/cli/azure/resource/#az_resource_list), чтобы получить субъект-службу для виртуальной машины с именем myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Для масштабируемого набора виртуальных машин Azure используется та же команда. Только в результате ее выполнения вы получите масштабируемый набор виртуальных машин с именем DevTestVMSS:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. После получения идентификатора субъекта-службы выполните команду [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), чтобы предоставить этой виртуальной машине или масштабируемому набору виртуальных машин доступ для чтения к учетной записи хранения myStorageAcct:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Устранение неполадок

Если MSI для ресурса не отображается в списке доступных удостоверений, убедитесь, что вы правильно включили MSI. В нашем случае можно вернуться к виртуальной машине или масштабируемому набору виртуальных машин на [портале Azure](https://portal.azure.com) и сделать следующее:

- просмотреть страницу "Конфигурация" и убедиться, что указан параметр "MSI enabled" = "Yes";
- просмотреть страницу "Расширения" и убедиться, что расширение MSI успешно развернуто (страница **Расширения** недоступна для масштабируемого набора виртуальных машин Azure).

Если какое-либо из условий не выполнено, может потребоваться повторно развернуть MSI для ресурса или устранить неполадки развертывания.

## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md).
- Чтобы включить MSI для виртуальной машины Azure, см. инструкции в статье о [настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md).
- Чтобы включить MSI для масштабируемого набора виртуальных машин, см. инструкции в статье о [настройке управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин с помощью портала Azure](qs-configure-portal-windows-vmss.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.


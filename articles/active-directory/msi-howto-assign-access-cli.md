---
title: "Как назначить доступ на основе MSI к ресурсу Azure с помощью Azure CLI"
description: "Пошаговые инструкции по назначению MSI для одного ресурса и предоставлению доступа другому ресурсу с помощью Azure CLI."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e6eede1c093145894f4330a0c4385969cd4dd7da
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---

# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Назначение доступа на основе управляемого удостоверения службы (MSI) для ресурса с помощью Azure CLI

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

После настройки MSI для ресурса Azure можно предоставить доступ на основе MSI другому ресурсу, как и любому субъекту безопасности. В этом примере показано, как предоставить виртуальной машине Azure доступ на основе MSI к учетной записи хранения Azure с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:

- использовать [Azure Cloud Shell](../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
- использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
- [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать локальную консоль интерфейса командной строки. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Использование RBAC для назначения доступа на основе MSI другому ресурсу

После включения MSI для ресурса Azure, [например виртуальной машины Azure](msi-qs-configure-cli-windows-vm.md): 

1. Если вы не используете Azure Cloud Shell с портала Azure, то сначала войдите в Azure с помощью команды [az login](/cli/azure/#login). Используйте учетную запись, которая связана с подпиской Azure, с помощью которой нужно развернуть виртуальную машину.

   ```azurecli-interactive
   az login
   ```

2. В этом примере мы предоставляем виртуальной машине Azure доступ к учетной записи хранения. Сначала с помощью команды [az resource list](/cli/azure/resource/#list) мы получаем субъект-службу для виртуальной машины myVM, который был создан при включении MSI для виртуальной машины.

   ```azurecli-interactive
   $spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. После получения идентификатора субъекта-службы мы выполняем команду [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), чтобы предоставить этой виртуальной машине доступ для чтения к учетной записи хранения myStorageAcct.

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Устранение неполадок

Если MSI для ресурса не отображается в списке доступных удостоверений, убедитесь, что вы правильно включили MSI. В нашем случае мы можем вернуться к виртуальной машине Azure на [портале Azure](https://portal.azure.com) и:

- просмотреть страницу "Конфигурация" и убедиться, что указан параметр "MSI enabled" = "Yes";
- просмотреть страницу "Расширения" и убедиться, что расширение MSI успешно развернуто.

Если какое-либо из условий не выполнено, может потребоваться повторно развернуть MSI для ресурса или устранить неполадки развертывания.

## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- О том, как включить MSI для виртуальной машины Azure, можно узнать в разделе [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.



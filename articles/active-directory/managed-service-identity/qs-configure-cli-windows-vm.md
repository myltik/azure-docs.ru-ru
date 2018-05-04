---
title: Настройка MSI на виртуальной машине Azure с помощью Azure CLI
description: Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: c76d53d32b297ff106c05bdd717a80a9f4b98814
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для виртуальных машин Azure с помощью Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:

- использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
- использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
- [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать локальную консоль интерфейса командной строки. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Включение MSI во время создания виртуальной машины Azure

Чтобы создать виртуальную машину с поддержкой MSI:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, которая связана с подпиской Azure, с помощью которой нужно развернуть виртуальную машину.

   ```azurecli-interactive
   az login
   ```

2. Создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью параметра [az group create](/cli/azure/group/#az_group_create), чтобы сохранить и развернуть виртуальную машину и связанные с ней ресурсы. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm/#az_vm_create). В следующем примере создается виртуальная машина с именем *myVM* с MSI, как запрошено параметром `--assign-identity`. В параметрах `--admin-username` и `--admin-password` определяются имя и пароль учетной записи администратора для входа в виртуальную машину. Подставьте соответствующие значения для своей среды: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Включение MSI на имеющейся виртуальной машине Azure

Если нужно включить MSI на имеющейся виртуальной машине, сделайте следующее:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```azurecli-interactive
   az login
   ```

2. Чтобы добавить MSI на существующую виртуальную машину, используйте команду [az vmss identity assign](/cli/azure/vm/identity/#az_vm_identity_assign) с `identity assign`:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Удаление MSI с виртуальной машины Azure

При наличии виртуальной машины, которой больше не требуется MSI, сделайте следующее:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```azurecli-interactive
   az login
   ```

2. Чтобы удалить MSI, выполните команду [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) с параметром `-n ManagedIdentityExtensionForWindows` или `-n ManagedIdentityExtensionForLinux` (в зависимости от типа виртуальной машины).

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Связанная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure: 

  - [Создание виртуальной машины Windows с помощью Azure CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Создание виртуальной машины Linux с помощью Azure CLI](../../virtual-machines/linux/quick-create-cli.md) 

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

















---
title: "Настройка MSI в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI"
description: "Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI."
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: c58ad9cc8bfa16b11201735bcc513e6dd692a2a9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Настройка управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для в масштабируемого набора виртуальных машин Azure с помощью Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:

- использовать [Azure Cloud Shell](../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
- использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
- [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать локальную консоль интерфейса командной строки. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Включение MSI во время создания масштабируемого набора виртуальных машин Azure

Чтобы масштабируемый набор виртуальных машин с поддержкой MSI, выполните следующие действия:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/#az_login). Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы сохранить и развернуть масштабируемый набор виртуальных машин и связанные с ним ресурсы, создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md#terminology) с помощью команды [az group create](/cli/azure/group/#az_group_create). Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss/#az_vmss_create). В следующем примере создается масштабируемый набор виртуальных машин с именем *myVMSS* с MSI в соответствии с запросом параметра `--assign-identity`. В параметрах `--admin-username` и `--admin-password` определяются имя и пароль учетной записи администратора для входа в виртуальную машину. Подставьте соответствующие значения для своей среды: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Включение MSI в существующем масштабируемом наборе виртуальных машин Azure

Чтобы включить MSI в существующем масштабируемом наборе виртуальных машин Azure, выполните следующие действия:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/#az_login). Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы добавить MSI на существующую виртуальную машину, выполните команду [az vm assign-identity](/cli/azure/vm/#az_vmss_assign_identity) с параметром `--assign-identity`:

   ```azurecli-interactive
   az vmss assign-identity -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Удаление MSI из масштабируемого набора виртуальных машин Azure

Если для масштабируемого набора виртуальных машин MSI больше не требуется, сделайте следующее:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/#az_login). Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы удалить MSI, выполните команду [az vmss remove-identity](/cli/azure/vmss/#az_vmss_remove_identity) с параметром `--identities`.

   ```azurecli-interactive
   az vmss remove-identity -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Дополнительная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md)
- Полное руководство по созданию масштабируемого набора виртуальных машин см. здесь: 

  - [Создание масштабируемого набора виртуальных машин с помощью CLI](../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

















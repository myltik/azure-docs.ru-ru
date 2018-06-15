---
title: Управление пользовательским управляемым удостоверением службы (MSI) с помощью Azure CLI
description: Пошаговые инструкции по созданию, получению списка и удалению пользовательских управляемых удостоверений службы с помощью Azure CLI.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 24172ebac8c7f124d0873b9d93d260fa2e1a8a44
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594617"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Создание, получение списка и удаление пользовательских удостоверений с помощью Azure CLI.

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет службы Azure с управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

В этой статье вы узнаете, как создавать, получать список и удалять пользовательские удостоверения с помощью Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.

- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:

    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
    - [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать локальную консоль интерфейса командной строки. Войдите в Azure с помощью `az login`, используйте учетную запись, которая связана с подпиской Azure, с помощью которой нужно развернуть пользовательское удостоверение.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого пользовательского удостоверения 

Для создания пользовательского удостоверения используйте команду [az identity create](/cli/azure/identity#az-identity-create). Параметр `-g` указывает группу ресурсов, в которой создано пользовательское удостоверение, а параметр `-n` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Получение списка пользовательских удостоверений

Для получения списка пользовательских удостоверений используйте команду [az identity list](/cli/azure/identity#az-identity-list).  Параметр `-g` указывает группу ресурсов, в которой было создано пользовательское удостоверение.  Замените `<RESOURCE GROUP>` собственным значением:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
В ответе JSON пользовательские удостоверения содержат значение `"Microsoft.ManagedIdentity/userAssignedIdentities"`, возвращаемое для ключа `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Удаление пользовательского удостоверения

Для удаления пользовательского удостоверения используйте команду [az identity delete](/cli/azure/identity#az-identity-delete).  Параметр -n указывает имя, а параметр -g — группу ресурсов, в которой было создано пользовательское удостоверение.  Замените значения параметров `<USER ASSIGNED IDENTITY NAME>` и `<RESOURCE GROUP>` собственными:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Удаление пользовательского удостоверения не удаляет ссылки из ресурса, которому оно было назначено. Удалите их из виртуальной машины или VMSS с помощью команды `az vm/vmss identity remove`.

## <a name="related-content"></a>Связанная информация

Полный список команд Azure CLI, используемых для работы с удостоверениями, см. в разделе [az identity](/cli/azure/identity).

Сведения о том, как назначить пользовательское удостоверение виртуальной машине Azure, см. в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-identity).


 

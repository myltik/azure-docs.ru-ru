---
title: Настройка MSI в масштабируемом наборе виртуальных машин Azure с помощью шаблона
description: Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин Azure с помощью шаблона Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: f7c5d063bfb287de9afe808395b951ecb161da69
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930618"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Настройка Управляемого удостоверения службы в масштабируемом наборе виртуальных машин с помощью шаблона

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с Управляемым удостоверением службы в масштабируемом наборе виртуальных машин Azure с помощью шаблона развертывания Azure Resource Manager:
- включать и отключать назначенное системой удостоверение в масштабируемом наборе виртуальных машин Azure;
- добавлять и удалять назначенное пользователем удостоверение в масштабируемом наборе виртуальных машин Azure.

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с Управляемым удостоверением службы, см. [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.

## <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Так же как портал Azure и скрипты, шаблоны [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) предоставляют возможность развертывать новые и измененные ресурсы, определенные в группе ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Включение MSI для новой или существующей виртуальной машины выполняется схожим образом. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) для развертываний.

## <a name="system-assigned-identity"></a>Удостоверение, назначенное системой

В этом разделе вы узнаете, как включить и отключить назначенное системой удостоверение с помощью шаблона Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Включение назначенного системой удостоверения во время создания масштабируемого набора виртуальных машин Azure или в существующем масштабируемом наборе виртуальных машин Azure

1. Загрузив шаблон в редактор, найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Имя вашего ресурса может немного отличаться от указанного на этом снимке экрана, в зависимости от используемого редактора и от того, изменяете ли вы шаблон для нового или имеющегося развертывания.
   
   ![Снимок экрана шаблона: поиск виртуальной машины](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Чтобы включить назначенное системой удостоверение, добавьте свойство `"identity"` на том же уровне, что и свойство `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Используйте следующий синтаксис:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. Добавьте расширение MSI для масштабируемого набора виртуальных машин как элемент `extensionsProfile` (необязательно). Этот шаг необязателен, так как для получения токенов можно также использовать удостоверение Службы метаданных экземпляров Azure (IMDS).  Используйте следующий синтаксис:

   >[!NOTE] 
   > В следующем примере предполагается, что развертывается расширение масштабируемого набора виртуальных машин Windows (`ManagedIdentityExtensionForWindows`). Можно также выполнить настройку для Linux, используя расширение `ManagedIdentityExtensionForLinux` для элементов `"name"` и `"type"`.
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
   ```

4. По завершении шаблон должен выглядеть следующим образом.

   ![Снимок экрана шаблона после изменения](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение назначенного системой удостоверения в масштабируемом наборе виртуальных машин Azure

> [!NOTE]
> Отключение Управляемого удостоверения службы в виртуальной машине в настоящее время не поддерживается. А пока вы можете переключаться между назначенным системой и пользовательским удостоверениями.

Если в масштабируемом наборе виртуальных машин больше не требуется назначенное системой удостоверение, но по-прежнему требуются удостоверения, назначенные пользователем:

- загрузите шаблон в редактор и измените тип удостоверения на `'UserAssigned'`.

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы присвоите назначенное пользователем удостоверение масштабируемому набору виртуальных машин Azure с помощью шаблона Azure Resource Manager.

> [!Note]
> Сведения о создании назначенного пользователем удостоверения с помощью шаблона Azure Resource Manager см. в разделе [Создание назначенного пользователем удостоверения](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Присвоение назначенного пользователем удостоверения масштабируемому набору виртуальных машин Azure

1. Чтобы присвоить назначенное пользователем удостоверение масштабируемому набору виртуальных машин, в элементе `resources` добавьте приведенную ниже запись.  Не забудьте заменить `<USERASSIGNEDIDENTITY>` на имя созданного удостоверения, назначенного пользователем.

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITY>)']"
            ]
        }

    }
    ```
2. Чтобы назначить расширение управляемого удостоверения масштабируемому набору виртуальных машин, добавьте в элемент приведенную ниже запись `extensionProfile` (необязательно). Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS). Используйте следующий синтаксис:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
                    "properties": {
                        "publisher": "Microsoft.ManagedIdentity",
                        "type": "ManagedIdentityExtensionForWindows",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "port": 50342
                        },
                        "protectedSettings": {}
                    }
                }
   ```
3.  По завершении шаблон должен выглядеть следующим образом:
   
      ![Снимок экрана с назначенным пользователем удостоверением](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>Дополнительная информация

- Чтобы получить более обширное представление о MSI, прочитайте раздел [Управляемое удостоверение службы (MSI) для ресурсов Azure](overview.md).


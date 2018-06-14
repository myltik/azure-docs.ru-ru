---
title: Настройка назначаемого пользователем удостоверения MSI для виртуальной машины Azure с помощью шаблона Azure
description: Пошаговые инструкции по настройке назначаемого пользователем управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью шаблона Azure Resource Manager.
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e01e4c397e0d0a19280a32fc1e8341b57b47e4eb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984045"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Настройка назначаемого пользователем управляемого удостоверения службы (MSI) для виртуальной машины с помощью шаблона Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет службы Azure с управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

Из этой статьи вы узнаете, как включить и удалить назначаемое пользователем удостоверение MSI для виртуальной машины Azure с помощью шаблона развертывания Azure Resource Manager.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Включение MSI во время создания виртуальной машины Azure или на имеющейся виртуальной машине

Как портал Azure и сценарии, шаблоны Azure Resource Manager предоставляют возможность развертывать новые и измененные ресурсы, определенные группой ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Создание и назначение пользовательского MSI для новой или существующей виртуальной машины выполняется таким же образом. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) для развертываний.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей MSI и виртуальную машину. Кроме того, убедитесь, что ваша учетная запись принадлежит роли, которая предоставляет разрешения на запись в подписке или ресурсах (например, "Владелец").

2. Загрузив шаблон в редактор, найдите ресурс `Microsoft.Compute/virtualMachines` в разделе `resources`. Имя вашего ресурса может немного отличаться от указанного на этом снимке экрана, в зависимости от используемого редактора и от того, изменяете ли вы шаблон для нового или имеющегося развертывания.

   >[!NOTE] 
   > В этом примере предполагается, что используются переменные `vmName`, `storageAccountName` и `nicName`, определенные в шаблоне.
   >

   ![Снимок экрана шаблона: поиск виртуальной машины](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Добавьте свойство `"identity"` на том же уровне, что и свойство `"type": "Microsoft.Compute/virtualMachines"`. Используйте следующий синтаксис:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Затем добавьте расширение MSI для виртуальной машины в качестве элемента `resources`. Используйте следующий синтаксис:

   >[!NOTE] 
   > В следующем примере предполагается, что расширение виртуальной машины Windows (`ManagedIdentityExtensionForWindows`) развертывается. Можно также выполнить настройку для Linux, используя расширение `ManagedIdentityExtensionForLinux` для элементов `"name"` и `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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

5. По завершении шаблон должен выглядеть следующим образом.

   ![Снимок экрана шаблона после изменения](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Удаление MSI с виртуальной машины Azure

При наличии виртуальной машины, которой больше не требуется MSI, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей виртуальную машину. Кроме того, убедитесь, что ваша учетная запись принадлежит роли, которая предоставляет разрешения на запись в виртуальной машине (например, "Участник виртуальных машин").

2. Удалите два элемента, добавленные в предыдущем разделе: свойство виртуальной машины `"identity"` и ресурс `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Связанная информация

- Чтобы получить более обширное представление о MSI, прочитайте раздел [Управляемое удостоверение службы (MSI) для ресурсов Azure](msi-overview.md).


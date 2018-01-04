---
title: "Настройка MSI, назначенный пользователем для ВМ Azure с помощью шаблона Azure"
description: "Пошагово, пошаговые инструкции по настройке назначенный пользователем управляемые службы удостоверений (MSI) для виртуальной Машины Azure, с помощью шаблона диспетчера ресурсов Azure."
services: active-directory
documentationcenter: 
author: bryanla
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
ms.openlocfilehash: d97f0fa2d6c1c92aaa3d5c74dd6715de00d32438
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Настройка назначенный пользователем управляемые службы удостоверений (MSI) для виртуальной Машины, с помощью шаблона Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет служб Azure с использованием управляемого удостоверения Azure Active Directory. Этот идентификатор можно использовать для проверки подлинности для служб, которые поддерживают проверку подлинности Azure AD, без использования учетных данных в коде. 

В этой статье вы узнаете, как для включения и удаления MSI, назначенный пользователем для виртуальной Машины Azure, с помощью шаблона развертывания диспетчера ресурсов Azure.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Включение MSI во время создания виртуальной машины Azure или на имеющейся виртуальной машине

Как портал Azure и сценарии, шаблоны Azure Resource Manager предоставляют возможность развертывать новые и измененные ресурсы, определенные группой ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Создание и назначение MSI, назначенный пользователем для новой или существующей виртуальной Машины выполняется таким же образом. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) для развертываний.

1. Войдите в Azure локально или через портал Azure, используйте учетную запись, которая связана с подпиской Azure, содержит ли MSI-ФАЙЛ и виртуальной Машины. Также убедитесь, что ваша учетная запись принадлежит роли, которая предоставляет разрешения на запись на подписку или ресурсов (например, роль «владелец»).

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


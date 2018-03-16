---
title: "Как настроить MSI на виртуальной машине Azure с помощью шаблона"
description: "Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью шаблона Azure Resource Manager."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 8f05c8c9dfcb5237886f641d933f3e6309943bc8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Настройка управляемого удостоверения службы виртуальной машины с помощью шаблона

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Служба управляемых удостоверений службы предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для виртуальных машин Azure с помощью шаблона развертывания Azure Resource Manager.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Включение MSI во время создания виртуальной машины Azure или на имеющейся виртуальной машине

Как портал Azure и сценарии, шаблоны Azure Resource Manager предоставляют возможность развертывать новые и измененные ресурсы, определенные группой ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Включение MSI для новой или существующей виртуальной машины выполняется схожим образом. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) для развертываний.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей виртуальную машину. Кроме того, убедитесь, что ваша учетная запись принадлежит роли, которая предоставляет разрешения на запись в виртуальной машине (например, "Участник виртуальных машин").

2. Загрузив шаблон в редактор, найдите ресурс `Microsoft.Compute/virtualMachines` в разделе `resources`. Имя вашего ресурса может немного отличаться от указанного на этом снимке экрана, в зависимости от используемого редактора и от того, изменяете ли вы шаблон для нового или имеющегося развертывания.

   >[!NOTE] 
   > В этом примере предполагается, что используются переменные `vmName`, `storageAccountName` и `nicName`, определенные в шаблоне.
   >

   ![Снимок экрана шаблона: поиск виртуальной машины](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

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

   ![Снимок экрана шаблона после изменения](../media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Удаление MSI с виртуальной машины Azure

При наличии виртуальной машины, которой больше не требуется MSI, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей виртуальную машину. Кроме того, убедитесь, что ваша учетная запись принадлежит роли, которая предоставляет разрешения на запись в виртуальной машине (например, "Участник виртуальных машин").

2. Удалите два элемента, добавленные в предыдущем разделе: свойство виртуальной машины `"identity"` и ресурс `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Связанная информация

- Чтобы получить более обширное представление о MSI, прочитайте раздел [Управляемое удостоверение службы (MSI) для ресурсов Azure](overview.md).


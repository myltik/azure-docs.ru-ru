---
title: "Настройка MSI в масштабируемом наборе виртуальных машин Azure с помощью шаблона"
description: "Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин Azure с помощью шаблона Azure Resource Manager."
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Настройка управляемого удостоверения службы виртуальной машины с помощью шаблона

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Служба управляемых удостоверений службы предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для масштабируемого набора виртуальных машин Azure с помощью шаблона развертывания Azure Resource Manager.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Включение MSI во время создания масштабируемого набора виртуальных машин Azure или в существующем масштабируемом наборе виртуальных машин Azure

Как портал Azure и сценарии, шаблоны Azure Resource Manager предоставляют возможность развертывать новые и измененные ресурсы, определенные группой ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Включение MSI для нового и существующего масштабируемого набора виртуальных машин выполняется одинаково. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) для развертываний.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Загрузив шаблон в редактор, найдите ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Имя вашего ресурса может немного отличаться от указанного на этом снимке экрана, в зависимости от используемого редактора и от того, изменяете ли вы шаблон для нового или имеющегося развертывания.
   
   ![Снимок экрана шаблона: поиск виртуальной машины](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Добавьте свойство `"identity"` на том же уровне, что и свойство `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Используйте следующий синтаксис:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Затем добавьте расширение MSI масштабируемого набора виртуальных машин как элемент `extensionsProfile`. Используйте следующий синтаксис:

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

5. По завершении шаблон должен выглядеть следующим образом.

   ![Снимок экрана шаблона после изменения](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Удаление MSI из масштабируемого набора виртуальных машин Azure

Если для масштабируемого набора виртуальных машин MSI больше не требуется, сделайте следующее:

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Удалите два элемента, добавленные в предыдущем разделе: свойства `"identity"` и `"extensionsProfile"` масштабируемого набора виртуальных машин.

## <a name="next-steps"></a>Дополнительная информация

- Чтобы получить более обширное представление о MSI, прочитайте раздел [Управляемое удостоверение службы (MSI) для ресурсов Azure](overview.md).


---
title: "Учетные записи хранения в Azure Stack | Документация Майкрософт"
description: "Узнайте, как создать учетную запись хранения Azure Stack."
services: azure-stack
documentationcenter: 
author: vhorne
manager: byronr
editor: 
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/1/2017
ms.author: victorh
ms.openlocfilehash: 41c9ee37c43d4ad41c51ea2ed023d3b47d460dd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="storage-accounts-in-azure-stack"></a>Учетные записи хранения в Azure Stack
Учетные записи хранения включают в себя службы BLOB-объектов и таблиц, а также уникальное пространство имен для объектов данных хранилища. По умолчанию данные в учетной записи доступны только владельцу учетной записи хранения.

1. На компьютере подтверждения концепции Azure Stack войдите в `https://adminportal.local.azurestack.external` в качестве [администратора](azure-stack-connect-azure-stack.md), а затем щелкните **Создать** > **Данные+хранилище** > **Учетная запись хранения**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. В колонке **Создание учетной записи хранения** введите имя учетной записи хранения. Создайте **группу ресурсов** или выберите имеющуюся, а затем щелкните **Создать** для создания учетной записи хранения.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Чтобы просмотреть новую учетную запись хранения, щелкните **Все ресурсы**, а затем найдите учетную запись хранения и щелкните ее имя.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Дополнительная информация
[Использование шаблонов Azure Resource Manager](user/azure-stack-arm-templates.md)

[Узнайте об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md)

[Скачайте руководство по проверке согласованного с Azure хранилища Azure Stack](http://aka.ms/azurestacktp1doc)

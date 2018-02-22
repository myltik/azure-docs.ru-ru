---
title: "Создание тестовой виртуальной машины в Azure Stack | Документация Майкрософт"
description: "Узнайте, как подготовить тестовую виртуальную машину в Azure Stack в качестве оператора облака."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 41096f68e5e7d9e31098d1e8919101418abe4c03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Создание тестовой виртуальной машины в Azure Stack

*Область применения: Пакет средств разработки Azure Stack*

Оператор Azure Stack может создать тестовую виртуальную машину, чтобы проверить развертывания комплекта разработки [Azure Stack](azure-stack-poc.md).

> [!NOTE]
> Прежде чем подготовить виртуальные машины, необходимо [добавить образ ознакомительной версии Windows Server 2016 в Azure Stack Marketplace](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины
1. На узле Пакета средств разработки Azure Stack [войдите](azure-stack-connect-azure-stack.md) на портал администрирования (`https://adminportal.local.azurestack.external`), а затем щелкните **Создать** > **Вычисление** > **Windows Server 2016 Datacenter Eval** > **Создать**.  
2. В колонке **Основные сведения** введите **имя**, **имя пользователя** и **пароль**. Выберите **подписку**. **Создайте группу ресурсов** или выберите имеющуюся, а затем нажмите кнопку **ОК**.  
3. В колонке **Выбор размера** выберите **A1 Standard**, а затем щелкните **Выбрать**.  
4. В колонке **Параметры** оставьте значения по умолчанию и нажмите кнопку **ОК**.
5. В колонке **Сводка** нажмите кнопку **ОК**, чтобы создать виртуальную машину.  
6. Чтобы просмотреть новую виртуальную машину, щелкните **All resources** (Все ресурсы), а затем найдите виртуальную машину и щелкните ее имя.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>Дополнительная информация
[Using the administrator portal in Azure Stack](azure-stack-manage-portals.md) (Использование портала администрирования в Azure Stack)

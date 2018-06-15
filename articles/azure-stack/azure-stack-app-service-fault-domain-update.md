---
title: Службы приложений Azure в Azure Stack. Обновление домена сбоя | Документация Майкрософт
description: Распространение Службы приложений Azure в доменах сбоя Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150980"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Распространение Службы приложений Azure в доменах сбоя Azure Stack

*Область применения: интегрированные системы Azure Stack*

Начиная с обновления 1802 Azure Stack поддерживает распределения рабочих нагрузок между доменами сбоя. Эта возможность крайне важна для обеспечения высокого уровня доступности.

> [!IMPORTANT]
> Чтобы использовать поддержку доменов сбоя, необходимо обновить интегрированные системы Azure Stack до версии 1802.  Этот документ применим только к развертываниям поставщика ресурсов службы приложений, созданным до обновления 1802.  Если вы развертывали свою службу приложений в Azure Stack, к которому уже было применено обновление 1802, поставщик ресурсов сразу был настроен с распределением по доменам сбоя.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Распределение поставщика ресурсов службы приложений по доменам сбоя

Чтобы перераспределить масштабируемые наборы, развернутые для поставщика ресурсов службы приложений, необходимо выполнить следующие действия для каждого масштабируемого набора.  По умолчанию масштабируемые наборы имеют следующие имена.

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Если в некоторых из этих масштабируемых наборов у вас нет развернутых экземпляров, балансирование для них выполнять не нужно.  Масштабируемые наборы будут правильно сбалансированы, когда вы в будущем примените к ним масштабирование.
>
>

1. Найдите масштабируемые наборы виртуальных машин на портале администратора Azure Stack.  Здесь указаны все существующие масштабируемые наборы, развернутые одновременно со службой приложений, а также число экземпляров в них.

    ![Список масштабируемых наборов виртуальных машин Microsoft Azure в пользовательском интерфейсе масштабируемых наборов виртуальных машин][1]

2. Выполните горизонтальное масштабирование для каждого набора.  Например, если в некотором масштабируемом наборе у вас есть три существующих экземпляра, увеличьте число экземпляров в нем до 6, чтобы подготовить в доменах сбоя три новых экземпляра.
    a. [Настройка среды PowerShell оператора Azure Stack](azure-stack-powershell-configure-admin.md) b. Используйте этот пример, чтобы развернуть масштабируемый набор.
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> На выполнение этого шага может уйти несколько часов, в зависимости от типа роли и количества экземпляров.
>
>

3. Состояние новых экземпляров роли можно контролировать в колонке ролей администрирования для службы приложений.  Проверка состояния для отдельного экземпляра роли экземпляр роли по щелчку в списке типов ролей

    ![Служба приложений Azure для ролей Azure Stack][2]

4. Когда все новые экземпляры перейдут в состояние **готовности**, вернитесь к колонке масштабируемого набора виртуальных машин и **удалите** старые экземпляры.

5. Повторите эти шаги для **каждого** масштабируемого набора виртуальных машин.

## <a name="next-steps"></a>Дополнительная информация

Можно также попробовать другие [службы PaaS (платформа как услуга)](azure-stack-tools-paas-services.md).

* [Use SQL databases on Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) (Использование баз данных SQL в Microsoft Azure Stack)
* [Use MySQL databases on Microsoft Azure Stack](azure-stack-mysql-resource-provider-deploy.md) (Использование баз данных MySQL в Microsoft Azure Stack)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png

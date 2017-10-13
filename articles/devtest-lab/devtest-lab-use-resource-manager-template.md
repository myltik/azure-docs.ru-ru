---
title: "Просмотр и использование шаблона Azure Resource Manager виртуальной машины | Документация Майкрософт"
description: "Сведения об использовании шаблона Azure Resource Manager виртуальной машины для создания других виртуальных машин."
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: tarcher
ms.openlocfilehash: 0807ab367b91be5acd261f2b58ca2112b2c9e380
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-virtual-machines-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager виртуальной машины

При создании виртуальной машины в DevTest Labs на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) вы можете просмотреть шаблон Azure Resource Manager, прежде чем сохранить виртуальную машину. Этот шаблон можно использовать как основу, чтобы создать больше виртуальных машин лаборатории с теми же настройками.

Здесь описывается, как просмотреть шаблон Resource Manager при создании виртуальной машины, а также как развернуть его в дальнейшем для автоматизации создания той же виртуальной машины.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Шаблоны Resource Manager для нескольких и одной виртуальной машины
Существует два способа создания виртуальных машин в DevTest Labs с помощью шаблона Resource Manager: подготовить ресурс Microsoft.DevTestLab/labs/virtualmachines или ресурс Microsoft.Commpute/virtualmachines. Каждый из этих ресурсов используется в различных сценариях и требует разных разрешений.

- Шаблоны Resource Manager, использующие тип ресурса Microsoft.DevTestLab/labs/virtualmachines (как объявлено в свойстве resource в шаблоне), могут подготовить отдельные виртуальные машины лаборатории. Каждая виртуальная машина затем отображается в виде отдельного элемента в списке виртуальных машин DevTest Labs:

   ![Список виртуальных машин как отдельных элементов в списке виртуальных машин DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Этот тип шаблона Resource Manager можно подготовить с помощью команды Azure PowerShell **New AzureRmResourceGroupDeployment** или с помощью команды Azure CLI **az group deployment create**. Для это требуется иметь разрешения администратора, поэтому пользователи, которым назначена роль пользователя DevTest Labs, не могут выполнить развертывание. 

- Шаблоны Resource Manager, использующие тип ресурса Microsoft.Compute/virtualmachines, могут подготовить несколько виртуальных машин, которые отображаются в качестве отдельной среды в списке виртуальных машин DevTest Labs:

   ![Список виртуальных машин как отдельных элементов в списке виртуальных машин DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Виртуальные машины в одной среде могут находиться под общим управлением и использовать один и тот же жизненный цикл. Пользователи, которым назначена роль пользователя DevTest Labs, могут создавать среды с помощью этих шаблонов, при условии, что администратор настроил лабораторию нужным образом.

В оставшейся части статьи рассматриваются шаблоны Resource Manager, использующие Mirosoft.DevTestLab/labs/virtualmachines. Они используются администраторами лаборатории для автоматизации создания виртуальной машины лаборатории (например, запрашиваемые виртуальные машины) или для создания образа золотого стандарта (например, фабрики образов).

В статье [Рекомендации по созданию шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) содержится множество рекомендаций и указаний, облегчающих создание надежных и простых в использовании шаблонов Azure Resource Manager.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Просмотр и сохранение шаблона Azure Resource Manager виртуальной машины
1. Выполните действия, описанные в статье [Создание первой виртуальной машины в лаборатории в Azure DevTest Labs](devtest-lab-create-first-vm.md), чтобы начать создание виртуальной машины.
1. Введите необходимые сведения для виртуальной машины и добавьте все необходимые для нее артефакты.
1. В нижней части окна настройки параметров выберите **View ARM template** (Просмотреть шаблон ARM).

   ![Кнопка View ARM template (Просмотреть шаблон ARM)](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Скопируйте и сохраните шаблон Resource Manager, чтобы позже использовать его для создания другой виртуальной машины.

   ![Сохранение шаблона Resource Manager для последующего использования](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Прежде чем использовать сохраненный шаблон Resource Manager, необходимо обновить раздел параметров. Вы можете создать файл parameter.json, который настраивает только параметры за пределами фактического шаблона Resource Manager. 

![Настройка параметров с помощью JSON-файла](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Развертывание шаблона Resource Manager для создания виртуальной машины
После сохранения и настройки шаблона Resource Manager в соответствии с вашими потребностями его можно использовать для автоматизации создания виртуальной машины. Сведения об использовании Azure PowerShell с шаблонами Resource Manager для развертывания ресурсов в Azure см. в [этой статье](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Сведения об использовании Azure CLI с шаблонами Resource Manager для развертывания ресурсов в Azure см. в [этой статье](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Только пользователь с правами владельца лаборатории может создать виртуальные машины из шаблона Resource Manager с помощью Azure PowerShell. Если необходимо автоматизировать создание виртуальной машины с помощью шаблона Resource Manager и у вас есть только разрешения пользователя, воспользуйтесь [командой CLI](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create)**az lab vm create**.

### <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения см. в статье [Создание сред со множеством виртуальных машин и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
* Ознакомьтесь с кратким руководством по шаблонам Resource Manager для автоматизации работы в DevTest Labs [в общедоступном репозитории DevTest Labs на сайте GitHub](https://github.com/Azure/azure-quickstart-templates).

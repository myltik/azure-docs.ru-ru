---
title: Удаление кластера Azure и его ресурсов | Документация Майкрософт
description: Сведения о том, как полностью удалить кластер Service Fabric путем удаления содержащей кластер группы ресурсов или выборочного удаления отдельных ресурсов.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: aljo
ms.openlocfilehash: 55840dc4b9f25c7e2676cff936390a0542eb239d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211275"
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Удаление кластера Service Fabric в Azure и используемых им ресурсов
Кластер Service Fabric состоит из многих других ресурсов Azure помимо собственно ресурса кластера. Чтобы полностью удалить кластер Service Fabric, необходимо также удалить все ресурсы, из которых он состоит.
Вы можете сделать это одним из двух способов: удалить группу ресурсов, в которой находится кластер (при этом будет удален ресурс кластера и другие ресурсы в группе ресурсов), или удалить ресурс кластера и связанные с ним ресурсы по отдельности (но не другие ресурсы в группе ресурсов).

> [!NOTE]
> Удаление ресурса кластера **не** приводит к удалению всех прочих ресурсов, из которых состоит кластер Service Fabric.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Удаление всей группы ресурсов, в которой находится кластер Service Fabric
Это самый простой способ, который гарантирует удаление всех ресурсов, связанных с кластером, включая группу ресурсов. Группу ресурсов можно удалить с помощью PowerShell или через портал Azure. Если выбранная группа ресурсов содержит ресурсы, которые не относятся к кластеру Service Fabric, можно удалить отдельные ресурсы.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Удаление группы ресурсов с помощью Azure PowerShell
Группу ресурсов также можно удалить, выполнив следующие командлеты Azure PowerShell. Убедитесь, что на компьютере установлена среда Azure PowerShell 1.0 или более поздней версии. Если вы не сделали этого ранее, выполните инструкции в статье [Как установить и настроить Azure PowerShell](/powershell/azure/overview)

Откройте PowerShell и выполните следующие командлеты PowerShell:

```powershell
Connect-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Будет выведен запрос на подтверждение удаления, если вы не использовали параметр *-Force* . После подтверждения группа ресурсов и все содержащиеся в ней ресурсы будут удалены.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Удаление группы ресурсов на портале Azure
1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к нужному кластеру Service Fabric.
3. Щелкните имя группы ресурсов на странице "Основные компоненты" кластера.
4. Откроется страница **Resource Group Essentials** (Основные компоненты группы ресурсов).
5. Нажмите кнопку **Delete**(Удалить).
6. Следуйте инструкциям на этой странице, чтобы завершить удаление группы ресурсов.

![Удаление группы ресурсов][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Удаление ресурса кластера и используемых им ресурсов, но не других ресурсов в группе ресурсов
Если в группе ресурсов содержатся только ресурсы, связанные с кластером Service Fabric, который требуется удалить, проще всего будет удалить группу ресурсов целиком. Если требуется выборочно удалить ресурсы в группе ресурсов, выполните следующие действия.

Если кластер развернут с помощью портала или с помощью одного из шаблонов Resource Manager для Service Fabric из коллекции шаблонов, то все ресурсы, используемые кластером, помечены следующими двумя тегами. С их помощью можно определить, какие ресурсы требуется удалить.

***Тег 1:*** ключ = clusterName, значение = "имя_кластера".

***Тег 2:*** ключ = resourceName, значение = ServiceFabric.

### <a name="delete-specific-resources-in-the-azure-portal"></a>Удаление отдельных ресурсов на портале Azure
1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к нужному кластеру Service Fabric.
3. В колонке "Основные компоненты" перейдите к элементу **Все параметры** .
4. В разделе **Управление ресурсами** колонки параметров щелкните **Теги**.
5. Щелкните один из **тегов** в колонке тегов, чтобы получить список всех ресурсов с этим тегом.
   
    ![Теги ресурсов][ResourceTags]
6. Получив список ресурсов с тегом, вы можете выбрать и удалить нужные ресурсы.
   
    ![Ресурсы с тегами][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Удаление ресурсов с помощью Azure PowerShell
Отдельные ресурсы также можно удалить, выполнив следующие командлеты Azure PowerShell. Убедитесь, что на компьютере установлена среда Azure PowerShell 1.0 или более поздней версии. Если вы не сделали этого ранее, выполните инструкции в статье [Как установить и настроить Azure PowerShell](/powershell/azure/overview)

Откройте PowerShell и выполните следующие командлеты PowerShell:

```powershell
Connect-AzureRmAccount
```
Для каждого ресурса, который требуется удалить, выполните следующий скрипт:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Чтобы удалить ресурс кластера, выполните следующий скрипт:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об обновлении кластера и секционировании служб см. в следующих статьях.

* [Узнайте об обновлениях кластера.](service-fabric-cluster-upgrade.md)
* [Узнайте о секционировании служб с отслеживанием для максимального масштабирования.](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG

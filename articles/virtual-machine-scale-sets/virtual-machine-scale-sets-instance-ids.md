---
title: Общие сведения об идентификаторах экземпляров для виртуальных машин масштабируемого набора Azure | Документация Майкрософт
description: Общие сведения об идентификаторах экземпляров для виртуальных машин масштабируемого набора Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: negat
ms.openlocfilehash: 3a43dc86f1fb53dfde4bce3938faaa30e18f5a6d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742895"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Общие сведения об идентификаторах экземпляров для виртуальных машин масштабируемого набора Azure
В этой статье описаны идентификаторы экземпляров для масштабируемых наборов и связанные с ними возможности.

## <a name="scale-set-instance-ids"></a>Идентификаторы экземпляров масштабируемого набора

Каждой виртуальной машине в масштабируемом наборе присваивается определяющий ее идентификатор экземпляра. Он используется в программных интерфейсах (API) масштабируемого набора для выполнения операций на определенной виртуальной машине в масштабируемом наборе. Например, при использовании API пересоздания образа можно указать определенный идентификатор экземпляра:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)).

PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm)).

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (дополнительные сведения см. в [документации по CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Список всех идентификаторов экземпляров можно получить, запросив список всех экземпляров в наборе масштабирования:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list)).

PowerShell: `Get-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)).

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (дополнительные сведения см. в [документации по CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instances)).

Для получения списка виртуальных машин масштабируемого набора также можно использовать сайт [resources.azure.com](https://resources.azure.com) или [пакеты SDK Azure](https://azure.microsoft.com/downloads/).

Точное представление выходных данных зависит от параметров, которые вы предоставляете команде. Ниже приведены несколько примеров выходных данных CLI:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Как видно выше, свойство instanceId является десятичным числом. После удаления старых экземпляров идентификаторы можно повторно использовать для новых экземпляров.

>[!NOTE]
> Нет **гарантии** относительно способа присваивания идентификаторов экземпляров виртуальным машинам в масштабируемом наборе. Может показаться, что они периодически увеличиваются, но это не всегда так. Не имеет значения, каким образом идентификаторы экземпляров присваиваются виртуальным машинам.

## <a name="scale-set-vm-names"></a>Имена виртуальных машин масштабируемого набора

В приведенном выше примере выходных данных также имеется свойство name для виртуальной машины. Это имя имеет форму {имя_масштабируемого_набора}_{ИД_экземпляра}. Оно отображается на портале Azure при отображении списка экземпляров в масштабируемом наборе:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Часть имени {ИД_экземпляра} — то же десятичное число, что и в свойстве instanceId, указанном выше.

## <a name="instance-metadata-vm-name"></a>Имя виртуальной машины метаданных экземпляра

Если вы запрашиваете [метаданные экземпляра](../virtual-machines/windows/instance-metadata-service.md) из виртуальной машины масштабируемого набора, в выходных данных отображается свойство name:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Это имя совпадает с именем, указанным выше.

## <a name="scale-set-vm-computer-name"></a>Имя компьютера виртуальной машины масштабируемого набора

Каждой виртуальной машины в масштабируемом наборе также присваивается имя компьютера. Имя этого компьютера — имя узла виртуальной машины в [предоставленном системой Azure разрешении DNS-имени в виртуальной сети](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Это имя компьютера имеет форму {префикс_имени_компьютера}{ИД_экземпляра_base36}.

{ИД_экземпляра_base36} представляется в кодировке [base36](https://en.wikipedia.org/wiki/Base36) и всегда состоит из шести цифр. Если представление в кодировке base36 состоит меньше, чем из шести цифр, часть имени {ИД_экземпляра_base36} дополняется нулями (до шести цифр). Например, экземпляр с частью имени {префикс_имени_компьютера} nsgvmss и идентификатором экземпляра 85 будет иметь имя компьютера nsgvmss00002D.

>[!NOTE]
> Префикс имени компьютера — свойство модели масштабируемого набора, задаваемое пользователем. Он может отличаться от имени масштабируемого набора.
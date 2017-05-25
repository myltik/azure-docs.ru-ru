---
title: "Создание виртуальной машины Windows с помощью шаблона в Azure | Документация Майкрософт"
description: "Простое создание виртуальной машины Windows с помощью шаблона Resource Manager и PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 67b023ccd761bc08b96c9ebda907c6451bfbe52f
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---

# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Создание виртуальной машины Windows с использованием шаблона Resource Manager

В этой статье описывается развертывание шаблона Azure Resource Manager с помощью PowerShell. Этот [шаблон](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) позволяет выполнить развертывание одной виртуальной машины под управлением Windows Server в новой виртуальной сети с одной подсетью.

Подробное описание ресурса виртуальной машины см. в статье [Виртуальные машины в шаблоне Azure Resource Manager](template-description.md). Дополнительные сведения обо всех ресурсах в шаблоне см. в статье [Пошаговое руководство по созданию шаблона Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Процедура, описанная в этой статье, занимает около 5 минут.

## <a name="step-1-install-azure-powershell"></a>Шаг 1. Установка Azure PowerShell

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](../../powershell-install-configure.md).

## <a name="step-2-create-a-resource-group"></a>Шаг 2. Создание группы ресурсов

Все ресурсы должны развертываться в [группе ресурсов](../../azure-resource-manager/resource-group-overview.md).

1. Получите список доступных расположений, где можно создавать ресурсы.
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. Создайте группу ресурсов в выбранном расположении. В этом примере показано, как создать группу ресурсов с именем **MyResourceGroup** в расположении **Central US**.

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
    ```
   
  Вы увидите нечто вроде этого примера:

    ```powershell 
    ResourceGroupName : myResourceGroup
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
    ```

## <a name="step-3-create-the-resources"></a>Шаг 3. Создание ресурсов
Разверните шаблон и укажите значения параметров при появлении запроса. В примере ниже развертывается шаблон 101-vm-simple-windows в созданной группе ресурсов.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
Вам будет предложено указать имя и пароль учетной записи администратора виртуальной машины, а также префикс DNS.

Вы увидите нечто вроде этого примера:

    DeploymentName    : azuredeploy
    ResourceGroupName : myResourceGroup
    ProvisioningState : Succeeded
    Timestamp         : 12/29/2016 8:11:37 PM
    Mode              : Incremental
    TemplateLink      :
       Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                        101-vm-simple-windows/azuredeploy.json
       ContentVersion : 1.0.0.0
    Parameters        :
      Name             Type                       Value
      ===============  =========================  ==========
      adminUsername    String                     myAdminUser
      adminPassword    SecureString
      dnsLabelPrefix   String                     myDomain
      windowsOSVersion String                     2016-Datacenter
    Outputs           :
      Name             Type                       Value
      ===============  =========================  ===========
      hostname         String                     myDomain.centralus.cloudapp.azure.com
    DeploymentDebugLogLevel :

> [!NOTE]
> Шаблоны и параметры можно также развернуть из локальных файлов. Дополнительные сведения см. в статье [Использование Azure PowerShell со службой хранилища Azure](../../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Дальнейшие действия

- При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении распространенных ошибок при развертывании Azure с помощью Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Узнайте, как использовать Azure PowerShell для создания виртуальной машины, изучив раздел [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Сведения об управлении созданной виртуальной машиной см. в статье [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



---
title: "Обработка уведомлений по обслуживанию для виртуальных машин Windows в Azure | Документация Майкрософт"
description: "Просмотрите уведомления по обслуживанию для виртуальных машин Windows, запущенных в Azure, и запустите самостоятельное обслуживание."
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 90129bee5771534b01ac12473c6cfed77840ae20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Обработка плановых уведомлений по обслуживанию для виртуальных машин Windows

Azure периодически выполняет обновления, чтобы повысить надежность, производительность и безопасность инфраструктуры узлов, в которой работают виртуальные машины. Обновления — это изменения, такие как исправление среды размещения или модернизация оборудования и вывод его из эксплуатации. Большинство этих обновлений не влияют на работу размещенных виртуальных машин. Но в некоторых случаях они все же оказывают определенное воздействие:

- Если обслуживание не требует перезагрузки, Azure с помощью миграции на месте приостанавливает работу виртуальной машины во время обновления узла.

- Если для обслуживания требуется перезагрузка, вы получите уведомление во время планирования обслуживания. В этих случаях вам предоставляется временное окно, в котором вы можете начать обслуживание самостоятельно в удобное для вас время.


Плановое обслуживание, требующее перезагрузки, запланировано в волнах. Каждая волна имеет разную область (регионы).

- Волна начинается с уведомления клиентов. По умолчанию уведомление отправляется владельцу и совладельцам подписки. Для уведомлений можно добавить дополнительных получателей и параметры обмена сообщениями, например электронная почта, SMS и объекты Webhook.  
- Вскоре после уведомления задается период самообслуживания. В рамках этого периода можно найти информацию о том, какая из виртуальных машин включена в эту волну, и начать обслуживание с использованием упреждающего повторного развертывания. 
- Когда заканчивается период самообслуживания, начинается период запланированного обслуживания. В настоящее время в Azure планируется и применяется необходимое обслуживание для вашей виртуальной машины. 

Два периода нужны, чтобы у вас было достаточно времени для запуска обслуживания и перезагрузки виртуальной машины до того, как Azure начнет обслуживание автоматически.


Для запроса окон обслуживания для виртуальных машин и запуска самообслуживания можно использовать портал Azure, PowerShell, REST API и CLI.

 > [!NOTE]
 > Если попытка запустить обслуживание завершилась сбоем, Azure отмечает виртуальную машину как **пропущенную** и не перезагружает ее во время периода запланированного обслуживания. Вместо этого позже вам сообщат новое расписание. 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Проверка состояния обслуживания с помощью PowerShell

Чтобы узнать, когда для виртуальных машин запланировано обслуживание, можно также использовать Azure PowerShell. Информацию о плановом обслуживании можно получить с помощью командлета [Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm), используя параметр `-status`.
 
Сведения об обслуживании возвращаются, только если имеется запланированное обслуживание. Если нет запланированного обслуживания, влияющего на виртуальную машину, командлет не возвращает информацию об обслуживании. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

В разделе MaintenanceRedeployStatus возвращаются следующие свойства: 
| Значение | Описание   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Указывает, можно ли сейчас запустить обслуживание на виртуальной машине ||
| PreMaintenanceWindowStartTime         | Начало периода самообслуживания, когда можно инициировать обслуживание на виртуальной машине ||
| PreMaintenanceWindowEndTime           | Завершение периода самообслуживания, когда можно инициировать обслуживание на виртуальной машине ||
| MaintenanceWindowStartTime            | Начало периода запланированного обслуживания, когда можно инициировать обслуживание на виртуальной машине ||
| MaintenanceWindowEndTime              | Завершение периода запланированного обслуживания, когда можно инициировать обслуживание на виртуальной машине ||
| LastOperationResultCode               | Результат последней попытки инициирования обслуживания на виртуальной машине ||



Вы также можете получить информацию о состоянии обслуживания для всех виртуальных машин в группе ресурсов с помощью командлета [Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm), не указывая виртуальную машину.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName --Status
```

Следующая функция PowerShell принимает идентификатор подписки и выводит список виртуальных машин, запланированных для обслуживания.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Запуск обслуживания вашей виртуальной машины с помощью PowerShell

Используя информацию из функции в предыдущем разделе, следующая команда запускает обслуживание на виртуальной машине, если для свойства **IsCustomerInitiatedMaintenanceAllowed** задано значение true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```
 

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как зарегистрироваться для событий обслуживания прямо из виртуальной машины с помощью функции [запланированных событий](scheduled-events.md).
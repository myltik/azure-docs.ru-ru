---
title: "Приступая к работе по созданию подсистемы балансировки нагрузки для Интернета в классическом режиме с помощью PowerShell | Документация Майкрософт"
description: "Сведения о создании балансировщика нагрузки для Интернета в классическом режиме с помощью PowerShell"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7344f2c3eeb7d52f8bc60e564d66b2cc51f10f75

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Приступая к работе по созданию балансировщика нагрузки (классический режим) для Интернета в PowerShell

[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье рассматривается классическая модель развертывания. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета с помощью диспетчера ресурсов Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Настройка балансировки нагрузки с помощью PowerShell

Чтобы настроить балансировщик нагрузки с помощью PowerShell:

1. Если вы ранее не использовали Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md) , чтобы выполнить вход в Azure и выбрать подписку.
2. Создав виртуальную машину, можно добавить к ней подсистему балансировки нагрузки в пределах той же облачной службы, используя для этого командлеты PowerShell.

В следующем примере набор балансировщика нагрузки именем "webfarm" добавляется в облачную службу "mytestcloud" (или myctestcloud.cloudapp.net) путем добавления конечных точек для балансировщика нагрузки в виртуальные машины "web1" и "web2". Балансировщик нагрузки принимает сетевой трафик через порт 80 и распределяет его между виртуальными машинами, определенными локальной конечной точкой (в данном случае порт 80), по протоколу TCP.

### <a name="step-1"></a>Шаг 1

Создайте конечную точку с балансировкой нагрузки для первой виртуальной машины "web1".

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Шаг 2

Создайте другую конечную точку для второй виртуальной машины "web2" с использованием того же имени набора балансировки нагрузки.

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Удаление виртуальной машины из балансировщика нагрузки

Для удаления конечной точки виртуальной машины из балансировщика нагрузки можно использовать Remove-AzureEndpoint.

```powershell
    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM
```

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете [приступить к созданию внутреннего балансировщика нагрузки](load-balancer-get-started-ilb-classic-ps.md) и настроить [режим распределения](load-balancer-distribution-mode.md) для определенного поведения балансировщика нагрузки.

Если вашему приложению необходимо поддерживать подключения для серверов за балансировщиком нагрузки, можно получить дополнительные сведения о [параметрах времени ожидания простоя TCP для балансировщика нагрузки](load-balancer-tcp-idle-timeout.md). Вы узнаете о поведении неактивного подключения при использовании балансировщика нагрузки Azure.



<!--HONumber=Nov16_HO2-->



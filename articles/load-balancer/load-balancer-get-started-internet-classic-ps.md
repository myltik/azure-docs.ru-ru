<properties 
   pageTitle="Начало работы по созданию балансировщика нагрузки для Интернета по классической модели с помощью PowerShell | Microsoft Azure"
   description="Сведения о создании балансировщика нагрузки для Интернета в классическом режиме с помощью PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2015"
   ms.author="joaoma" />

# Начало работы по созданию балансировщика нагрузки (классический режим) для Интернета в PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье рассматривается классическая модель развертывания. Если вас интересует модель развертывания диспетчера ресурсов Azure, перейдите к статье [Начало работы по созданию балансировщика нагрузки для Интернета с помощью диспетчера ресурсов](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## Настройка балансировки нагрузки с помощью PowerShell

Чтобы настроить балансировщик нагрузки с помощью PowerShell, выполните указанные ниже действия:

1. Если вы ранее не использовали Azure PowerShell, следуйте указаниям в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md) до этапа входа в Azure и выбора подписки.


2. Создав виртуальную машину, можно добавить к ней подсистему балансировки нагрузки в пределах той же облачной службы, используя для этого командлеты PowerShell.

В следующем примере набор балансировщика нагрузки именем "webfarm" добавляется в облачную службу "mytestcloud" (или myctestcloud.cloudapp.net) путем добавления конечных точек для балансировщика нагрузки в виртуальные машины "web1" и "web2". Балансировщик нагрузки принимает сетевой трафик через порт 80 и распределяет его между виртуальными машинами, определенными локальной конечной точкой (в данном случае порт 80), по протоколу TCP.


### Шаг 1
Создайте конечную точку с балансировкой нагрузки для первой виртуальной машины "web1".

	Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### Шаг 2 

Создайте другую конечную точку для второй виртуальной машины "web2" с использованием того же имени набора балансировки нагрузки.

	Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## Удаление виртуальной машины из балансировщика нагрузки

Для удаления конечной точки виртуальной машины из балансировщика нагрузки можно использовать Remove-AzureEndpoint.

	Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-internal-getstarted.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->
<properties 
   pageTitle="Начало работы по созданию балансировщика нагрузки для Интернета по классической модели развертывания с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Сведения о создании балансировщика нагрузки для Интернета по классической модели развертывания с помощью интерфейса командной строки Azure"
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
   ms.date="11/06/2015"
   ms.author="joaoma" />

# Начало работы по созданию балансировщика нагрузки (классический режим) для Интернета в интерфейсе командной строки Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье рассматривается классическая модель развертывания. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета с помощью диспетчера ресурсов Azure](load-balancer-get-started-internet-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## Пошаговая процедура создания балансировщика нагрузки для Интернета с помощью интерфейса командной строки

Это руководство описывает создание балансировщика нагрузки для Интернета на основе приведенного выше сценария.

1. Если вы еще не пользовались интерфейсом командной строки Azure, см. статью [Установка и настройка интерфейса командной строки Azure](xplat-cli.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.

2. Выполните команду **azure config mode**, чтобы переключиться в классический режим, как показано ниже.

		azure config mode asm

	Ожидаемые выходные данные:

		info:    New mode is asm


## Создание набора балансировщика нагрузки и конечной точки 

Сценарий предполагает, что были созданы виртуальные машины "web1" и "web2". В этом руководстве будет создан набор балансировщика нагрузки с использованием порта 80 в качестве общего порта и порта 80 в качестве локального порта. Порт пробы также настраивается на порт 80 и называется "lbset" набора балансировщика нагрузки.


### Шаг 1 

Создайте первую конечную точку и набор балансировщика нагрузки, используя `azure network vm endpoint create` для виртуальной машины "web1".

	azure network endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset 


## Шаг 2 

Добавьте в набор балансировщика нагрузки вторую виртуальную машину "web2".

	azure network endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## Шаг 3. 

Проверьте конфигурацию балансировщика нагрузки с помощью `azure vm show`.

	azure vm show web1

Выходные данные должны выглядеть следующим образом:

	data:    DNSName "contoso.cloudapp.net"
	data:    Location "East US"
	data:    VMName "web1"
	data:    IPAddress "10.0.0.5"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
	6-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
	data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
	/vhds/joaomatest-web1-2015-09-25.vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
	r-2012-R2-20150916-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
	data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
	data:    Network Endpoints 0 localPort 80
	data:    Network Endpoints 0 name "tcp-80-80"
	data:    Network Endpoints 0 port 80
	data:    Network Endpoints 0 loadBalancerProbe port 80
	data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
	data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 5986
	data:    Network Endpoints 1 name "PowerShell"
	data:    Network Endpoints 1 port 5986
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 3389
	data:    Network Endpoints 2 name "Remote Desktop"
	data:    Network Endpoints 2 port 58081
	info:    vm show command OK

## Создание конечной точки удаленного рабочего стола для виртуальной машины

Вы можете создать конечную точку удаленного рабочего стола для пересылки трафика с общего порта на локальный порт для конкретной виртуальной машины с помощью `azure vm endpoint create`.

	azure vm endpoint create web1 54580 -k 3389 


## Удаление виртуальной машины из балансировщика нагрузки

Вам необходимо удалить конечную точку, сопоставленную с набором балансировщика нагрузки, из виртуальной машины. После удаления конечной точки виртуальная машина больше не входит в набор балансировщика нагрузки.

 Используя приведенный выше пример, можно удалить конечную точку, созданную для виртуальной машины "web1", из "lbset" балансировщика нагрузки с помощью команды `azure vm endpoint delete`.

	azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE]Вы можете отобразить дополнительные параметры для управления конечными точками с помощью команды `azure vm endpoint --help`.


## Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-internal-getstarted.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

 

<!---HONumber=Nov15_HO3-->
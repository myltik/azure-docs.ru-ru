.<properties
   pageTitle="Создание внутреннего балансировщика нагрузки с помощью PowerShell в классической модели развертывания | Microsoft Azure"
   description="Узнайте, как создать внутренний балансировщик нагрузки в классической модели развертывания с помощью PowerShell."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# Приступая к созданию внутреннего балансировщика нагрузки (классическая модель) с помощью PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия с помощью модели Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## Создание набора внутреннего балансировщика нагрузки для виртуальных машин

Чтобы создать набор внутреннего балансировщика нагрузки и серверы, которые будут направлять на него свой трафик, выполните следующее:

1. Создайте экземпляр внутренней балансировки нагрузки. Он будет служить конечной точкой для входящего трафика, который необходимо распределять по серверам в наборе балансировки нагрузки.

1. Добавьте конечные точки, соответствующие виртуальным машинам, которые будут принимать входящий трафик.

1. Настройте серверы, которые будут отправлять трафик для балансировки нагрузки, таким образом, чтобы они передавали трафик на виртуальный IP-адрес экземпляра внутренней балансировки нагрузки.


### Шаг 1. Создание экземпляра внутренней балансировки нагрузки

Для существующей облачной службы или облачной службы, развернутой в региональной виртуальной сети, можно создать экземпляр внутренней балансировки нагрузки с помощью следующих команд Windows PowerShell:

	$svc="<Cloud Service Name>"
	$ilb="<Name of your ILB instance>"
	$subnet="<Name of the subnet within your virtual network>"
	$IP="<The IPv4 address to use on the subnet-optional>"

	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Обратите внимание, что здесь командлет [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell использует набор параметров DefaultProbe. Дополнительную информацию о дополнительных наборах параметров см. в разделе [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### Шаг 2: Добавление конечных точек в экземпляр внутренней балансировки нагрузки

Пример:

	$svc="mytestcloud"
	$vmname="DB1"
	$epname="TCP-1433-1433"
	$lbsetname="lbset"
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$ilb="ilbset"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### Шаг 3. Настройка серверов для отправки трафика на новую конечную точку внутренней балансировки нагрузки

На серверах, трафик которых будет перераспределяться, необходимо настроить использование нового виртуального IP-адреса экземпляра внутренней балансировки нагрузки. Этот адрес прослушивается экземпляром внутренней балансировки нагрузки. В большинстве случаев достаточно просто добавить или изменить запись DNS для виртуального IP-адреса экземпляра внутренней балансировки нагрузки.

Если во время создания экземпляра внутренней балансировки нагрузки вы указали IP-адрес, то виртуальный IP-адрес у вас уже есть. В противном случае его можно получить с помощью следующих команд:

	$svc="<Cloud Service Name>"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Чтобы использовать эти команды, заполните значения и удалите символы < и >. Пример:

	$svc="mytestcloud"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


Обратите внимание на IP-адрес в результатах выполнения команды Get-AzureInternalLoadBalancer и внесите необходимые изменения в настройки серверов или записи DNS, чтобы обеспечить отправку трафика на виртуальный IP-адрес.

>[AZURE.NOTE] Для различных сценариев администрирования платформа Microsoft Azure использует статические общедоступные маршрутизируемые IPv4-адреса. IP-адрес — 168.63.129.16. Не блокируйте этот IP-адрес брандмауэрами, поскольку это может привести к непредвиденному поведению. Во внутренней подсистеме балансировки нагрузки Azure этот IP-адрес используется зондами мониторинга для определения состояния виртуальных машин в наборе балансировки нагрузки. Если группа безопасности сети используется для ограничения трафика, поступающего на виртуальные машины Azure во внутреннем наборе балансировки нагрузки, или если она применяется к подсети виртуальной сети, убедитесь, что правила сетевой безопасности разрешают поступление сетевого трафика с адреса 168.63.129.16.


## Пример внутренней балансировки нагрузки

Подробное описание создания набора балансировки нагрузки для двух примеров конфигурации см. в следующих разделах.

### Многоуровневое приложение для Интернета

Вы хотите предоставить службу базы данных с балансировкой нагрузки для набора веб-серверов с выходом в Интернет. Оба набора серверов размещены в одной облачной службе Azure. Трафик веб-серверов для TCP-порта 1433 необходимо распределить среди двух виртуальных машин на уровне базы данных. На рис. 1 показана эта конфигурация.

![Внутренний набор балансировки нагрузки для уровня базы данных](./media/load-balancer-internal-getstarted/IC736321.png)


Конфигурация состоит из следующих элементов:

- Существующая облачная служба, в которой размещены виртуальные машины, называется mytestcloud.

- Два существующих сервера базы данных называются DB1 и DB2.

- Веб-серверы на уровне Интернета подключаются к серверам базы данных на уровне базы данных, используя частный IP-адрес. Другой вариант — использовать собственный DNS-сервер для виртуальной сети и вручную зарегистрировать запись A для набора внутреннего балансировщика нагрузки.

Следующие команды позволяют настроить новый экземпляр внутренней балансировки нагрузки с именем **ILBset** и добавить конечные точки для виртуальных машин, соответствующие двум серверам базы данных:

	$svc="mytestcloud"
	$ilb="ilbset"
	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$epname="TCP-1433-1433"
	$lbsetname="lbset"
	$vmname="DB1"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="TCP-1433-1433-2"
	$vmname="DB2"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## Удаление конфигурации внутренней балансировки нагрузки

Чтобы удалить виртуальную машину как конечную точку из экземпляра внутреннего балансировщика нагрузки, выполните следующие команды:

	$svc="<Cloud service name>"
	$vmname="<Name of the VM>"
	$epname="<Name of the endpoint>"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Чтобы использовать эти команды, заполните значения и удалите символы < и >.

Пример:

	$svc="mytestcloud"
	$vmname="DB1"
	$epname="TCP-1433-1433"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Чтобы удалить экземпляр внутреннего балансировщика нагрузки из облачной службы, выполните следующие команды:

	$svc="<Cloud service name>"
	Remove-AzureInternalLoadBalancer -ServiceName $svc

Чтобы использовать эти команды, заполните значения и удалите символы < и >.

Пример:

	$svc="mytestcloud"
	Remove-AzureInternalLoadBalancer -ServiceName $svc



## Дополнительная информация о командлетах для внутреннего балансировщика нагрузки


Чтобы получить дополнительную информацию о командлетах внутренней балансировки нагрузки, выполните следующие команды в командной строке Windows PowerShell:

- Get-help New-AzureInternalLoadBalancerConfig -full

- Get-help Add-AzureInternalLoadBalancer -full

- Get-help Get-AzureInternalLoadbalancer -full

- Get-help Remove-AzureInternalLoadBalancer -full

## Дальнейшие действия

[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->
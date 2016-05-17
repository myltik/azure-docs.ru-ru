<properties
	pageTitle="Настройка внешнего прослушивателя для групп доступности AlwaysOn | Microsoft Azure"
	description="В этом учебнике описывается процесс создания прослушивателя группы доступности AlwaysOn в Azure, доступном через публичный виртуальный IP-адрес связанной облачной службы."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/28/2016"
	ms.author="mikeray" />

# Настройка внешнего прослушивателя для групп доступности AlwaysOn в Azure

> [AZURE.SELECTOR]
- [Внутренний прослушиватель](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Внешний прослушиватель](virtual-machines-windows-classic-ps-sql-ext-listener.md)

В этом разделе показано, как настроить прослушиватель для группы доступности AlwaysOn, доступной через Интернет. Такую настройку можно выполнить, связав **общедоступный виртуальный IP-адрес (VIP)** облачной службы с прослушивателем.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


В группе доступности могут быть реплики, доступные только локально или только в Azure. В гибридных конфигурациях возможны оба способа доступа одновременно. Реплики в Azure могут находиться в одном или нескольких регионах (при использовании нескольких виртуальных сетей). В приведенных ниже указаниях предполагается, что вы уже [настроили группу доступности](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md), но еще не настроили прослушиватель.

## Рекомендации и ограничения для внешних прослушивателей

Обратите внимание на следующие рекомендации для прослушивателя группы доступности в Azure при развертывании с использованием общедоступного виртуального IP-адреса облачной службы.

- Прослушиватель группы доступности поддерживается в Windows Server 2008 R2, Windows Server 2012 и Windows Server 2012 R2.

- Клиентское приложение должно находиться в облачной службе, отличной от той, которая содержит виртуальные машины группы доступности. Azure не поддерживает службу Direct Server Return для клиента и сервера в одной облачной службе.

- По умолчанию действия в этой статье показывают, как настроить один прослушиватель для использования виртуального IP-адреса облачной службы. Тем не менее, для облачной службы можно зарезервировать и создать несколько виртуальных IP-адресов. Это позволяет использовать описанные в этой статье шаги, чтобы создать несколько прослушивателей, привязанных к собственному виртуальному IP-адресу. Подробнее о том, как создать несколько виртуальных IP-адресов, см. в разделе [Несколько виртуальных IP-адресов для облачной службы](../load-balancer/load-balancer-multivip.md).

- Если вы создаете прослушиватель в гибридной среде, в локальной сети должно быть настроено подключение к сети Интернет помимо VPN-подключения типа "сеть-сеть" к виртуальной сети Azure. В подсети Azure прослушиватель группы доступности будет доступен только через общедоступный IP-адрес соответствующей облачной службы.

- Создание внешнего прослушивателя в той же облачной службе, что и внутренний прослушиватель, использующий внутренний балансировщик нагрузки (ILB), не поддерживается.

## Определение доступности прослушивателя

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

В этой статье рассматривается создание прослушивателя, использующего **внешнюю балансировку нагрузки**. Если вы хотите использовать закрытый прослушиватель из своей виртуальной сети, см. другую версию этой статьи, где приведены пошаговые инструкции по настройке [прослушивателя с внутренней подсистемой балансировки нагрузки](virtual-machines-windows-classic-ps-sql-int-listener.md)

## Создание конечных точек балансировки нагрузки в ВМ со службой Direct Server Return

Внешняя балансировка нагрузки использует общедоступный виртуальный IP-адрес облачной службы, на которой размещены виртуальные машины. В таком случае вам не обязательно создавать или настраивать балансировщик нагрузки.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Скопируйте приведенный ниже сценарий PowerShell в текстовый редактор и задайте значения переменных в соответствии с вашими условиями (для некоторых параметров указаны значения по умолчанию). Обратите внимание: если ваша группа доступности охватывает несколько регионов Azure, приведенный сценарий нужно будет запустить в каждом центре данных для всех облачных служб и узлов, которые находятся в этом центре.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

		# Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
		ForEach ($node in $AGNodes)
		{
		    Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
		}

1. Присвоив значения переменным, скопируйте сценарий из текстового редактора в текущий сеанс Azure PowerShell и выполните его. Если в командной строке отображается >>, нажмите клавишу ВВОД еще раз, чтобы начать выполнение сценария.

## Проверка наличия пакета KB2854082

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Открытие портов брандмауэра в узлах групп доступности.

[AZURE.INCLUDE [брандмауэр](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Создание прослушивателя группы доступности

[AZURE.INCLUDE [брандмауэр](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Для использования внешней балансировки нагрузки вам потребуется получить общедоступный виртуальный IP-адрес облачной службы, на которой размещены ваши реплики. Перейдите на классический портал Azure. Перейдите к облачной службе, в которой находится виртуальная машина вашей группы доступности. Откройте представление **Панель мониторинга**.

3. Запишите адрес, показанный в разделе **Общедоступный виртуальный IP-адрес (VIP-адрес)**. Если ваше решение располагается в нескольких виртуальных сетях, повторите этот шаг для каждой облачной службы с виртуальной машиной, на которой размещается реплика.

4. Войдите на одну из виртуальных машин и скопируйте приведенный ниже сценарий PowerShell в текстовый редактор. Затем присвойте переменным записанные ранее значения.

		# Define variables
		$ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Присвоив значения переменным, откройте окно Windows PowerShell с повышенными правами. Затем скопируйте сценарий из текстового редактора, вставьте его в текущий сеанс Azure PowerShell и выполните сценарий. Если в командной строке отображается >>, нажмите клавишу ВВОД еще раз, чтобы начать выполнение сценария.

1. Повторите эти действия на каждой виртуальной машине. Этот сценарий настраивает ресурс IP-адреса путем установки IP-адреса облачной службы и прочих параметров, таких как порт зонда. После подключения ресурс IP-адреса сможет отвечать на запросы, отправляемые на порт зонда из созданной ранее конечной точки балансировки нагрузки.

## Подключение прослушивателя

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Дальнейшие действия

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Проверьте прослушиватель группы доступности (в пределах одной виртуальной сети)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Проверьте прослушиватель группы доступности (из сети Интернет)

Для доступа к прослушивателю, осуществляемому за пределами виртуальной сети, необходимо использовать внешнюю или общедоступную подсистему балансировки нагрузки (описание приведено в этом разделе). Внутренняя подсистема доступна только в пределах одной виртуальной сети. В строке подключения укажите имя облачной службы. Например, если ваша облачная служба называется *mycloudservice*, оператор sqlcmd будет выглядеть так:

	sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

В отличие от предыдущего примера здесь потребуется проверка подлинности SQL. Это связано с тем, что вызывающий объект не может использовать проверку подлинности Windows через Интернет. Дополнительные сведения см. в статье [Группы доступности AlwaysOn на виртуальной машине Azure: сценарии клиентских подключений](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). При использовании проверки подлинности SQL учетные данные для входа на обоих репликах должны совпадать. Дополнительные сведения об устранении неполадок с учетными данными, возникающих при использовании групп доступности, см. в статье [Сопоставление учетных записей или использование пользователей автономных баз данных SQL для подключения к другим репликам и сопоставления баз данных доступности](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Если реплики AlwaysOn размещены в разных подсетях, клиенты должны указывать **MultisubnetFailover=True** в строке подключения. В результате будут выполняться попытки параллельного подключения к репликам в разных подсетях. Обратите внимание, что этот сценарий включает развертывание межрегиональной группы доступности AlwaysOn.

## Дальнейшие действия

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0504_2016-->
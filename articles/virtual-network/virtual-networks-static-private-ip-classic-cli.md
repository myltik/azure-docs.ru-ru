<properties 
   pageTitle="Настройка статического частного IP-адреса в классическом режиме с помощью интерфейса командной строки | Microsoft Azure"
   description="Основные сведения о статических частных IP-адресах (DIP) и об управлении ими в классическом режиме с помощью интерфейса командной строки"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# Как задать статический частный IP-адрес (классический) в Azure CLI

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье рассматривается классическая модель развертывания. Кроме того, вы можете [управлять статическим частным IP-адресом в модели развертывания для диспетчера ресурсов](virtual-networks-static-private-ip-arm-cli.md).

Для выполнения приведенных ниже примеров команд Azure CLI требуется созданная простая среда. Для выполнения команд в том виде, в каком они представлены в данном документе, сначала постройте тестовую среду, описанную в разделе [Создание виртуальной сети](virtual-networks-create-vnet-classic-cli.md).

## Как указать статический частный IP-адрес при создании виртуальной машины
Чтобы создать новую виртуальную машину *DNS01* в новой облачной службе *TestService* согласно приведенному ранее сценарию, выполните следующее:

1. Если вы еще не пользовались интерфейсом командной строки Azure, см. статью [Установка и настройка интерфейса командной строки Azure](xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
1. Выполните команду **azure service create** для создания облачной службы.

		azure service create TestService --location uscentral

	Ожидаемые выходные данные:

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name TestService
		info:    service create command OK
	
2. Выполните команду **azure create vm** для создания виртуальной машины. Обратите внимание на значение статического частного IP-адреса. В списке, который откроется после выполнения команды, будут указаны используемые параметры.

		azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

	Ожидаемые выходные данные:

		info:    Executing command vm create
		warn:    --vm-size has not been specified. Defaulting to "Small".
		info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
		info:    Looking up virtual network
		info:    Looking up cloud service
		warn:    --location option will be ignored
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Retrieving storage accounts
		info:    Creating VM
		info:    OK
		info:    vm create command OK

	- **-l (или --location)**. Регион Azure для создания виртуальной машины. В данном сценарии это *centralus*.
	- **-n (или --vm-name)**. Имя создаваемой виртуальной машины.
	- **-w (или --virtual-network-name)**. Имя виртуальной сети, в которой будет создана виртуальная машина. 
	- **-S (или --static-ip)**. Статический частный IP-адрес для виртуальной машины.
	- **TestService**. Имя облачной службы, в которой будет создана виртуальная машина.
	- **bd507d3a70934695bc2128e3e5a255ba\_\_RightImage-Windows-2012R2-x64-v14.2**. Образ, используемый для создания виртуальной машины.
	- **adminuser**. Локальный администратор виртуальной машины Windows.
	- ****AdminP@ssw0rd**. Пароль локального администратора виртуальной машины Windows.

## Как получить информацию о статическом частном IP-адресе виртуальной машины
Чтобы просмотреть информацию о статическом частном IP-адресе виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду Azure CLI и обратите внимание на значение *Network StaticIP*:

	azure vm static-ip show DNS01

Ожидаемые выходные данные:

	info:    Executing command vm static-ip show
	info:    Getting virtual machines
	data:    Network StaticIP "192.168.1.101"
	info:    vm static-ip show command OK

## Как удалить статический частный IP-адрес виртуальной машины
Чтобы удалить статический частный IP-адрес, добавленный на виртуальную машину в приведенном выше сценарии, выполните следующую команду Azure CLI:
	
	azure vm static-ip remove DNS01

Ожидаемые выходные данные:

	info:    Executing command vm static-ip remove
	info:    Getting virtual machines
	info:    Reading network configuration
	info:    Updating network configuration
	info:    vm static-ip remove command OK

## Как добавить статический частный IP-адрес для существующей виртуальной машины
Чтобы добавить статический внутренний IP-адрес для виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду:

	azure vm static-ip set DNS01 192.168.1.101

Ожидаемые выходные данные:

	info:    Executing command vm static-ip set
	info:    Getting virtual machines
	info:    Looking up virtual network
	info:    Reading network configuration
	info:    Updating network configuration
	info:    vm static-ip set command OK

## Дальнейшие действия

- Узнайте о [зарезервированных общедоступных IP-адресах](../virtual-networks-reserved-public-ip).
- Узнайте об [общедоступных IP-адресах уровня экземпляра (ILPIP)](../virtual-networks-instance-level-public-ip).
- Ознакомьтесь с [REST API зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Oct15_HO1-->
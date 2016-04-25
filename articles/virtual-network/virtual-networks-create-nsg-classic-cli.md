<properties
   pageTitle="Как создавать сетевые группы безопасности в классическом режиме с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Узнайте, как создавать и развертывать сетевые группы безопасности в классическом режиме, используя интерфейс командной строки Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

# Как создавать сетевые группы безопасности \(в классическом режиме\) в интерфейсе командной строки Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье рассматривается классическая модель развертывания. Вы также можете [создавать группы безопасности сети с использованием модели развертывания на основе диспетчера ресурсов](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Для приведенных ниже примеров команд интерфейса командной строки Azure требуется уже созданная простая среда, основанная на приведенном выше сценарии. Чтобы выполнять команды в соответствии с инструкциями, представленными в этом документе, сначала создайте тестовую среду, [создав виртуальную сеть](virtual-networks-create-vnet-classic-cli.md).

## Как создавать сетевую группу безопасности для подсети переднего плана
Чтобы создать сетевую группу безопасности под названием **NSG-FrontEnd** по описанному выше сценарию, выполните указанные ниже действия.

1. Если вы еще не пользовались интерфейсом командной строки Azure, см. статью [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.

2. Запустите команду **`azure config mode`**, чтобы перейти в классический режим, как показано ниже.

		azure config mode asm

	Ожидаемые выходные данные:

		info:    New mode is asm

3. Запустите команду **`azure network nsg create`**, чтобы создать сетевую группу безопасности.

		azure network nsg create -l uswest -n NSG-FrontEnd

	Ожидаемые выходные данные:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : NSG-FrontEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Параметры

	- **-l \(или --location\)**. Регион Azure, в котором будет создана сетевая группа безопасности. В нашем случае это *westus*.
	- **-n \(или --name\)**. Имя для новой сетевой группы безопасности. В данном сценарии это *NSG-FrontEnd*.

4. Выполните команду **`azure network nsg rule create`**, чтобы создать правило, которое разрешает доступ к точке 3389 \(RDP\) из Интернета.

		azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : rdp-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 3389
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Параметры

	- **-a \(или --nsg-name\)**. Имя сетевой группы безопасности, в которой будет создано правило. В данном сценарии это *NSG-FrontEnd*.
	- **-n \(или --name\)**. Имя нового правила. В данном сценарии это *rdp-rule*.
	- **-c \(или --action\)**. Уровень доступа для правила \(Deny или Allow\).
	- **-p \(или --protocol\)**. Протокол \(Tcp, Udp или \*\) для правила.
- **-r \(или --type\)**. Направление подключения \(Inbound или Outbound\).
	- **-y \(или --priority\)**. Приоритет правила.
	- **-f \(или --source-address-prefix\)**. Префикс адреса источника в CIDR или использование тегов по умолчанию.
	- **-o \(или --source-port-range\)**. Исходный порт или диапазон портов.
	- **-e \(или --destination-address-prefix\)**. Префикс адреса назначения в CIDR или использование тегов по умолчанию.
	- **-u \(или --destination-port-range\)**. Конечный порт или диапазон портов.

5. Выполните команду **`azure network nsg rule create`**, чтобы создать правило, которое разрешает доступ к порту 80 \(HTTP\) из Интернета.

		azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Выполните команду **`azure network nsg subnet add`**, чтобы связать сетевую группу безопасности с подсетью переднего плана.

		azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

	Ожидаемые выходные данные:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-FrontEnd"
		info:    network nsg subnet add command OK

## Как создавать сетевую группу безопасности для внутренней подсети
Чтобы создать сетевую группу безопасности под названием *NSG-BackEnd* по описанному выше сценарию, выполните следующие действия.

3. Запустите команду **`azure network nsg create`**, чтобы создать сетевую группу безопасности.

		azure network nsg create -l uswest -n NSG-BackEnd

	Ожидаемые выходные данные:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : NSG-BackEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Параметры

	- **-l \(или --location\)**. Регион Azure, в котором будет создана сетевая группа безопасности. В нашем случае это *westus*.
	- **-n \(или --name\)**. Имя для новой сетевой группы безопасности. В данном сценарии это *NSG-FrontEnd*.

4. Выполните команду **`azure network nsg rule create`**, чтобы создать правило, которое разрешает доступ к порту 1433 \(SQL\) из подсети переднего плана.

		azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : sql-rule
		data:    Source address prefix           : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 1433
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK


5. Выполните команду **`azure network nsg rule create`**, чтобы создать правило, которое запрещает доступ к Интернету.

		azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : *
		data:    Source Port                     : *
		data:    Destination address prefix      : INTERNET
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Outbound
		data:    Action                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Выполните команду **`azure network nsg subnet add`**, чтобы связать сетевую группу безопасности с внутренней подсетью.

		azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

	Ожидаемые выходные данные:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-BackEndX"
		info:    Looking up the subnet "BackEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-BackEndX"
		info:    network nsg subnet add command OK

<!---HONumber=AcomDC_0413_2016-->
<properties 
	pageTitle="Использование SSH в Linux и Mac | Microsoft Azure" 
	description="Создание и использование SSH-ключей в Linux и Mac для развертывания с помощью диспетчера ресурсов и классической модели развертывания в Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/15/2015" 
	ms.author="rasquill"/>

#Использование SSH с Linux и Mac в Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

В этом разделе описывается использование служебных программ **ssh-keygen** и **openssl** в Linux и Mac для создания и использования файлов формата **ssh-rsa** и **pem** для обеспечения безопасности при обмене данными с виртуальными машинами Azure под управлением Linux. В новых развертываниях рекомендуется создавать виртуальные машины Azure под управлением Linux с помощью модели развертывания диспетчера ресурсов. В этом случае нужно указать файл открытого ключа или строку типа *ssh-rsa* (в зависимости от клиента развертывания). [Портал предварительной версии](https://portal.azure.com) в настоящий момент принимает только строки формата **ssh-rsa** как для классического развертывания, так и для развертывания через диспетчер ресурсов.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Создание файлов таких типов для использования на компьютере под управлением Windows для безопасного обмена данными с виртуальными машинами Linux в Azure описывается в статье [Использование ключей SSH для Windows](virtual-machines-windows-use-ssh-key.md).

## Необходимые файлы

Базовая конфигурация ssh для Azure включает открытый и закрытый 2048-разрядный ключ **ssh-rsa** (по умолчанию **ssh-keygen** использует файлы **~/.ssh/id\_rsa** и **~/.ssh/id-rsa.pub**), а также файл `.pem`, созданный на основе файла закрытого ключа **id\_rsa** для использования в классической модели развертывания классического портала.

Ниже приведены сценарии развертывания и типы используемых в них файлов.

1. Ключи **ssh-rsa** нужны для любого развертывания с использованием [портала предварительной версии](https://portal.azure.com) независимо от модели развертывания.
2. PEM-файл нужен для создания виртуальных машин с использованием [классического портала](https://manage.windowsazure.com). PEM-файлы также поддерживаются в классических развертываниях с использованием [интерфейса командной строки Azure](xplat-cli-install.md). 

## Создание ключей для использования с SSH

В зависимости от сценария, Azure требует использовать файлы 2048-разрядных ключей формата **ssh-rsa** или эквивалентные PEM-файлы. Если у вас уже есть такие файлы, при создании виртуальной машины Azure передайте файл открытого ключа.

Если эти файлы нужно создать:

1. Убедитесь, что вы используете актуальную реализацию **ssh-keygen** и **openssl**. Она зависит от платформы. 

	- Для Mac посетите [веб-сайт безопасности продуктов Apple](https://support.apple.com/HT201222) и при необходимости установите нужные обновления.
	- Для дистрибутивов Linux на базе Debian, таких как Ubuntu, Debian, Mint и пр.:

			sudo apt-get update ssh-keygen
			sudo apt-get update openssl

	- Для дистрибутивов Linux на базе RPM, таких как CentOS и Oracle Linux:

			sudo yum update ssh-keygen
			sudo yum update openssl

	- Для SLES и OpenSUSE:

			sudo zypper update ssh-keygen
			sudo zypper update openssl

2. Используйте **ssh-keygen** для создания файлов 2048-разрядных открытых и закрытых ключей RSA, и, если у вас нет конкретного расположения или имен для файлов, оставьте значения по умолчанию: `~/.ssh/id_rsa`. Базовая команда выглядит так:

		ssh-keygen -t rsa -b 2048 

	Обычно конкретная реализация **ssh-keygen** добавляет комментарий, часто это имя пользователя и имя узла. Вы можете указать особый комментарий с помощью параметра `-C`.

3. Создайте PEM-файл из своего файла `~/.ssh/id_rsa`, чтобы иметь возможность работать с классическим порталом. Выполните следующую команду **openssl**:

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	Если вы хотите создать PEM-файл из другого файла закрытого ключа, измените аргумент `-key`.

> [AZURE.NOTE]Если вы планируете управлять службами, развернутыми с помощью классической модели развертывания, вам может потребоваться создать **CER-**файл для передачи на портал, хотя это не связано с использованием протокола **ssh** или подключением к виртуальным машинам Linux, о которых идет речь в этой статье. Чтобы создать эти файлы в Linux или Mac, выполните команду <br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer,

чтобы преобразовать PEM-файл в файл сертификата X509 с кодировкой DER.

## Использование имеющихся ключей SSH

Ключи ssh-rsa (`.pub`) можно использовать для любой новой работы, особенно для модели развертывания с помощью диспетчера ресурсов и в портале предварительной версии. Если вам нужно использовать классический портал, то может потребоваться создать файл `.pem` на основе своих ключей.

## Создание виртуальной машины с помощью файла открытого ключа

При наличии нужных файлов существует много способов создать виртуальную машину, к которой можно безопасно подключаться с помощью открытого и закрытого ключа. Практически всегда, особенно при использовании развертываний с помощью диспетчера ресурсов, нужно указать PUB-файл в ответ на запрос пути к файлу ключа ssh или содержимого файла в виде строки.

### Пример. Создание виртуальной машины с помощью файла id\_rsa.pub

Чаще всего этот файл используется при создании виртуальной машины или передаче шаблона для создания виртуальной машины. В следующем примере кода показано создание новой безопасной виртуальной машины Linux в Azure путем передачи имени файла открытого ключа (в данном случае это файл `~/.ssh/id_rsa.pub` по умолчанию) в команду `azure vm create`. (Другие аргументы были созданы ранее.)

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa.pub \
	testrg testvm westeurope linux

В следующем примере показано использование формата **ssh-rsa** с шаблоном диспетчера ресурсов и интерфейсом командной строки Azure для создания виртуальной машины Ubuntu, для защиты которой используется имя пользователя и содержимое `~/.ssh/id_rsa.pub` в виде строки. (Здесь указан сокращенный вариант строки открытого ключа для удобства восприятия.)

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.2-LTS
	info:    group deployment create command OK


### Пример. Создание виртуальной машины с помощью PEM-файла

После этого можно использовать PEM-файл с классическим порталом или в режиме классического развертывания и с командой `azure vm create`, как показано в следующем примере.

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ru-RU-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ru-RU-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## Подключение к виртуальной машине

В качестве параметров команды **ssh** указываются имя пользователя для входа в систему, сетевой адрес компьютера и номер порта для подключения, а также множество других специальных параметров. (Чтобы получить дополнительную информацию о **ssh**, можно начать со этой [статьи о Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell).)

Типичное использование в развертывании с диспетчером ресурсов может выглядеть следующим образом (если вы указали просто дочерний домен и расположение развертывания):

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

При подключении к облачной службе в классическом развертывании используемый адрес может выглядеть следующим образом:

	ssh user@subdomain.cloudapp.net -p 22

Поскольку адрес может быть разным — вы можете использовать IP-адрес или, возможно, у вас есть имя пользовательского домена — вам нужно узнать адрес виртуальной машины Azure.

### Определение SSH-адреса виртуальной машины Azure в классическом развертывании

Узнать адрес для использования с виртуальной машиной и в классической модели развертывания можно с помощью команды `azure vm show`, указав в ней имя виртуальной машины:

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ru-RU-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ru-RU-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### Определение SSH-адреса виртуальной машины Azure в развертывании с помощью диспетчера ресурсов

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

Проверьте раздел профиля сети.

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

Если при создании виртуальной машины вы использовали не SSH-порт по умолчанию (номер 22), узнать номера портов, у которых имеются правила для входящего подключения, можно с помощью команды `azure network nsg show`, например:

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### Пример. Выходные данные сеанса SSH при использовании PEM-ключей и классического развертывания

Если вы создали виртуальную машину с помощью PEM-файла, созданного из файла `~/.ssh/id_rsa`, можно подключиться по протоколу ssh непосредственно к этой виртуальной машине. Имейте в виду, что в этом случае для подтверждения сертификата будет использоваться закрытый ключ в расположении `~/.ssh/id_rsa`. (Процесс создания виртуальной машины осуществляет вычисление открытого ключа из PEM-файла и помещает открытый ключ в виде ssh-rsa в `~/.ssh/authorized_users`.) Подключение может выглядеть следующим образом:

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

	* Documentation:  https://help.ubuntu.com/

	System information as of Sat Oct 10 20:53:08 UTC 2015

	System load: 0.52              Memory usage: 5%   Processes:       80
	Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

	Graph this data and manage this system at:
		https://landscape.canonical.com/

	Get cloud support with Ubuntu Advantage Cloud Guest:
		http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

## Проблемы при подключении

Можно ознакомиться с советами в статье [Устранение неполадок SSH-подключений](virtual-machines-troubleshoot-ssh-connections.md), которые могут помочь устранить проблему.

## Дальнейшие действия
 
Теперь, когда вы подключились к виртуальной машине, обновите выбранный дистрибутив, перед тем как использовать его дальше.

<!---HONumber=AcomDC_0107_2016-->
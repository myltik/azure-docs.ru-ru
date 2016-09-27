<properties 
   pageTitle="Настройка подключения шлюза типа ";точка— сеть"; к виртуальной сети с помощью модели развертывания Resource Manager | Microsoft Azure"
   description="Безопасно подключайтесь к своей виртуальной сети Azure, создав VPN-подключение шлюза ";точка — сеть";."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc" />

# Настройка подключения типа "точка — сеть" к виртуальной сети с помощью PowerShell

> [AZURE.SELECTOR]
- [Resource Manager — PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Классическая модель — классический портал](vpn-gateway-point-to-site-create.md)

Конфигурация типа "точка — сеть" позволяет создать безопасное подключение к виртуальной сети с отдельного клиентского компьютера. Это эффективное решение для подключения к виртуальной сети из удаленного расположения, например, если вы находитесь дома или на конференции либо если подключение к виртуальной сети требуется всего нескольким клиентам.

В этой статье рассматривается создание виртуальной сети с подключением "точка — сеть" в **модели развертывания Resource Manager**. Для этого нам понадобится PowerShell. Сейчас создать это решение полностью на портале Azure нельзя.

Для подключения типа "точка–сеть" не требуется VPN-устройство или общедоступный IP-адрес. VPN-подключение сначала устанавливается с локального клиентского компьютера. Дополнительные сведения о подключениях типа "точка — сеть" см. в статьях [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md#point-to-site-connections) и [Планирование и проектирование VPN-шлюза](vpn-gateway-plan-design.md).



**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Модели развертывания и средства для подключений типа "точка — сеть"**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Схема подключения типа "точка — сеть"](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "точка — сеть")


## Об этой конфигурации.

В этом сценарии вы создадите виртуальную сеть с подключением типа "точка — сеть". С помощью приведенных здесь инструкций вы также сможете создать сертификаты, необходимые для этой конфигурации. Подключение "точка — сеть" состоит из следующих элементов: виртуальная сеть с VPN-шлюзом, CER-файл корневого сертификата (открытый ключ), сертификат клиента и конфигурация VPN на стороне клиента.

Для этой конфигурации мы используем следующие значения. Переменные присваиваются в части [1](#declare) этой статьи. Вы можете использовать эти пошаговые инструкции, используя указанные в них значения, или же изменить значения в соответствии со своей средой.

- Имя — **VNet1**, используемые адресные пространства — **192.168.0.0/16** и **10.254.0.0/16**. Обратите внимание, что для виртуальной сети можно использовать более одного адресного пространства.
- Имя подсети — **FrontEnd**, используется **192.168.1.0/24**.
- Имя подсети — **BackEnd**, используется **10.254.1.0/24**.
- Имя подсети — **GatewaySubnet**, используется **192.168.200.0/24**. Имя подсети *GatewaySubnet* обязательно для работы шлюза.
- Пул адресов VPN-клиента — **172.16.201.0/24**. VPN-клиенты, подключающиеся к виртуальной сети с помощью этого подключения "точка — сеть", получают IP-адреса из этого пула.
- Подписка — убедитесь, что выбрана правильная подписка, если у вас их несколько.
- Группа ресурсов — **TestRG**.
- Расположение: **восточная часть США**
- DNS-сервер — **IP-адрес** DNS-сервера, который нужно использовать для разрешения имен.
- Имя шлюза — **GW**.
- Имя общедоступного IP-адреса — **GWIP**.
- Тип VPN: **RouteBased**.


## Подготовка

- Убедитесь в том, что у вас уже есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
	
- Установите командлеты PowerShell Azure Resource Manager (1.0.2 или более поздней версии). Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Если вы работаете с этой конфигурацией в PowerShell, убедитесь, что вы запустили ее от имени администратора.

## <a name="declare"></a>Часть 1. Вход и настройка переменных

В этом разделе мы выполним вход и объявим значения для этой конфигурации. Объявленные значения используются в примерах скриптов. Измените значения в соответствии со своей средой. Также можно использовать объявленные значения и выполнить эти шаги в качестве упражнения.

1. В консоли PowerShell войдите в свою учетную запись Azure. Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он скачивает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

		Login-AzureRmAccount 

2. Получите список подписок Azure.

		Get-AzureRmSubscription

3. Укажите подписку, которую нужно использовать.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Объявите переменные, которые вы хотите использовать. Используйте следующий пример, подставив собственные значения в соответствующих параметрах.

		$VNetName  = "VNet1"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"


## Часть 2. Настройка виртуальной сети 


1. Создайте группу ресурсов.

		New-AzureRmResourceGroup -Name $RG -Location $Location

2. Создайте конфигурации подсети для виртуальной сети, присвоив им имена *FrontEnd*, *BackEnd* и *GatewaySubnet*. Эти префиксы должны быть частью объявленного адресного пространства виртуальной сети.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Создание виртуальной сети. Необходимо указать DNS-сервер, который может разрешать имена для ресурсов, к которым вы подключаетесь. В этом примере мы использовали общедоступный IP-адрес. Подставьте собственные значения.
	
		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Укажите переменные для созданной виртуальной сети.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Запросите динамически назначенный общедоступный IP-адрес. Этот IP-адрес необходим для правильной работы шлюза. Позже вы подключите шлюз к конфигурации IP-адреса для шлюза.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## Часть 3. Добавление доверенных сертификатов

В Azure проверка подлинности клиентов, которые используют подключение "точка — сеть", выполняется с помощью сертификатов. Импортируйте в Azure CER-файл (открытый ключ) для корневого сертификата. При добавлении CER-файла Base64-encoded X.509 в Azure вы указываете Azure доверять корневому сертификату, который представляет собой файл.

Если вы пользуетесь корпоративным решением центра сертификации, можно использовать существующую цепочку сертификатов. В противном случае можно создать самозаверяющий корневой сертификат. Один из способов создания самозаверяющего сертификата — средство makecert. Дополнительные сведения о создании самозаверяющего корневого сертификата с помощью *makecert* см. в статье [Работа с самозаверяющими корневыми сертификатами для конфигураций типа "точка-сеть"](vpn-gateway-certificates-point-to-site.md).

Независимо от способа получения сертификата следует отправить CER-файл сертификата в Azure, а также создать сертификаты клиента для установки на подключаемых клиентах. Не устанавливайте самозаверяющий сертификат непосредственно на клиенте. Создание сертификатов клиента описывается ниже в разделе, посвященном [конфигурации клиента](#cc).
		
В Azure можно добавить до 20 доверенных сертификатов. Чтобы получить открытый ключ, экспортируйте сертификат как CER-файл X.509 в кодировке Base-64. Один из способов экспорта CER-файла: откройте **certmger.msc** и найдите сертификат в разделе Personal/Certificates. Щелкните правой кнопкой мыши и экспортируйте сертификат как CER-файл X.509 в кодировке Base-64 без закрытого ключа. Запишите путь к расположению, в которое экспортирован файл CER-файл. Ниже приведен пример получения строкового представления сертификата Base64.

Добавьте доверенный сертификат в Azure. На этом шаге необходимо использовать собственный путь к CER-файлу. Обратите особое внимание на переменную $P2SRootCertName = "ARMP2SRootCert.cer", которую вы определили в части 1 этой статьи. Если имя вашего сертификата отличается, измените переменную соответствующим образом.
    
		$filePathForCert = "pasteYourCerFilePathHere"
		$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
		$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## Часть 4. Создание VPN-шлюза

Настройте и создайте шлюз для своей виртуальной сети. Параметр *-GatewayType* должен иметь значение **Vpn**, а параметр *-VpnType*— **RouteBased**. Это может занять до 45 минут.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
		-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
		-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
		-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Часть 5. Скачивание пакета конфигурации VPN-клиента

На клиентах с подключением к Azure типа "точка — сеть" должны быть установлены сертификат клиента и пакет конфигурации VPN-клиента. Для клиентов Windows доступны пакеты конфигурации VPN-клиента. Пакет VPN-клиента содержит сведения о настройке программного обеспечения VPN-клиента. Такое ПО встроено в ОС Windows и зависит от сети VPN, к которой необходимо подключиться. Пакет не устанавливает никакого дополнительного программного обеспечения. Дополнительные сведения см. в разделе [Подключения "точка — сеть"](vpn-gateway-vpn-faq.md#point-to-site-connections) статьи "VPN-шлюз: вопросы и ответы".

1. После создания шлюза используйте приведенный ниже пример, чтобы скачать пакет конфигурации клиента.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. Командлет PowerShell вернет URL-адрес. Ниже приведен пример возвращаемого URL-адреса.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Скопируйте и вставьте этот URL-адрес в веб-браузер, чтобы скачать пакет. Затем установите пакет на клиентском компьютере.

4. На клиентском компьютере перейдите в раздел **Параметры сети** и щелкните **VPN**. Вы увидите подключение в списке. Оно будет содержать имя виртуальной сети, подключение к которой будет установлено, и выглядеть примерно так:

	![VPN-клиент](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN-клиент")

## <a name="cc"></a>Часть 6. Создание сертификата клиента

Теперь мы создадим сертификаты клиента. Можно создать уникальный сертификат для каждого клиента, который будет подключаться, или использовать один сертификат для нескольких клиентов. Преимущество уникальных клиентских сертификатов заключается в том, что при необходимости можно отозвать один сертификат. В противном случае, если все используют один и тот же сертификат клиента и вам необходимо отозвать сертификат для одного клиента, потребуется создать и установить новые сертификаты для всех клиентов, которые используют сертификат для проверки подлинности.

- При использовании корпоративного решения для создания сертификатов создайте сертификат клиента с именем в формате "имя@домен.com", а не в формате NetBIOS "ДОМЕН\\имя пользователя".

- Дополнительные сведения об использовании самозаверяющего сертификата для создания сертификата клиента см. в статье [Работа с самозаверяющими корневыми сертификатами для конфигураций типа "точка — сеть"](vpn-gateway-certificates-point-to-site.md).

## Часть 7. Установка сертификата клиента

Установите сертификат клиента на каждый компьютер, который будет подключаться к виртуальной сети. Этот сертификат требуется для проверки подлинности. Установку сертификата клиента можно выполнить автоматически или вручную. Описанные ниже шаги помогут вам экспортировать и установить сертификат клиента вручную.

1. Для экспорта сертификата клиента можно использовать команду *certmgr.msc*. Щелкните правой кнопкой мыши сертификат, который надо экспортировать, выберите элемент **Все задачи** и нажмите кнопку **Экспорт**.
2. Экспортируйте сертификат клиента с закрытым ключом. Это *PFX*-файл. Обязательно запишите или запомните пароль (ключ), который задали для этого сертификата.
3. Скопируйте *PFX*-файл на клиентский компьютер. На клиентском компьютере дважды щелкните *PFX*-файл, чтобы установить его. В окне запроса введите пароль. Не меняйте место установки.


## Часть 8. Подключение к Azure

1. Чтобы подключиться к виртуальной сети, откройте VPN-подключения на клиентском компьютере и найдите созданное VPN-подключение. Его имя совпадает с названием вашей виртуальной сети. Щелкните **Подключить**. Может появиться всплывающее сообщение об использовании сертификата. В таком случае щелкните **Продолжить**, чтобы использовать более высокий уровень привилегий.

2. На странице состояния **Подключение** щелкните **Подключить**. Если появится окно **Выбор сертификата**, убедитесь в том, что отображается сертификат клиента, с помощью которого вы хотите подключиться к сети. Если окно не появится, выберите нужный сертификат в раскрывающемся списке и нажмите кнопку **ОК**.

	![VPN-клиент 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Подключение VPN-клиента")

3. Теперь следует установить подключение.

	![VPN-клиент 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Подключение 2 VPN-клиента")

## Часть 9. Проверка подключения

1. Чтобы проверить, активно ли VPN-подключение, откройте окно командной строки от имени администратора и выполните команду *ipconfig/all*.

2. Просмотрите результаты. Обратите внимание, что полученный вами IP-адрес — это один из адресов в пуле адресов VPN-клиента подключения "точка–cеть", указанном в конфигурации. Результаты должны выглядеть примерно так:
    
		PPP adapter VNet1:
			Connection-specific DNS Suffix .:
			Description.....................: VNet1
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Добавление и удаление доверенного корневого сертификата

Сертификаты используются для проверки подлинности VPN-клиентов в VPN-подключениях типа "точка — сеть". Ниже приведены пошаговые инструкции по добавлению и удалению корневых сертификатов. При добавлении CER-файла Base64-encoded X.509 в Azure вы указываете Azure доверять корневому сертификату, который представляет собой файл.

Доверенные корневые сертификаты можно добавлять и удалять с помощью PowerShell или портала Azure. Если вы хотите сделать это с помощью портала Azure, выберите **Шлюз виртуальной сети > Параметры > Конфигурация "точка — сеть" > Корневые сертификаты**. Ниже описано выполнение этих задач с помощью PowerShell.

### Добавление доверенного корневого сертификата

В Azure можно добавить до 20 CER-файлов доверенных корневых сертификатов. Чтобы добавить корневой сертификат, сделайте следующее:

1. Создайте корневой сертификат и подготовьте его к добавлению в Azure. Экспортируйте открытый ключ как CER-файл X.509 в кодировке Base-64 и откройте его в текстовом редакторе. Затем скопируйте только раздел, указанный ниже.
 
	Скопируйте значения, как показано в следующем примере.

	![на основе сертификата.](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "на основе сертификата.")
	
2. Укажите имя сертификата и сведения о ключе как значения переменных. Подставьте собственные значения, как показано в следующем примере.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Добавьте новый корневой сертификат. Можно добавлять только один сертификат за раз.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Чтобы проверить, добавлен ли новый сертификат должным образом, можно выполнить следующий командлет.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

### Удаление доверенного корневого сертификата

Можно удалить доверенный корневой сертификат из Azure. При удалении доверенного сертификата сертификаты клиента, созданные на его основе, не смогут подключаться к Azure с использованием подключения типа "точка — сеть". Чтобы клиенты снова смогли подключаться, необходимо установить новый сертификат клиента, созданный на основе доверенного сертификата в Azure.

1. Чтобы удалить доверенный корневой сертификат, измените пример кода ниже.

	Объявите переменные.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Удалите сертификат.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Выполните следующий командлет, чтобы убедиться, что сертификат успешно удален.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

## Управление списком отозванных сертификатов клиента

Можно отозвать сертификаты клиента. Список отзыва сертификатов позволяет выборочно запрещать подключение типа "точка-сеть" на основе отдельных сертификатов клиента. При удалении корневого сертификата (CER-файл) из Azure будет запрещен доступ для всех сертификатов клиента, созданных на основе отозванного корневого сертификата или подписанных им. Вы можете отозвать определенный сертификат клиента, если он не является корневым. В таком случае другие сертификаты, созданные на основе корневого сертификата, по-прежнему будут действовать. Обычно корневой сертификат используется для управления доступом на уровнях группы или организации, а отозванный сертификат клиента — для точного контроля доступа для отдельных пользователей.

### Отзыв сертификата клиента

1. Получите отпечаток сертификата клиента, который нужно отозвать.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Добавьте отпечаток в список отозванных отпечатков.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Убедитесь, что отпечаток добавлен в список отзыва сертификатов. Необходимо добавлять по одному отпечатку за раз.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Возобновление использования сертификата клиента

Можно возобновить использование сертификата клиента, удалив отпечаток из списка отозванных сертификатов клиента.

1.  Удалите отпечаток из списка отозванных отпечатков сертификатов клиента.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Проверьте, удален ли отпечаток из списка отозванных отпечатков.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Дальнейшие действия

Вы можете добавить виртуальную машину в виртуальную сеть. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!---HONumber=AcomDC_0921_2016-->
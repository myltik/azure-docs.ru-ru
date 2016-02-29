<properties 
   pageTitle="Настройка подключения типа ";точка–сеть"; через VPN-шлюз к виртуальной сети Azure | Microsoft Azure"
   description="Безопасно подключайтесь к своей виртуальной сети Azure, создав VPN-подключение ";точка — сеть";. Эту конфигурацию удобно использовать, если требуется подключение между организациями из удаленного расположения без использования VPN-устройства. Кроме того, ее можно использовать с гибридными конфигурациями сети. Эта статья содержит инструкции по PowerShell для виртуальных сетей, созданных с помощью модели развертывания диспетчера ресурсов."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="cherylmc" />

# Настройка подключения типа "точка–сеть" к виртуальной сети с помощью PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Classic](vpn-gateway-point-to-site-create.md)

Конфигурация типа "точка–сеть" позволяет создать отдельное безопасное подключение к виртуальной сети с клиентского компьютера. VPN-подключение сначала устанавливается с локального клиентского компьютера. "Точка–сеть" — это идеальное решение для подключения к виртуальной сети из удаленного расположения, например, если вы находитесь дома или на конференции либо если подключение к виртуальной сети требуется всего нескольким клиентам. Для подключения типа "точка–сеть" не требуется VPN-устройство или общедоступный IP-адрес. Дополнительные сведения о подключениях типа "точка — сеть" см. в статьях [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md#point-to-site-connections) и [О безопасных распределенных подключениях для виртуальных сетей](vpn-gateway-cross-premises-options.md).

Сведения в этой статье применимы к виртуальным сетям и VPN-шлюзам, созданным с помощью модели развертывания **диспетчера ресурсов Azure**. Если нужно настроить подключение типа "точка-сеть" для виртуальной сети, созданной с помощью Service Management (другое название — классическая модель развертывания), см. сведения в статье [Настройка VPN-подключения типа "точка-сеть" к виртуальной сети](vpn-gateway-point-to-site-create.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Об этой конфигурации.

В этом сценарии вы создадите виртуальную сеть с подключением типа "точка–сеть". Подключение "точка–сеть" состоит из следующих элементов: виртуальная сеть со шлюзом, отправленный CER-файл корневого сертификата, сертификат клиента и конфигурация VPN на стороне клиента.

Для этой конфигурации мы используем следующие значения.

- Имя — **TestVNet** с использованием адресных пространств **192.168.0.0/16** и **10.254.0.0/16**. Обратите внимание, что для виртуальной сети можно использовать более одного адресного пространства.
- Имя подсети — **FrontEnd** с использованием **192.168.1.0/24**.
- Имя подсети — **BackEnd** с использованием **10.254.1.0/24**.
- Имя подсети — **GatewaySubnet** с использованием **192.168.200.0/24**. Имя подсети *GatewaySubnet* обязательно для работы шлюза. 
- Пул адресов VPN-клиента — **172.16.201.0/24**. VPN-клиенты, подключающиеся к виртуальной сети с помощью этого подключения "точка–сеть", будут получать IP-адреса из этого пула.
- Подписка — убедитесь, что выбрана правильная подписка, если у вас их несколько.
- Группа ресурсов — **TestRG**.
- Расположение — **East US**.
- DNS-сервер — **IP-адрес** DNS-сервера, который нужно использовать для разрешения имен.
- Имя шлюза — **GW**.
- Имя общедоступного IP-адреса — **GWIP**.
- Тип VPN — **RouteBased**.


## Подготовка

- Убедитесь в том, что у вас уже есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться в бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
	
- Вам потребуется установить командлеты PowerShell диспетчера ресурсов Azure (1.0.2 или более поздней версии). Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Настройка подключения "точка–сеть" для Azure

1. В консоли PowerShell войдите в свою учетную запись Azure. Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он скачивает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

		Login-AzureRmAccount 

2. Получите список подписок Azure.

		Get-AzureRmSubscription

3. Укажите подписку, которую нужно использовать.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. В этой конфигурации объявлены следующие переменные PowerShell со значениями, которые вы хотите использовать. Объявленные значения будут использоваться в примерах скриптов. Объявите значения, которые вы хотите использовать. Используйте пример, описанный ниже, при необходимости подставив свои собственные значения.

		$VNetName  = "TestVNet"
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

5. Создайте новую группу ресурсов.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. Создайте конфигурации подсети для виртуальной сети, присвоив им имена *FrontEnd*, *BackEnd* и *GatewaySubnet*. Обратите внимание, что эти префиксы должны быть частью адресного пространства виртуальной сети, объявленного выше.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Создание виртуальной сети. Обратите внимание, что необходимо указать DNS-сервер, который может разрешать имена для ресурсов, к которым вы подключаетесь. В этом примере мы использовали общий IP-адрес, но вам потребуется указать здесь свои значения.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Укажите переменные для виртуальной сети, которую вы только что создали.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Запросите динамически назначенный общедоступный IP-адрес. Этот IP-адрес необходим для правильной работы шлюза. Позже вы подключите шлюз к конфигурации IP-адреса для шлюза.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Отправьте CER-файл корневого сертификата на портал Azure. Вы можете использовать корневой сертификат из корпоративной среды сертификатов или самозаверяющий корневой сертификат. Вы можете отправить до 20 корневых сертификатов. Дополнительные сведения о создании самозаверяющего корневого сертификата с помощью *makecert* см. в статье [Работа с самозаверяющими корневыми сертификатами для конфигураций типа "точка-сеть"](vpn-gateway-certificates-point-to-site.md). Обратите внимание на то, что CER-файл не должен содержать закрытый ключ корневого сертификата.
	
	Ниже приведен пример того, как это выглядит. Сложность передачи данных общего сертификата состоит в том, что необходимо скопировать и вставить всю строку без пробелов. В противном случае передача не состоится. На этом шаге необходимо использовать собственный CER-файл сертификата. Не пытайтесь скопировать и вставить приведенный ниже пример.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Создайте шлюз для своей виртуальной сети. Параметр GatewayType должен иметь значение Vpn, а параметр VpnType — RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Настройка клиента

Для каждого клиента, который подключается к Azure с помощью подключения "точка–сеть", есть два обязательных требования: VPN-клиент должен быть настроен для подключения, а сертификат клиента должен быть установлен. Для клиентов Windows доступны пакеты конфигурации VPN-клиента. Дополнительные сведения см. в статье [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Скачайте пакет конфигурации VPN-клиента. На этом шаге используйте приведенный ниже пример, чтобы скачать пакет конфигурации клиента.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	Командлет PowerShell вернет URL-адрес. Скопируйте и вставьте этот URL-адрес в веб-браузер, чтобы скачать пакет на свой компьютер. Ниже приведен пример возвращаемого URL-адреса.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Создайте и установите сертификаты клиента (PFX), созданные из корневого сертификата, на клиентских компьютерах. Можно использовать любой метод установки, который вам подходит. Если вы используете самозаверяющий корневой сертификат и не знаете, как это делать, см. сведения в статье [Работа с самозаверяющими корневыми сертификатами для конфигураций типа "точка-сеть"](vpn-gateway-certificates-point-to-site.md).

3. Чтобы подключиться к виртуальной сети, откройте VPN-подключения на клиентском компьютере и найдите только что созданное VPN-подключение. Его имя совпадает с названием вашей виртуальной сети. Щелкните **Подключить**. Может появиться всплывающее сообщение об использовании сертификата. В таком случае нажмите кнопку **Продолжить**, чтобы использовать более высокий уровень привилегий.

4. На странице состояния **Подключение** щелкните **Подключить**. Если появится окно **Выбор сертификата**, убедитесь в том, что отображается сертификат клиента, с помощью которого вы хотите подключиться к сети. Если окно не появится, выберите нужный сертификат в раскрывающемся списке и нажмите кнопку **ОК**.

5. Теперь следует установить подключение. Подключение можно проверить с помощью следующей процедуры.

## Проверка подключения

1. Чтобы проверить, активно ли VPN-подключение, откройте окно командной строки от имени администратора и выполните команду *ipconfig/all*.

2. Просмотрите результаты. Обратите внимание, что полученный вами IP-адрес — это один из адресов в пуле адресов VPN-клиента подключения "точка–cеть", указанном в конфигурации. Результаты должны выглядеть примерно так:
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Добавление и удаление корневого сертификата

Сертификаты используются для проверки подлинности VPN-клиентов в VPN-подключениях типа "точка — сеть". Ниже приведены пошаговые указания, которые позволят добавить и удалить корневые сертификаты.

### Добавление корневого сертификата

Можно добавить на портал Azure до 20 корневых сертификатов. Чтобы добавить корневой сертификат, сделайте следующее:

1. Создайте корневой сертификат и подготовьте его к отправке.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Отправьте новый корневой сертификат. Обратите внимание, что можно добавлять только один корневой сертификат за раз.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. Чтобы проверить, добавлен ли новый сертификат должным образом, можно выполнить следующий командлет.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

### Удаление корневого сертификата

Можно удалить корневой сертификат из Azure. При удалении корневого сертификата сертификаты клиента, созданные на основе корневого сертификата, не смогут подключаться к Azure с использованием подключения типа "точка-сеть", пока не будет установлен сертификат клиента, созданный на основе корневого сертификата, допустимого в Azure.

1. Удалите корневой сертификат.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. Выполните следующий командлет, чтобы убедиться, что сертификат успешно удален.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## Управление списком отозванных сертификатов клиента

Можно отозвать сертификаты клиента. Список отзыва сертификатов позволяет выборочно запрещать подключение типа "точка-сеть" на основе отдельных сертификатов клиента. При удалении корневого сертификата из Azure будет запрещен доступ для всех сертификатов клиента, созданных на основе отозванного корневого сертификата или подписанных им. Отзыв сертификата клиента позволит запретить доступ для определенного сертификата. Обычно корневой сертификат используется для управления доступом на уровнях группы или организации, а отозванный сертификат клиента — для точного контроля доступа для отдельных пользователей.

### Отзыв сертификата клиента

1. Получите отпечаток сертификата клиента, который нужно отозвать.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Добавьте отпечаток в список отозванных отпечатков.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Убедитесь, что отпечаток добавлен в список отзыва сертификатов. Добавляйте по одному отпечатку за раз.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Возобновление использования сертификата клиента

Можно возобновить использование сертификата клиента, удалив отпечаток из списка отозванных сертификатов клиента.

1.  Удалите отпечаток из списка отозванных отпечатков сертификатов клиента.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Проверьте, удален ли отпечаток из списка отозванных отпечатков.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Дальнейшие действия

Вы можете добавить виртуальную машину в виртуальную сеть. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0218_2016-->
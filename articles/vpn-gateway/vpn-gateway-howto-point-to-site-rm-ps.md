---
title: "Подключение компьютера к виртуальной сети Azure с помощью соединения типа &quot;точка — сеть&quot; и PowerShell | Документация Майкрософт"
description: "Безопасно подключите компьютер к виртуальной сети Azure, создав подключение типа &quot;точка — сеть&quot; через VPN-шлюз."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 70b6c9b8cb14e4e0b1162162071c03174bb150c1
ms.openlocfilehash: 48f93102d70da0e1e593afe1cea1a0af3a78f8e0


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Настройка подключения типа "точка — сеть" к виртуальной сети с помощью PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager — PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Классическая модель — портал Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Конфигурация типа "точка — сеть" позволяет создать безопасное подключение к виртуальной сети с отдельного клиентского компьютера. Это эффективное решение для подключения к виртуальной сети из удаленного расположения, например, если вы находитесь дома или на конференции либо если подключение к виртуальной сети требуется всего нескольким клиентам. 

Для подключения типа "точка–сеть" не требуется VPN-устройство или общедоступный IP-адрес. VPN-подключение сначала устанавливается с локального клиентского компьютера. Дополнительные сведения о подключениях типа "точка — сеть" см. в статьях [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md#point-to-site-connections) и [Планирование и проектирование VPN-шлюза](vpn-gateway-plan-design.md). 

В этой статье рассматривается создание виртуальной сети с подключением типа "точка — сеть" в модели развертывания Resource Manager с помощью PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Модели и методы развертывания для подключений типа "точка — сеть"
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

В следующей таблице представлены две модели развертывания и доступные методы развертывания для конфигурации "точка — сеть". Когда появится статья с руководством по конфигурации, мы разместим прямую ссылку на нее в этой таблице.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Базовый рабочий процесс
![Схема соединения компьютера с виртуальной сетью Azure через подключение типа "точка — сеть"](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

В этом сценарии вы создадите виртуальную сеть с подключением типа "точка–сеть". С помощью приведенных здесь инструкций вы также сможете создать сертификаты, необходимые для этой конфигурации. Подключение "точка — сеть" состоит из следующих элементов: виртуальная сеть с VPN-шлюзом, CER-файл корневого сертификата (открытый ключ), сертификат клиента и конфигурация VPN на стороне клиента. 

Для этой конфигурации мы используем следующие значения. Переменные присваиваются в части [1](#declare) этой статьи. Вы можете использовать эти пошаговые инструкции, используя указанные в них значения, или же изменить значения в соответствии со своей средой. 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Примеры значений
* **Имя: VNet1**.
* **Адресное пространство: 192.168.0.0/16** и **10.254.0.0/16**.<br>Чтобы продемонстрировать, что эта конфигурация будет работать с несколькими адресными пространствами, в этом примере мы используем несколько адресных пространств. Однако это необязательно для этой конфигурации.
* **Имя подсети: FrontEnd**.
  * **Диапазон адресов подсети: 192.168.1.0/24**.
* **Имя подсети: BackEnd.**
  * **Диапазон адресов подсети: 10.254.1.0/24.**
* **Имя подсети: GatewaySubnet.**<br>Имя подсети *GatewaySubnet* обязательно для работы VPN-шлюза.
  * **Диапазон адресов подсети: 192.168.200.0/24.** 
* **Пул адресов VPN-клиента: 172.16.201.0/24.**<br>VPN-клиенты, подключающиеся к виртуальной сети с помощью этого подключения типа "точка — сеть", получают IP-адреса из пула адресов VPN-клиента.
* **Подписка**. Если у вас есть несколько подписок, убедитесь, что используется правильная.
* **Группа ресурсов: TestRG**.
* **Расположение: восточная часть США**.
* **DNS-сервер: IP-адрес** DNS-сервера, который нужно использовать для разрешения имен.
* **Имя шлюза: Vnet1GW**.
* **Имя общедоступного IP-адреса: VNet1GWPIP**.
* **Тип VPN: RouteBased.**

## <a name="before-beginning"></a>Подготовка
* Убедитесь в том, что у вас уже есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial).
* Установите последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Как установить и настроить Azure PowerShell](/powershell/azureps-cmdlets-docs). Если вы работаете с этой конфигурацией в PowerShell, убедитесь, что вы запустили ее от имени администратора. 

## <a name="a-namedeclareapart-1---log-in-and-set-variables"></a><a name="declare"></a>Часть 1. Вход и настройка переменных
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
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="a-nameconfigurevnetapart-2---configure-a-vnet"></a><a name="ConfigureVNet"></a>Часть 2. Настройка виртуальной сети
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

## <a name="a-namecertificatesapart-3---certificates"></a><a name="Certificates"></a>Часть 3. Сертификаты
Сертификаты используются в Azure для проверки подлинности VPN-клиентов в VPN-подключениях типа "точка — сеть". Данные общедоступного сертификата (а не закрытый ключ) экспортируются в виде CER-файла X.509 в кодировке Base-64 из корневого сертификата, созданного с помощью корпоративного решения, или самозаверяющего корневого сертификата. Затем данные общедоступного сертификата импортируются из корневого сертификата в Azure. Кроме того, нужно создать сертификат клиента из корневого сертификата для клиентов. Для каждого клиента, которому нужно подключаться к виртуальной сети с помощью подключения типа "точка — сеть", требуется установить сертификат клиента, созданный из корневого сертификата.

### <a name="a-namecerastep-1---obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>Шаг 1. Получение CER-файла для корневого сертификата
Для необходимого корневого сертификата нужно получить данные общедоступного сертификата.

* Если используется корпоративная система для создания сертификатов, необходимо получить CER-файл для нужного корневого сертификата. 
* Если вы не планируете использовать корпоративное решение для создания сертификатов, нужно создать самозаверяющий корневой сертификат. При создании самозаверяющего сертификата для подключений "точка — сеть" рекомендуется использовать метод makecert. Самозаверяющие сертификаты можно создавать с помощью PowerShell, но такие сертификаты не содержат поля, необходимые для подключений "точка — сеть". Действия для Windows 10 см. в статье [Работа с самозаверяющими сертификатами для подключений типа "точка — сеть"](vpn-gateway-certificates-point-to-site.md).

1. Чтобы получить CER-файл из сертификата, откройте файл **certmgr.msc** и найдите корневой сертификат. Щелкните самозаверяющий корневой сертификат правой кнопкой мыши, выберите пункт **Все задачи**, а затем — **Экспорт**. Откроется **мастера экспорта сертификатов**.
2. В мастере нажмите кнопку **Далее**, выберите **Нет, не экспортировать закрытый ключ** и снова нажмите кнопку **Далее**.
3. На странице **Формат экспортируемого файла** выберите **Файлы X.509 (.CER) в кодировке Base-64**. Затем щелкните **Далее**. 
4. На странице **Имя экспортируемого файла** нажмите кнопку **Обзор**, чтобы перейти в расположение для экспорта сертификата. В поле **Имя файла**введите имя для файла сертификата. Нажмите кнопку **Далее**.
5. Нажмите кнопку **Готово** , чтобы экспортировать сертификат.

### <a name="a-namegenerateastep-2---generate-the-client-certificate"></a><a name="generate"></a>Шаг 2. Создание сертификата клиента
Теперь мы создадим сертификаты клиента. Можно создать уникальный сертификат для каждого клиента, который будет подключаться, или использовать один сертификат для нескольких клиентов. Преимущество уникальных клиентских сертификатов заключается в том, что при необходимости можно отозвать один сертификат. В противном случае, если все используют один и тот же сертификат клиента и вам необходимо отозвать сертификат для одного клиента, потребуется создать и установить новые сертификаты для всех клиентов, которые используют сертификат для проверки подлинности. Позже сертификаты клиента будут установлены на каждом клиентском компьютере.

* При использовании корпоративного решения для создания сертификатов создайте сертификат клиента с именем в формате 'name@yourdomain.com',, а не в формате NetBIOS "ДОМЕН\имя пользователя". 
* Дополнительные сведения об использовании самозаверяющего сертификата для создания сертификата клиента см. в статье [Работа с самозаверяющими сертификатами для подключений типа "точка — сеть"](vpn-gateway-certificates-point-to-site.md).

### <a name="a-nameexportclientcertastep-3---export-the-client-certificate"></a><a name="exportclientcert"></a>Шаг 3. Экспорт сертификата клиента
Этот сертификат требуется для проверки подлинности. После создания сертификата клиента его нужно экспортировать. Позже этот сертификат устанавливается на каждый клиентский компьютер.

1. Для экспорта сертификата клиента можно использовать команду *certmgr.msc*. Щелкните правой кнопкой мыши сертификат, который нужно экспортировать, щелкните **Все задачи** и выберите **Экспорт**.
2. Экспортируйте сертификат клиента с закрытым ключом. Это *PFX* -файл. Обязательно запишите или запомните пароль (ключ), который задали для этого сертификата.

### <a name="a-nameuploadastep-4---upload-the-root-certificate-cer-file"></a><a name="upload"></a>Шаг 4. Передача CER-файла корневого сертификата
Объявите переменную для имени сертификата, заменив имеющееся значение собственным.

        $P2SRootCertName = "Mycertificatename.cer"

Добавьте данные общедоступного сертификата для корневого сертификата в Azure. Вы можете отправить файлы для 20 корневых сертификатов. Не отправляйте закрытый ключ для корневого сертификата в Azure. После отправки CER-файла он используется в Azure для проверки подлинности клиентов, подключающихся к виртуальной сети. 

Добавьте собственный путь к файлу, а затем выполните командлеты.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4---create-the-vpn-gateway"></a><a name="creategateway"></a>Часть 4. Создание VPN-шлюза
Настройте и создайте шлюз для своей виртуальной сети. У параметра *-GatewayType* должно быть значение **Vpn**, а у параметра *-VpnType* — **RouteBased**. Это может занять до 45 минут.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5---download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Часть 5. Скачивание пакета конфигурации VPN-клиента
На клиентах с подключением к Azure типа "точка — сеть" должны быть установлены сертификат клиента и пакет конфигурации VPN-клиента. Для клиентов Windows доступны пакеты конфигурации VPN-клиента. Пакет VPN-клиента содержит сведения о настройке программного обеспечения VPN-клиента. Такое ПО встроено в ОС Windows и зависит от сети VPN, к которой необходимо подключиться. Пакет не устанавливает никакого дополнительного программного обеспечения. Дополнительные сведения см. в статье [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. После создания шлюза можно скачать пакет конфигурации клиента. В этом примере будет скачан пакет для 64-разрядных клиентов. Чтобы скачать 32-разрядный клиент, замените Amd64 на x86. Пакет для VPN-клиента также можно скачать на портале Azure.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. Командлет PowerShell вернет URL-адрес. Ниже приведен пример возвращаемого URL-адреса.
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Скопируйте и вставьте этот URL-адрес в веб-браузер, чтобы скачать пакет. Затем установите пакет на клиентском компьютере. При появлении всплывающего окна SmartScreen щелкните **Дополнительно**, а затем выберите **Выполнить в любом случае**, чтобы установить пакет.
4. На клиентском компьютере перейдите в раздел **Параметры сети** и щелкните **VPN**. Вы увидите подключение в списке. Оно будет содержать имя виртуальной сети, подключение к которой будет установлено, и выглядеть примерно так: 
   
    ![VPN-клиент](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png)

## <a name="a-nameclientcertificateapart-6---install-the-client-certificate"></a><a name="clientcertificate"></a>Часть 6. Установка сертификата клиента
Для прохождения проверки подлинности каждому клиентскому компьютеру требуется сертификат клиента. При установке сертификата клиента потребуется пароль, созданный при экспорте сертификата клиента.

1. Скопируйте PFX-файл на клиентский компьютер.
2. Дважды щелкните PFX-файл, чтобы установить его. Не меняйте место установки.

## <a name="a-nameconnectapart-7---connect-to-azure"></a><a name="connect"></a>Часть 7. Подключение к Azure
1. Чтобы подключиться к виртуальной сети, откройте VPN-подключения на клиентском компьютере и найдите созданное VPN-подключение. Его имя совпадает с названием вашей виртуальной сети. Щелкните **Подключить**. Может появиться всплывающее сообщение об использовании сертификата. В таком случае щелкните **Продолжить** , чтобы использовать более высокий уровень привилегий. 
2. На странице состояния **подключения** щелкните **Подключить**. Если появится окно **Выбор сертификата** , убедитесь в том, что отображается сертификат клиента, с помощью которого вы хотите подключиться к сети. Если окно не появится, выберите нужный сертификат в раскрывающемся списке и нажмите кнопку **ОК**.
   
    ![Подключение VPN-клиента к Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Теперь следует установить подключение.
   
    ![Подключение установлено](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

## <a name="a-nameverifyapart-8---verify-your-connection"></a><a name="verify"></a>Часть 8. Проверка подключения
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

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>Добавление и удаление доверенного корневого сертификата
Сертификаты используются для проверки подлинности VPN-клиентов в VPN-подключениях типа "точка — сеть". Ниже приведены пошаговые инструкции по добавлению и удалению корневых сертификатов. При добавлении CER-файла Base64-encoded X.509 в Azure вы указываете Azure доверять корневому сертификату, который представляет собой файл. 

Доверенные корневые сертификаты можно добавлять и удалять с помощью PowerShell или портала Azure. Если вы хотите сделать это с помощью портала Azure, выберите **Шлюз виртуальной сети > Параметры > Конфигурация "точка — сеть" > Корневые сертификаты**. Ниже описано выполнение этих задач с помощью PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Добавление доверенного корневого сертификата
В Azure можно добавить до 20 CER-файлов доверенных корневых сертификатов. Чтобы добавить корневой сертификат, сделайте следующее:

1. Создайте корневой сертификат и подготовьте его к добавлению в Azure. Экспортируйте открытый ключ как CER-файл X.509 в кодировке Base-64 и откройте его в текстовом редакторе. Затем скопируйте только раздел, указанный ниже. 
   
    Скопируйте значения, как показано в следующем примере.
   
    ![на основе сертификата.](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)
2. Укажите имя сертификата и сведения о ключе как значения переменных. Подставьте собственные значения, как показано в следующем примере.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Добавьте новый корневой сертификат. Можно добавлять только один сертификат за раз.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. Чтобы проверить, добавлен ли новый сертификат должным образом, можно выполнить следующий командлет.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Удаление доверенного корневого сертификата
Можно удалить доверенный корневой сертификат из Azure. При удалении доверенного сертификата сертификаты клиента, созданные на его основе, не смогут подключаться к Azure с использованием подключения типа "точка — сеть". Чтобы клиенты снова смогли подключаться, необходимо установить новый сертификат клиента, созданный на основе доверенного сертификата в Azure.

1. Чтобы удалить доверенный корневой сертификат, измените пример кода ниже.
   
    Объявите переменные.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Удалите сертификат.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Выполните следующий командлет, чтобы убедиться, что сертификат успешно удален. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>Управление списком отозванных сертификатов клиента
Можно отозвать сертификаты клиента. Список отзыва сертификатов позволяет выборочно запрещать подключение типа "точка-сеть" на основе отдельных сертификатов клиента. При удалении корневого сертификата (CER-файл) из Azure будет запрещен доступ для всех сертификатов клиента, созданных на основе отозванного корневого сертификата или подписанных им. Вы можете отозвать определенный сертификат клиента, если он не является корневым. В таком случае другие сертификаты, созданные на основе корневого сертификата, по-прежнему будут действовать.

Обычно корневой сертификат используется для управления доступом на уровнях группы или организации, а отозванный сертификат клиента — для точного контроля доступа для отдельных пользователей.

### <a name="revoke-a-client-certificate"></a>Отзыв сертификата клиента
1. Получите отпечаток сертификата клиента, который нужно отозвать.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "?ef2af033d0686820f5a3c74804d167b88b69982f"
2. Добавьте отпечаток в список отозванных отпечатков.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Убедитесь, что отпечаток добавлен в список отзыва сертификатов. Необходимо добавлять по одному отпечатку за раз.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Возобновление использования сертификата клиента
Можно возобновить использование сертификата клиента, удалив отпечаток из списка отозванных сертификатов клиента.

1. Удалите отпечаток из списка отозванных отпечатков сертификатов клиента.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Проверьте, удален ли отпечаток из списка отозванных отпечатков.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Дальнейшие действия
Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительные сведения о виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Feb17_HO3-->



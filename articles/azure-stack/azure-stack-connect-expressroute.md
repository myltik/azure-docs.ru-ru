---
title: Подключение Azure Stack к Azure с помощью ExpressRoute
description: Сведения о подключении виртуальных сетей в Azure Stack к виртуальным сетям в Azure с помощью ExpressRoute.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 544fc1bcc9212fd38938d58447f5050df2a08796
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29844927"
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Подключение Azure Stack к Azure с помощью ExpressRoute

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Виртуальные сети в Azure Stack можно подключить к виртуальным сетям в Azure двумя способами:
   * **Сеть-сеть**

     VPN-подключение по протоколу IPsec (протоколы IKE v1 и IKE v2). Для этого типа подключения требуется VPN-устройство или RRAS. Дополнительные сведения см. в статье [Подключение Azure Stack к Azure с помощью VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Прямое подключение к Azure из развертывания Azure Stack. ExpressRoute **не** поддерживает VPN-подключение через общедоступный Интернет. Дополнительные сведения об Azure ExpressRoute см. в [этой статье](../expressroute/expressroute-introduction.md).

В этой статье приведен пример подключения Azure Stack к Azure с помощью ExpressRoute.
## <a name="requirements"></a>Требования
Чтобы подключить Azure Stack к Azure с помощью ExpressRoute, вам потребуется:
* Подписка Azure, используемая при создании канала ExpressRoute и виртуальных сетей в Azure.
* Канал ExpressRoute, подготовленный через [поставщика услуг подключения](../expressroute/expressroute-locations.md).
* Маршрутизатор, к портам WAN которого подключен канал ExpressRoute.
* Локальная сеть маршрутизатора связана с мультитенантным шлюзом Azure Stack.
* Маршрутизатор должен поддерживать VPN-подключения типа "сеть — сеть" между интерфейсом локальной сети и мультитенантным шлюзом Azure Stack.
* Если в развертывание Azure Stack входит несколько клиентов, маршрутизатор должен поддерживать создание нескольких компонентов VRF (Virtual Routing and Forwarding).

На следующей схеме показана концептуальная схема сети после завершения конфигурации:

![Концептуальная схема](media/azure-stack-connect-expressroute/Conceptual.png)

**Схема 1**

На следующей схеме архитектуры показано подключение нескольких клиентов из инфраструктуры Azure Stack через маршрутизатор ExpressRoute к Azure в Microsoft Edge:

![Схема архитектуры](media/azure-stack-connect-expressroute/Architecture.png)

**Схема 2**

В примере в этой статье подключение к Azure через частный пиринг ExpressRoute устанавливается на основе такой же архитектуры. Это делается через VPN-подключение типа "сеть — сеть" из шлюза виртуальной сети в Azure Stack к маршрутизатору ExpressRoute. Далее в этой статье описано, как создать сквозное подключение между двумя виртуальными сетями из двух разных клиентов в Azure Stack к соответствующим виртуальным сетям в Azure. Вы можете добавить мультитенантные виртуальные сети и применить приведенные ниже шаги к каждому клиенту или развернуть виртуальную сеть с одним клиентом, как показано в этом примере.

## <a name="configure-azure-stack"></a>Настройка Azure Stack
Теперь создадим ресурсы, используемые при настройке среды Azure Stack как клиента. Необходимые действия представлены на схеме ниже. Следуя этим инструкциям, вы узнаете, как создавать ресурсы с помощью портала Azure Stack, но то же самое можно сделать и с помощью PowerShell.

![Этапы создания сетевых ресурсов](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Перед началом работы
Перед началом настройки вам потребуется:
* Развертывание Azure Stack.

   Сведения о развертывании Пакета средств разработки Azure Stack см. в [этой статье](azure-stack-deploy-overview.md).
* Предложение Azure Stack, на которое могут подписаться пользователи.

  Инструкции см. в статье [Make virtual machines available to your Azure Stack users](azure-stack-tutorial-tenant-vm.md) (Создание предложения по виртуальным машинам для пользователей Azure Stack).

### <a name="create-network-resources-in-azure-stack"></a>Создание сетевых ресурсов в Azure Stack

Ниже приведены инструкции по созданию необходимых ресурсов сети в Azure Stack для каждого клиента.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Создание виртуальной сети и подсети виртуальной машины
1. Войдите на пользовательский портал с помощью учетной записи пользователя (клиента).

2. На портале нажмите кнопку **Создать**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. В меню Marketplace выберите **Сети**.

4. В меню щелкните **Виртуальная сеть**.

5. Введите значения из таблицы ниже в соответствующие поля.

   |Поле  |Значение  |
   |---------|---------|
   |ИМЯ     |Tenant1VNet1         |
   |Пространство адресов     |10.1.0.0/16|
   |Имя подсети     |Tenant1-Sub1|
   |Диапазон адресов подсети     |10.1.1.0/24|

6. В поле **Подписка** должна появиться созданная ранее подписка.

    a. Создайте группу ресурсов или используйте имеющуюся, щелкнув **Использовать существующий**.

    Б. Проверьте значение расположения по умолчанию.

    c. Щелкните **Закрепить на панели мониторинга**.

    d. Нажмите кнопку **Создать**.



#### <a name="create-the-gateway-subnet"></a>Создание подсети шлюза
1. На панели мониторинга откройте созданный ресурс виртуальной сети (Tenant1VNet1).
2. В разделе "Параметры" выберите **Подсети**.
3. Щелкните **Подсеть шлюза**, чтобы добавить подсеть шлюза в виртуальную сеть.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. Подсети присвоено имя **GatewaySubnet** по умолчанию.
   Подсети шлюза являются специальными, и для правильной работы им должно быть присвоено конкретное имя.
5. Проверьте, указан ли в поле **Диапазон адресов** адрес **10.1.0.0/24**.
6. Нажмите кнопку **ОК**, чтобы создать подсеть шлюза.

#### <a name="create-the-virtual-network-gateway"></a>Создание шлюза виртуальной сети
1. На пользовательском портале Azure Stack нажмите кнопку **Создать**.
   
2. В меню Marketplace выберите **Сети**.
3. В списке сетевых ресурсов выберите **Шлюз виртуальной сети**.
4. В поле **Имя** введите **GW1**.
5. Щелкните пункт **Виртуальная сеть**, чтобы выбрать виртуальную сеть.
   Выберите **Tenant1VNet1** в списке.
6. Щелкните пункт меню **Общедоступный IP-адрес**. Когда откроется раздел **Выбрать общедоступный IP-адрес**, щелкните **Создать**.
7. В поле **Имя** введите **GW1-PiP** и нажмите кнопку **ОК**.
8. Для параметра **Тип VPN** должно быть установлено значение **Route-based** (На основе маршрутов) по умолчанию.
    Не изменяйте значение этого параметра.
9. Убедитесь, что для параметров **Подписка** и **Расположение** выбраны правильные значения. При необходимости ресурс можно закрепить на панели мониторинга. Нажмите кнопку **Создать**.

#### <a name="create-the-local-network-gateway"></a>Создание шлюза локальной сети

Ресурс шлюза локальной сети позволяет указать удаленный шлюз на другом конце VPN-подключения. В этом примере в качестве удаленного компонента используется подчиненный интерфейс локальной сети маршрутизатора ExpressRoute. В этом примере клиент 1 имеет удаленный адрес 10.60.3.255, как показано на схеме 2.

1. Войдите в систему физического компьютера Azure Stack.
2. Войдите на пользовательский портал с помощью учетной записи пользователя и нажмите кнопку **Создать**.
3. В меню Marketplace выберите **Сети**.
4. В списке сетевых ресурсов выберите **шлюз локальной сети**.
5. В поле **Имя** введите **ER-Router-GW**.
6. В поле **IP-адрес** введите значение со схемы 2. Подчиненный интерфейс локальной сети маршрутизатора ExpressRoute клиента 1 имеет IP-адрес 10.60.3.255. Введите IP-адрес соответствующего интерфейса маршрутизатора своей среды.
7. В поле **Адресное пространство** введите адресное пространство виртуальных сетей, которые нужно подключить к Azure. Для этого примера введите значение со схемы 2. Обратите внимание, что для клиента 1 требуются подсети **192.168.2.0/24** (центральная виртуальная сеть в Azure) и **10.100.0.0/16** (периферийная виртуальная сеть в Azure). Введите соответствующие подсети своей среды.
   > [!IMPORTANT]
   > В этом примере предполагается, что вы используете статические маршруты подключения VPN типа "сеть — сеть" между шлюзом Azure Stack и маршрутизатором ExpressRoute.

8. Убедитесь, что для полей **Подписка**, **Группа ресурсов** и **Расположение** выбраны правильные значения, и нажмите кнопку **Создать**.

#### <a name="create-the-connection"></a>Создание подключения
1. На пользовательском портале Azure Stack нажмите кнопку **Создать**.
2. В меню Marketplace выберите **Сети**.
3. В списке сетевых ресурсов выберите **Подключение**.
4. В разделе **основных** параметров в качестве значения параметра **Тип соединения** выберите **Site-to-site (IPSec)** (Сеть — сеть (IPSec)).
5. Заполните поля **Подписка**, **Группа ресурсов** и **Расположение** и нажмите кнопку **ОК**.
6. В разделе **Параметры** выберите **Шлюз виртуальной сети** и щелкните **GW1**.
7. Выберите **Local network gateway** (Шлюз локальной сети), а затем щелкните **ER Router GW**.
8. В поле **Имя подключения** введите **ConnectToAzure**.
9. В поле **Shared Key (PSK)** (Общий ключ (PSK)) введите **abc123** и нажмите кнопку **ОК**.
10. В разделе **Сводка** нажмите кнопку **ОК**.

    После создания подключения вы можете просмотреть общедоступный IP-адрес, используемый шлюзом виртуальной сети. Чтобы найти этот адрес на портале Azure Stack, перейдите к шлюзу виртуальной сети. В разделе **Обзор** найдите **общедоступный IP-адрес**. Запишите этот адрес. Он используется в качестве *внутреннего IP-адреса* в следующем разделе (если это требуется в вашем развертывании).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Чтобы гарантировать передачу данных через VPN-подключение, в каждой виртуальной сети Azure Stack необходимы виртуальные машины, отправляющие и получающие данные. Создайте виртуальную машину и поместите ее в подсеть виртуальной машины в виртуальной сети.

1. На пользовательском портале Azure Stack нажмите кнопку **Создать**.
2. В меню Marketplace выберите **Виртуальные машины**.
3. В списке образов виртуальных машин выберите образ **Windows Server 2016 Datacenter Eval** и нажмите кнопку **Создать**.
4. В разделе**Основные сведения** в поле **Имя** введите **VM01**.
5. Введите допустимое имя пользователя и пароль. Эта учетная запись будет использоваться для входа в виртуальную машину после ее создания.
6. Заполните поля **Подписка**, **Группа ресурсов** и **Расположение** и нажмите кнопку **ОК**.
7. В разделе **Размер** выберите размер этого экземпляра виртуальной машины, а затем нажмите кнопку **Выбрать**.
8. В разделе **Параметры** можно принять значения по умолчанию. Проверьте, выбрана ли виртуальная сеть **Tenant1VNet1** и задано ли для подсети значение **10.1.1.0/24**. Последовательно выберите **ОК**.
9. Просмотрите параметры в разделе **Сводка**, а затем нажмите кнопку **ОК**.

Повторите действия из раздела **Создание виртуальной сети и подсети виртуальной машины** вплоть до раздела **Создание виртуальной машины** для каждого клиента виртуальной сети.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Настройка виртуальной машины NAT для обхода шлюза
> [!IMPORTANT]
> Этот раздел применяется только к развертываниям Пакета средств разработки Azure Stack. В развертываниях с несколькими узлами преобразование сетевых адресов (NAT) не требуется.

Пакет средств разработки Azure Stack работает автономно и изолирован от сети, в которой развернут физический узел. Поэтому "внешняя" сеть VIP, к которой подключены шлюзы, на самом деле не внешняя. Она скрыта за маршрутизатором, выполняющим преобразование сетевых адресов.
 
В качестве маршрутизатора используется виртуальная машина Windows Server (**AzS-BGPNAT01**), на которой выполняется роль служб маршрутизации и удаленного доступа в инфраструктуре Пакета средств разработки Azure Stack. NAT необходимо настроить на виртуальной машине AzS-bgpnat01. Это позволит установить VPN-подключение типа "сеть — сеть" на обоих концах.

#### <a name="configure-the-nat"></a>Настройка NAT

1. Войдите в систему физического компьютера Azure Stack с помощью учетной записи администратора.
2. Скопируйте и измените приведенный ниже сценарий PowerShell, а затем выполните его в интегрированной среде сценариев Windows PowerShell с повышенными привилегиями. Замените пароль администратора на собственный. Адрес, полученный в выходных данных, — это *адрес EBGP*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Чтобы настроить NAT, скопируйте и измените приведенный ниже сценарий PowerShell, а затем выполните его в интегрированной среде сценариев Windows PowerShell с повышенными привилегиями. Замените *адрес EBGP* и *внутренний IP-адрес* (полученный в разделе **Создание подключения**) в сценарии на собственный.

   На схемах выше *адрес EBGP* имеет значение 10.10.0.62, а *внутренний IP-адрес* — 192.168.102.1.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Настройка Azure
После настройки Azure Stack можно приступить к развертыванию некоторых ресурсов Azure. На схеме ниже приведен пример виртуальной сети клиента в Azure. Вы можете использовать любые имя и схему адресации для своей виртуальной сети Azure. Однако диапазоны адресов виртуальных сетей в Azure и Azure Stack должны быть уникальными и не перекрываться.

![Виртуальные сети Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Схема 3**

Ресурсы, развертываемые в Azure и Azure Stack, похожи. Аналогичным образом вы развертываете следующее:
* виртуальные сети и подсети;
* подсеть шлюза;
* шлюз виртуальной сети;
* подключение;
* канал ExpressRoute.

Пример сетевой инфраструктуры Azure использует следующую конфигурацию:
* Используется центральная виртуальная сеть (192.168.2.0/24) и периферийная (10.100.0.0./16) (стандартная звездообразная топология).
* Рабочие нагрузки развертываются в периферийной виртуальной сети, а канал ExpressRoute подключается к центральной виртуальной сети.
* Две виртуальные сети связаны через пиринговую связь.

### <a name="configure-vnets"></a>Настройка виртуальных сетей
1. Войдите на портал Azure с помощью своих учетных данных Azure.
2. Создайте центральную виртуальную сеть в адресном пространстве 192.168.2.0/24. Создайте подсеть в диапазоне адресов 192.168.2.0/25 и добавьте подсеть шлюза в диапазон адресов 192.168.2.128/27.
3. Создайте периферийную виртуальную сеть и подсеть в диапазоне адресов 10.100.0.0/16.


Дополнительные сведения о создании виртуальной сети в Azure см. в [этой статье](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Настройка канала ExpressRoute

1. Просмотрите предварительные требования ExpressRoute в [этой статье](../expressroute/expressroute-prerequisites.md).
2. Создайте канал ExpressRoute с помощью подписки Azure, как описано в статье [Создание и изменение канала ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
3. Предоставьте ключ службы из предыдущего шага поставщику или хостеру, чтобы они могли подготовить канал ExpressRoute на своей стороне.
4. Настройте частный пиринг в канале ExpressRoute, следуя указаниям из статьи [Создание и изменение пиринга для канала ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md).

### <a name="create-the-virtual-network-gateway"></a>Создание шлюза виртуальной сети

* Создайте шлюз виртуальной сети ExpressRoute в центральной виртуальной сети, следуя указаниям из статьи [Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md).

### <a name="create-the-connection"></a>Создание подключения

* Чтобы связать канал ExpressRoute с центральной виртуальной сетью, следуйте инструкциям в статье [Подключение виртуальной сети к каналу ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Установление пиринга между виртуальными сетями

* Установите пиринг между центральной и периферийной виртуальной сетью, следуя инструкциям в статье [Создание пиринга между виртуальными сетями, развернутыми с помощью Resource Manager в одной подписке](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). При настройке пиринговой связи между виртуальными сетями убедитесь, что выбраны следующие параметры:
   * Из центральной виртуальной сети к периферийной виртуальной сети: **Allow gateway transit** (Разрешить транзит шлюзов).
   * Из периферийной виртуальной сети к центральной виртуальной сети: **Use remote gateway** (Использовать удаленный шлюз).

### <a name="create-a-virtual-machine"></a>Создание виртуальной машины

* Разверните виртуальные машины рабочей нагрузки в периферийную виртуальную сеть.

Повторите эти действия для всех дополнительных виртуальных сетей клиента, которые необходимо подключить в Azure, через их соответствующие каналы ExpressRoute.

## <a name="configure-the-router"></a>Настройка маршрутизатора

Ниже приведена сквозная схема инфраструктуры, которую можно использовать при настройке маршрутизатора ExpressRoute. На этой схеме показаны два клиента (клиент 1 и клиент 2) и их соответствующие каналы ExpressRoute. Каждый клиент связан с собственным компонентом VRF (Virtual Routing and Forwarding) в локальной и глобальной сети маршрутизатора ExpressRoute. Это обеспечивает сквозную изоляцию между двумя клиентами. При настройке обратите внимание на IP-адреса, используемые в интерфейсах маршрутизатора.

![Сквозная схема](media/azure-stack-connect-expressroute/EndToEnd.png)

**Схема 4**

Вы можете использовать любой маршрутизатор, поддерживающий протокол IKEv2 VPN и BGP, чтобы разорвать VPN-подключение типа "сеть — сеть" с Azure Stack. Тот же маршрутизатор используется при подключении к Azure через канал ExpressRoute. 

Ниже приведен пример конфигурации из Cisco ASR 1000, поддерживающей сетевую инфраструктуру на схеме 4.

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Проверка подключения

После установления подключения типа "сеть — сеть" и настройки канала ExpressRoute следует проверить подключение. Это простая задача.  Войдите на одну из виртуальных машин, созданных в виртуальной сети Azure, и проверьте связь с виртуальной машиной, созданной в среде Azure Stack (или наоборот). 

Чтобы убедиться, что вы передаете трафик через подключение типа "сеть — сеть" и ExpressRoute, необходимо проверить связь с выделенным IP-адресом виртуальной машины на обеих сторонах, а не с виртуальным IP-адресом. Чтобы сделать это, необходимо найти и записать адрес на другом конце подключения.

### <a name="allow-icmp-in-through-the-firewall"></a>Разрешение ICMP-трафика через брандмауэр
По умолчанию Windows Server 2016 не разрешает передачу ICMP-пакетов через брандмауэр. На каждой виртуальной машине, используемой при тестировании, выполните следующий командлет в окне PowerShell с повышенными привилегиями:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Проверка связи с виртуальной машиной Azure Stack

1. Войдите на пользовательский портал Azure Stack с помощью учетной записи клиента.
2. В области навигации слева щелкните **Виртуальные машины**.
3. Найдите созданную ранее виртуальную машину и щелкните ее.
4. В разделе виртуальной машины нажмите кнопку **Подключение**.
5. Откройте окно PowerShell с повышенными привилегиями и введите **ipconfig/all**.
6. Найдите IPv4-адрес в выходных данных и запишите его. Проверьте связь с этим адресом с виртуальной машины в виртуальной сети Azure. В примере среды этот адрес получен из подсети 10.1.1.x/24. В вашей среде адрес может отличаться. Тем не менее он должен находиться в пределах подсети, созданной для подсети виртуальной сети клиента.


### <a name="view-data-transfer-statistics"></a>Просмотр статистики передачи данных

Если вы хотите знать, какой объем трафика передается через подключение, эти сведения можно просмотреть в разделе "Подключение" на пользовательском портале Azure Stack. Эти сведения также позволяют убедиться в том, что проверка связи действительно прошла через подключения VPN и ExpressRoute.

1. Войдите на пользовательский портал Microsoft Azure Stack с помощью учетной записи клиента.
2. Перейдите к группе ресурсов, в которой создан VPN-шлюз, и выберите тип объекта **Подключения**.
3. В списке щелкните подключение **ConnectToAzure**.
4. В разделе **Подключение** можно просмотреть статистику в полях **Входящие данные** и **Исходящие данные**. Там будут отображаться ненулевые значения.

   ![Входящие и выходящие данные](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Дополнительная информация
[Развертывание приложений в Azure и Azure Stack](azure-stack-solution-pipeline.md)

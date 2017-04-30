---
title: "Соединение локальной сети с виртуальной сетью Azure с помощью VPN типа &quot;сеть — сеть&quot; и интерфейса командной строки | Документация Майкрософт"
description: "Сведения о создании подключения IPsec между локальной сетью и виртуальной сетью Azure через общедоступный Интернет. Они помогут вам создать подключение типа &quot;сеть — сеть&quot; с использованием VPN-шлюза и интерфейса командной строки."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/26/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Создание виртуальной сети с VPN типа "сеть — сеть" с помощью интерфейса командной строки

В этой статье показано, как с помощью Azure CLI создавать подключение типа "сеть — сеть" с использованием VPN-шлюза между вашей локальной сетью к виртуальной. Приведенные в этой статье инструкции относятся к модели развертывания с помощью Resource Manager. Эту конфигурацию также можно создать с помощью разных средств или моделей развертывания, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager — PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager — интерфейс командной строки](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Классическая модель — портал Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Классическая модель — классический портал](vpn-gateway-site-to-site-create.md)
> 
>


![Схема подключения типа "сеть — сеть" через VPN-шлюз](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

Подключение VPN-шлюза типа "сеть — сеть" используется для подключения между локальной сетью и виртуальной сетью Azure через туннель VPN по протоколу IPsec/IKE (IKEv1 или IKEv2). Для этого типа подключения требуется локальное VPN-устройство, которому назначен внешний общедоступный IP-адрес. Дополнительные сведения о VPN-шлюзах см. в [этой статье](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Перед началом работы

Перед началом настройки убедитесь, что удовлетворены следующие требования:

* Убедитесь, что выбрана модель развертывания с помощью Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Совместимое VPN-устройство и пользователь, который может настроить его. Дополнительные сведения о совместимых устройствах VPN и их настройке см. в [этой статье](vpn-gateway-about-vpn-devices.md).
* Внешний общедоступный IPV4-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
* Если вы не знаете диапазоны IP-адресов в своей конфигурации локальной сети, найдите того, кто сможет предоставить вам нужную информацию. При создании этой конфигурации необходимо указать префиксы диапазона IP-адресов, которые Azure будет направлять к локальному расположению. Ни одна из подсетей локальной сети не может перекрывать виртуальные подсети, к которым вы хотите подключиться. 
* Последняя версия команд интерфейса командной строки (версии 2.0 или более поздней). Сведения об установке команд интерфейса командной строки см. в статье [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Установка Azure CLI 2.0).

### <a name="example-values"></a>Примеры значений

Следующие значения можно использовать для создания тестовой среды или для лучшего понимания примеров в этой статье.

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Вход в Azure

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```

Если у вас есть несколько подписок Azure, укажите подписки для этой учетной записи.

```azurecli
Az account list --all
```

Укажите подписку, которую нужно использовать.

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2. Создание группы ресурсов

В следующем примере создается группа ресурсов с именем TestRG1 в расположении eastus. Если у вас уже есть группа ресурсов в регионе, где вы хотите создать виртуальную сеть, вы можете воспользоваться ею.

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3. Создать виртуальную сеть

Если у вас нет виртуальной сети, создайте ее. При создании виртуальной сети убедитесь, что указанные адресные пространства не перекрываются ни с одним из адресных пространств, которые используются в локальной сети. 

В следующем примере создаются виртуальная сеть TestVNet1 и подсеть Subnet1.

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>Создание подсети шлюза

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

Для этой конфигурации необходима также подсеть шлюза. Шлюз виртуальной сети использует подсеть шлюза, которая содержит IP-адреса, используемые службами VPN-шлюза. При создании подсеть шлюза нужно назвать GatewaySubnet. Если будет присвоено другое имя, вы создадите подсеть, но Azure не будет рассматривать ее как подсеть шлюза.

Размер указанной подсети шлюза зависит от конфигурации VPN-шлюза, которую вы хотите создать. Хотя можно создать подсеть шлюза размером /29, рекомендуется создать подсеть большего размера, включающую несколько адресов, выбрав значение /27 или /28. Использование более крупной подсети для шлюза позволяет создавать достаточное число IP-адресов с учетом возможных конфигураций в будущем.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Создание шлюза локальной сети

Обычно термин "шлюз локальной сети" означает локальное расположение. Присвойте сайту имя, по которому Azure может обращаться к этому сайту, а затем укажите IP-адрес локального VPN-устройства, к которому вы подключитесь. Вы можете также указать префиксы IP-адресов, которые будут направляться через VPN-шлюз к VPN-устройству. Указываемые префиксы адресов расположены в локальной сети. При изменении локальной сети вы сможете без проблем обновить эти префиксы.

Используйте следующие значения:

* Параметр *--gateway-ip-address* — это IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT).
* *--local-address-prefixes* — это локальные адресные пространства.

В следующем примере показано, как добавить шлюз локальной сети с несколькими префиксами адреса:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Запрос общедоступного IP-адреса

Запросите общедоступный IP-адрес, который будет выделен для VPN-шлюза виртуальной сети. По этому IP-адресу будет подключаться VPN-устройство.

Сейчас шлюз виртуальной сети для модели развертывания, в которой используется Resource Manager, поддерживает только общедоступные IP-адреса, выделяемые динамически. Но это не значит, что IP-адрес изменится. IP-адрес VPN-шлюза изменяется только после его удаления и повторного создания. Общедоступный IP-адрес шлюза виртуальной сети остается прежним после изменения размера, сброса или обновления VPN-шлюза, а также после других процедур по его обслуживанию. 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Создание VPN-шлюза

Создайте VPN-шлюз виртуальной сети. Создание VPN-шлюза может занять от 45 минут и более.

Используйте следующие значения:

* Для конфигурации "сеть — сеть" задайте для параметра *--gateway-type* значение *Vpn*. Тип шлюза всегда зависит от реализуемой конфигурации. Дополнительные сведения см. в разделе [Типы шлюзов](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* У параметра *--vpn-type* может быть значение *RouteBased* (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *PolicyBased* (в некоторых документах — шлюз со статической маршрутизацией). Этот параметр зависит от требований устройства, к которому вы подключаетесь. Дополнительные сведения о VPN-шлюзах см. в [этой статье](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* У параметра *--sku* может быть значение Basic, Standard или HighPerformance. К определенным номерам SKU применяются ограничения настройки. Дополнительные сведения см. в разделе о [номерах SKU шлюзов](vpn-gateway-about-vpngateways.md#gateway-skus).

После выполнения этой команды вы не увидите ответа или выходных данных. Процесс создания шлюза занимает около 45 минут.

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. Настройка устройства VPN

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
Чтобы найти общедоступный IP-адрес шлюза виртуальной сети, используйте следующий пример, заменив значения собственными: Для удобства чтения выходные данные форматируются для отображения списка общедоступных IP-адресов в формате таблицы.

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9. Создание VPN-подключения

Создайте VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и локальным VPN-устройством. Обратите особое внимание на значение общего ключа, которое должно соответствовать значению заданного общего ключа для VPN-устройства.

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Через некоторое время будет установлено подключение.

## <a name="toverify"></a>10. Проверка VPN-подключения

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

Если вы хотите использовать другой метод проверки подключения, см. статью [Проверка соединения VPN-шлюза](vpn-gateway-verify-connection-resource-manager.md).

## <a name="common-tasks"></a>Стандартные задачи

### <a name="to-view-local-network-gateways"></a>Просмотр локальных сетевых шлюзов

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>Изменение префиксов IP-адресов для локального сетевого шлюза
Если вам нужно изменить префиксы для шлюза локальной сети, сделайте следующее. При каждом изменении нужно указать полный список префиксов, а не только те префиксы, которые вы хотите изменить.

- **Если имеется указанное подключение**, используйте следующий пример. Укажите полный список префиксов, состоящий из существующих префиксов и тех, которые требуется добавить. В этом примере 10.0.0.0/24 и 20.0.0.0/24 уже существуют. Добавим префиксы 30.0.0.0/24 и 40.0.0.0/24.

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **Если у вас нет указанного подключения**, воспользуйтесь той же командой, которую вы использовали для создания шлюза локальной сети. Эта команда также используется для обновления IP-адреса шлюза VPN-устройства. Используйте ее, только если у вас еще нет подключения. В этом примере 10.0.0.0/24, 20.0.0.0/24, 30.0.0.0/24 и 40.0.0.0/24 уже существуют. Мы указываем только префиксы, которые нужно сохранить. В этом случае это 10.0.0.0/24 и 20.0.0.0/24.

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>Изменение IP-адреса шлюза для локального сетевого шлюза

В этой конфигурации IP-адрес является IP-адресом VPN-устройства, для которого создается подключение. При изменении IP-адреса VPN-устройства можно изменить значение. IP-адреса можно изменять даже при наличии подключения шлюза.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

При просмотре результатов убедитесь, что префиксы IP-адресов включены.

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>Просмотр общедоступных IP-адресов шлюза виртуальной сети

Чтобы найти общедоступный IP-адрес шлюза виртуальной сети, используйте следующий пример. Для удобства чтения выходные данные форматируются для отображения списка общедоступных IP-адресов в формате таблицы.

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>Проверка значений общего ключа

Убедитесь, что значение общего ключа совпадает со значением, использованным для настройки VPN-устройства. Если это не так, выполните подключение еще раз, используя значение с устройства, или воспользуйтесь предыдущим значением устройства. Значения должны совпадать.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>Дальнейшие действия

*  Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительные сведения о виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).
* Сведения о принудительном туннелировании см. в [этом разделе](vpn-gateway-forced-tunneling-rm.md).
* Список сетевых команд Azure CLI см. в статье об [Azure CLI](https://docs.microsoft.com/cli/azure/network).

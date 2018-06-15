---
title: Расширение возможностей HDInsight с помощью виртуальной сети Azure | Документы Майкрософт
description: Узнайте, как использовать виртуальную сеть Azure для подключения HDInsight к другим облачным ресурсам или к ресурсам в центре обработки данных.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: 9ad7a35864f8e8a1aa95f0b54174a792f069ba18
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700712"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Расширение возможностей HDInsight с помощью виртуальной сети Azure

Узнайте, как использовать [виртуальную сеть Azure](../virtual-network/virtual-networks-overview.md) для расширения возможностей HDInsight. Виртуальная сеть Azure обеспечивает реализацию следующих сценариев:

* Подключение к HDInsight непосредственно из локальной сети.

* Подключение к хранилищам данных HDInsight в виртуальной сети Azure.

* Прямой доступ к службам Hadoop, недоступным из Интернета. Например, возможность напрямую работать с API Kafka или использовать API Java для HBase.

> [!WARNING]
> Информация в этом документе требует понимания принципов работы сети TCP/IP. Если вы не знакомы с ними, перед внесением изменений в рабочие сети обратитесь к специалистам, знакомым с сетями TCP/IP.

> [!IMPORTANT]
> Если вам необходимы пошаговые инструкции по подключению HDInsight к локальной сети с помощью виртуальной сети Azure, то см. статью [Подключение HDInsight к локальной сети](connect-on-premises-network.md).

## <a name="planning"></a>Планирование

Ниже перечислены вопросы, на которые необходимо ответить, если планируется установка HDInsight в виртуальной сети.

* Необходимо ли установить HDInsight в существующей виртуальной сети? Или вы создаете новую сеть?

    При использовании существующей виртуальной сети может потребоваться изменить параметры конфигурации сети, прежде чем устанавливать HDInsight. Дополнительные сведения см. в разделе [Добавление HDInsight в существующую виртуальную сеть](#existingvnet).

* Необходимо ли подключить виртуальную сеть, содержащую HDInsight, к другой виртуальной сети или к локальной сети?

    Для удобства работы с ресурсами в сетях может потребоваться создать пользовательский DNS-сервер и настроить переадресацию DNS. Дополнительные сведения см. в разделе [Подключение нескольких сетей](#multinet).

* Необходимо ли ограничить/перенаправить входящий или исходящий трафик в HDInsight?

    Для HDInsight требуется неограниченная связь с определенными IP-адресами в центре обработки данных Azure. Существует несколько портов, которые следует разрешить в брандмауэрах для взаимодействия с клиентами. Дополнительные сведения см. в разделе [Управление сетевым трафиком](#networktraffic).

## <a id="existingvnet"></a>Добавление HDInsight в существующую виртуальную сеть

Следуйте инструкциям в этом разделе, чтобы узнать, как добавить новый кластер HDInsight в существующую виртуальную сеть Azure.

> [!NOTE]
> В виртуальную сеть невозможно добавить существующий кластер HDInsight.

1. Какая модель используется для развертывания виртуальной сети: классическая модель или развертывание с помощью диспетчера ресурсов?

    Для HDInsight 3.4 и более поздней версии требуется виртуальная сеть диспетчера ресурсов. Для работы более ранних версий HDInsight требовалась классическая виртуальная сеть.

    Если существующая сеть является классической виртуальной сетью, необходимо создать виртуальную сеть диспетчера ресурсов и затем соединить их друг с другом. [Подключение виртуальных сетей из разных моделей развертывания с помощью портала](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    После присоединения экземпляр HDInsight, который установлен в сети диспетчера ресурсов, может взаимодействовать с ресурсами в классической сети.

2. Используется ли принудительное туннелирование? Принудительное туннелирование — это настройка подсети, которая принудительно отправляет исходящий интернет-трафик на устройство для его проверки и ведения журнала. HDInsight не поддерживает принудительное туннелирование. Перед установкой HDInsight в подсеть либо удалите принудительное туннелирование, либо создайте новую подсеть для HDInsight.

3. Применяются ли группы безопасности сети, определяемые пользователем маршруты или виртуальные сетевые устройства для ограничения входящего или исходящего трафика виртуальной сети?

    Поскольку HDInsight является управляемой службой, ей требуется неограниченный доступ к нескольким IP-адресам в центре обработки данных Azure. Чтобы разрешить взаимодействие с этими IP-адресами, обновите существующие группы безопасности сети и определяемые пользователем маршруты.

    В состав HDInsight входит несколько служб, которые используют различные порты. Не блокируйте трафик на этих портах. Список портов, трафик через которые следует разрешить на межсетевых экранах виртуального устройства, см. в разделе [Безопасность](#security).

    Чтобы найти существующую конфигурацию безопасности, используйте следующие команды Azure PowerShell или Azure CLI:

    * Группы безопасности сети

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Дополнительные сведения см. в документе, посвященном [устранению неполадок с группами безопасности сети](../virtual-network/diagnose-network-traffic-filter-problem.md).

        > [!IMPORTANT]
        > Правила группы безопасности сети применяются в порядке, основанном на приоритете правила. Применяется первое правило, которое соответствует шаблону трафика; другие правила не применяются к этому трафику. Правила применяются в следующем порядке: от правила с максимальными разрешениями к правилу с минимальными разрешениями. Дополнительные сведения см. в документе [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/security-overview.md).

    * Определяемые пользователем маршруты

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Дополнительные сведения см. в документе, посвященном [устранению неполадок с маршрутами](../virtual-network/diagnose-network-routing-problem.md).

4. Создайте кластер HDInsight и выберите виртуальную сеть Azure во время настройки. Следуйте инструкциям в следующих документах, чтобы понять процесс создания кластера:

    * [Создание кластера HDInsight с использованием портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Создание кластера HDInsight с использованием Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Создание кластеров HDInsight с помощью интерфейса командной строки Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Создание кластеров Hadoop в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > Добавление кластера HDInsight в виртуальную сеть — это необязательный шаг настройки. При настройке кластера не забудьте выбрать виртуальную сеть.

## <a id="multinet"></a>Подключение нескольких сетей

Самая сложная задача в конфигурации с несколькими сетями — это разрешение имен между сетями.

Azure предоставляет разрешение имен для служб Azure, установленных в виртуальной сети. Эта встроенная функция разрешения имен позволяет HDInsight подключаться к следующим ресурсам с помощью полного доменного имени (FQDN):

* любые ресурсы, доступные в Интернете; например, microsoft.com, google.com;

* любой ресурс, который находится в той же виртуальной сети Azure, с помощью __внутреннего DNS-имени__ ресурса; например, если используется разрешение имен по умолчанию, ниже указаны примеры внутренних DNS-имен, которые назначаются рабочим узлам HDInsight:

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net;
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net.

    Оба этих узла могут взаимодействовать напрямую друг с другом и с другими узлами в HDInsight, используя внутренние DNS-имена.

При использовании разрешения имен по умолчанию HDInsight __запрещено__ разрешать имена ресурсов в сетях, которые присоединены к виртуальной сети. Например, подключение локальной сети к виртуальной сети является распространенной практикой. Если используется лишь разрешение имен по умолчанию, HDInsight не может обращаться к ресурсам локальной сети по имени. Это также относится и к ресурсам: ресурсы в локальной сети не могут обращаться к ресурсам в виртуальной сети по имени.

> [!WARNING]
> Перед созданием кластера HDInsight необходимо создать пользовательский DNS-сервер и настроить виртуальную сеть на использование этого сервера.

Чтобы включить разрешение имен между виртуальной сетью и ресурсами в присоединенных сетях, выполните указанные ниже действия.

1. Создайте пользовательский DNS-сервер в виртуальной сети Azure, где планируется установить HDInsight.

2. Настройте виртуальную сеть для использования с пользовательским DNS-сервером.

3. Найдите назначенный Azure DNS-суффикс для виртуальной сети. Это значение аналогично `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Сведения о поиске DNS-суффиксов см. в разделе [Пример: пользовательский DNS-сервер](#example-dns).

4. Настройте перенаправление между DNS-серверами. Конфигурация зависит от типа удаленной сети.

    * Если удаленная сеть является локальной, настройте DNS следующим образом:
        
        * __Пользовательский DNS-сервер__ (в виртуальной сети):

            * Перенаправляйте запросы DNS-суффиксов виртуальной сети на рекурсивный сопоставитель Azure (168.63.129.16). Azure обрабатывает запросы к ресурсам в виртуальной сети

            * Остальные запросы перенаправляйте на локальный DNS-сервер. Локальный DNS-сервер обрабатывает все остальные запросы на разрешение имен, включая запросы к интернет-ресурсам, таким как microsoft.com.

        * __Локальный DNS-сервер__: перенаправляйте запросы DNS-суффиксов виртуальной сети на пользовательский DNS-сервер. Последний затем перенаправляет их на рекурсивный сопоставитель Azure.

        Такая конфигурация пересылает запросы полных доменных имен с DNS-суффиксом для виртуальных сетей на пользовательский DNS-сервер. Все остальные запросы (даже для общедоступных интернет-адресов) обрабатываются локальным DNS-сервером.

    * Если удаленная сеть является еще одной виртуальной сетью Azure, настройте DNS следующим образом:

        * __Пользовательский DNS-сервер__ (в каждой виртуальной сети):

            * Запросы DNS-суффиксов виртуальных сетей перенаправляются на пользовательские DNS-серверы. DNS-сервер в каждой виртуальной сети должен разрешать ресурсы в своей сети.

            * Все остальные запросы перенаправляются на рекурсивный сопоставитель Azure. Рекурсивный сопоставитель отвечает за разрешение локальных и интернет-ресурсов.

        DNS-сервер для каждой сети перенаправляет запросы на другой сервер на основе DNS-суффикса. Все остальные запросы разрешаются рекурсивным сопоставителем Azure.

    Пример каждой конфигурации см. в разделе [Пример: пользовательский DNS-сервер](#example-dns).

Дополнительные сведения см. в документе [Разрешение имен для виртуальных машин и экземпляров ролей](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="directly-connect-to-hadoop-services"></a>Подключение напрямую к службам Hadoop

В большей части документации по HDInsight предполагается, что у вас есть доступ к кластеру через Интернет. Например, что вы можете подключиться к кластеру по адресу https://CLUSTERNAME.azurehdinsight.net. Этот адрес использует общедоступный шлюз, который будет недоступен, если вы использовали группы безопасности сети или определяемые пользователем маршруты для ограничения доступа из Интернета.

Для прямого подключения к Ambari и другим веб-страницам через виртуальную сеть сделайте следующее:

1. Чтобы найти внутренние полные доменные имена (FQDN) узлов кластера HDInsight, используйте один из следующих методов:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    В возвращенном списке узлов найдите полные доменные имена (FQDN) головных узлов и используйте эти имена для подключения к Ambari и другим веб-службам. Например, воспользуйтесь `http://<headnode-fqdn>:8080` для доступа к Ambari.

    > [!IMPORTANT]
    > Некоторые службы, размещенные на головных узлах, одновременно активны только на одном узле. Если при попытке доступа к службе на одном головном узле появляется сообщение об ошибке 404, переключитесь на другой головной узел.

2. Чтобы определить узел и порт, через которые доступна служба, обратитесь к документу [Порты, используемые службами Hadoop в HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a id="networktraffic"></a>Управление сетевым трафиком

Для управления сетевым трафиком в виртуальных сетях Azure можно использовать следующие методы:

* **Группы безопасности сети** (NSG) позволяют фильтровать входящий и исходящий трафик в сети. Дополнительные сведения см. в документе [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/security-overview.md).

    > [!WARNING]
    > HDInsight не поддерживает ограничение исходящего трафика.

* **Определяемые пользователем маршруты** (UDR) определяют то, как проходит трафик между ресурсами в сети. Дополнительные сведения см. в документе [Определяемые пользователем маршруты и IP-пересылка](../virtual-network/virtual-networks-udr-overview.md).

* **Сетевые виртуальные модули** реплицируют функциональные возможности устройств, таких как межсетевые экраны и маршрутизаторы. Дополнительные сведения см. в документе [Сетевые устройства](https://azure.microsoft.com/solutions/network-appliances).

Поскольку HDInsight является управляемой службой, ей требуется неограниченный доступ к службам работоспособности и управления Azure в облаке Azure. При использовании NSG и UDR необходимо убедиться, что эти службы могут взаимодействовать с HDInsight.

HDInsight предоставляет службы на нескольких портах. При использовании виртуального модуля межсетевого экрана необходимо разрешить трафик через порты, используемые для этих служб. Дополнительные сведения см. в разделе [Требуемые порты].

### <a id="hdinsight-ip"></a>HDInsight с группами безопасности сети и определяемые пользователем маршрутами

Если вы планируете использовать **группы безопасности сети** или **определяемые пользователем маршруты** для управления сетевым трафиком, выполните следующие действия, прежде чем устанавливать HDInsight:

1. Определите регион Azure, который планируется использовать для HDInsight.

2. Определите IP-адреса, необходимые для HDInsight. Дополнительные сведения см. в разделе [Требуемые IP-адреса](#hdinsight-ip).

3. Создайте или измените группы безопасности сети или определяемые пользователем маршруты для подсети, в которой планируется установить HDInsight.

    * __Группы безопасности сети__: разрешите __входящий__ трафик через порт __443__ с диапазона IP-адресов.
    * __определяемые пользователем маршруты__: создайте маршрут до каждого IP-адреса и задайте для параметра __Тип следующего прыжка__ значение __Интернет__.

Дополнительные сведения о группах безопасности сети и определяемых пользователем маршрутах см. в следующей документации:

* [группа безопасности сети](../virtual-network/security-overview.md).

* [Определяемые пользователем маршруты](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Принудительное туннелирование

Принудительное туннелирование — это определяемая пользователем конфигурация маршрутизации, где весь трафик из подсети принудительно направляется в определенную сеть или расположение, например в локальную сеть. HDInsight __не__поддерживает принудительное туннелирование.

## <a id="hdinsight-ip"></a> Требуемые IP-адреса

> [!IMPORTANT]
> Службы работоспособности и управления Azure должны иметь возможность взаимодействовать с HDInsight. Если вы используете группы безопасности сети или определяемые пользователем маршруты, то разрешите трафик с IP-адресов, чтобы эти службы имели доступ к HDInsight.
>
> Если для управления трафиком не используются группы безопасности сети или определяемые пользователем маршруты, то этот раздел можно пропустить.

Если вы используете группы безопасности сети или определяемые пользователем маршруты, то необходимо разрешить передачу трафика службы работоспособности и службы управления в HDInsight. Чтобы найти IP-адреса, которые нужно разрешить, выполните следующие действия.

1. Всегда должен быть разрешен трафик для следующих IP-адресов.

    | IP-адрес | Разрешенный порт | Направление |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Входящий трафик |
    | 23.99.5.239 | 443 | Входящий трафик |
    | 168.61.48.131 | 443 | Входящий трафик |
    | 138.91.141.162 | 443 | Входящий трафик |

2. Если ваш кластер HDInsight находится в одном из следующих регионов, необходимо разрешить трафик для IP-адресов, указанных для этого региона.

    > [!IMPORTANT]
    > Если используемый регион Azure не указан в списке, то используйте только четыре IP-адреса, приведенные на шаге 1.

    | Страна | Регион | Разрешенные IP-адреса | Разрешенный порт | Направление |
    | ---- | ---- | ---- | ---- | ----- |
    | Азия | Восточная Азия | 23.102.235.122</br>52.175.38.134 | 443 | Входящий трафик |
    | &nbsp; | Юго-Восточная Азия | 13.76.245.160</br>13.76.136.249 | 443 | Входящий трафик |
    | Австралия | Восточная часть Австралии | 104.210.84.115</br>13.75.152.195 | 443 | Входящий трафик |
    | &nbsp; | Юго-Восточная часть Австралии | 13.77.2.56</br>13.77.2.94 | 443 | Входящий трафик |
    | Бразилия | Южная часть Бразилии | 191.235.84.104</br>191.235.87.113 | 443 | Входящий трафик |
    | Канада | Восточная Канада | 52.229.127.96</br>52.229.123.172 | 443 | Входящий трафик |
    | &nbsp; | Центральная Канада | 52.228.37.66</br>52.228.45.222 | 443 | Входящий трафик |
    | Китай | Север Китая | 42.159.96.170</br>139.217.2.219 | 443 | Входящий трафик |
    | &nbsp; | Восток Китая | 42.159.198.178</br>42.159.234.157 | 443 | Входящий трафик |
    | Европа | Северная Европа | 52.164.210.96</br>13.74.153.132 | 443 | Входящий трафик |
    | &nbsp; | Западная Европа| 52.166.243.90</br>52.174.36.244 | 443 | Входящий трафик |
    | Германия | Центральная Германия | 51.4.146.68</br>51.4.146.80 | 443 | Входящий трафик |
    | &nbsp; | Северо-восточная Германия | 51.5.150.132</br>51.5.144.101 | 443 | Входящий трафик |
    | Индия | Центральная Индия | 52.172.153.209</br>52.172.152.49 | 443 | Входящий трафик |
    | Япония | Восточная часть Японии | 13.78.125.90</br>13.78.89.60 | 443 | Входящий трафик |
    | &nbsp; | Западная часть Японии | 40.74.125.69</br>138.91.29.150 | 443 | Входящий трафик |
    | Корея | Центральная Корея | 52.231.39.142</br>52.231.36.209 | 433 | Входящий трафик |
    | &nbsp; | Южная Корея | 52.231.203.16</br>52.231.205.214 | 443 | Входящий трафик
    | Великобритания | Западная часть Великобритании | 51.141.13.110</br>51.141.7.20 | 443 | Входящий трафик |
    | &nbsp; | Южная часть Великобритании | 51.140.47.39</br>51.140.52.16 | 443 | Входящий трафик |
    | США | Центральный регион США | 13.67.223.215</br>40.86.83.253 | 443 | Входящий трафик |
    | &nbsp; | Восток США | 13.82.225.233</br>40.71.175.99 | 443 | Входящий трафик |
    | &nbsp; | Северо-центральный регион США | 157.56.8.38</br>157.55.213.99 | 443 | Входящий трафик |
    | &nbsp; | Западно-центральная часть США | 52.161.23.15</br>52.161.10.167 | 443 | Входящий трафик |
    | &nbsp; | Запад США | 13.64.254.98</br>23.101.196.19 | 443 | Входящий трафик |
    | &nbsp; | Западный регион США 2 | 52.175.211.210</br>52.175.222.222 | 443 | Входящий трафик |

    Сведения об IP-адресах для Azure для государственных организаций см. в документе [Аналитика Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics).

3. Если в виртуальной сети используется пользовательский DNS-сервер, необходимо также разрешить доступ с адреса __168.63.129.16__. Этот адрес рекурсивного сопоставителя Azure. Дополнительные сведения см. в документе [Разрешение имен для виртуальных машин и экземпляров ролей](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Дополнительные сведения см. в разделе [Управление сетевым трафиком](#networktraffic).

## <a id="hdinsight-ports"></a> Требуемые порты

Если вы планируете использовать **виртуальный модуль межсетевого экрана** для защиты виртуальной сети, необходимо разрешить исходящий трафик через следующие порты:

* 53
* 443
* 1433
* 11000–11999;
* 14000–14999.

Список портов для определенных служб см. в документе [Порты, используемые службами Hadoop в HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Дополнительные сведения о правилах межсетевого экрана для виртуальных модулей см. в документе [Сценарий использования виртуальных устройств](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a id="hdinsight-nsg"></a>Пример: группы безопасности сети с HDInsight

В примерах в этом разделе демонстрируется порядок создания правил группы безопасности сети, которые разрешают HDInsight взаимодействовать со службами управления Azure. Прежде чем использовать эти примеры, настройте IP-адреса в соответствии с теми, которые применяются для вашего региона Azure. Эти сведения представлены в разделе [HDInsight с группами безопасности сети и определяемыми пользователем маршрутами](#hdinsight-ip).

### <a name="azure-resource-management-template"></a>Шаблон управления ресурсами Azure

Следующий шаблон управления ресурсами создает виртуальную сеть, которая ограничивает входящий трафик, но разрешает трафик с IP-адресов, требуемых для HDInsight. Этот шаблон также создает кластер HDInsight в виртуальной сети.

* [Развертывание защищенной виртуальной сети Azure и кластера HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Измените IP-адреса, используемые в этом примере, в соответствии с теми, которые применяются для вашего региона Azure. Эти сведения представлены в разделе [HDInsight с группами безопасности сети и определяемыми пользователем маршрутами](#hdinsight-ip).

### <a name="azure-powershell"></a>Azure PowerShell

Используйте следующий сценарий PowerShell для создания виртуальной сети, которая ограничивает входящий трафик и разрешает трафик с IP-адресов для региона Северная Европа.

> [!IMPORTANT]
> Измените IP-адреса, используемые в этом примере, в соответствии с теми, которые применяются для вашего региона Azure. Эти сведения представлены в разделе [HDInsight с группами безопасности сети и определяемыми пользователем маршрутами](#hdinsight-ip).

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzureRmVirtualNetwork
```

> [!IMPORTANT]
> В этом примере показано, как добавить правила, чтобы разрешить входящий трафик на требуемые IP-адреса. В нем не содержится правило, ограничивающее входящий доступ из других источников.
>
> В следующем примере показано, как включить доступ по SSH через Интернет:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Ниже приведен порядок действий по созданию виртуальной сети, которая ограничивает входящий трафик, но разрешает трафик с IP-адресов, требуемых для HDInsight.

1. Используйте следующую команду, чтобы создать новую группу безопасности сети с именем `hdisecure`. Замените значение параметра **RESOURCEGROUPNAME** на имя группы ресурсов, содержащей виртуальную сеть Azure. Замените значение параметра **LOCATION** на расположение (регион), в котором была создана группа.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    После создания группы вы получите сведения о новой группе.

2. Используйте следующую команду для добавления правил в новую группу безопасности сети. Эти правила разрешают входящий трафик через порт 443 от службы работоспособности и управления Azure HDInsight. Замените значение **RESOURCEGROUPNAME** именем группы ресурсов, содержащей виртуальную сеть Azure.

    > [!IMPORTANT]
    > Измените IP-адреса, используемые в этом примере, в соответствии с теми, которые применяются для вашего региона Azure. Эти сведения представлены в разделе [HDInsight с группами безопасности сети и определяемыми пользователем маршрутами](#hdinsight-ip).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Используйте следующую команду, чтобы получить уникальный идентификатор для этой группы безопасности сети:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Эта команда возвращает значение следующего вида:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Если вы не получили ожидаемых результатов, заключите идентификатор в команде в двойные кавычки.

4. Используйте следующую команду, чтобы применить группу безопасности сети к подсети. Замените __GUID__ и __RESOURCEGROUPNAME__ значениями, возвращенными на предыдущем шаге. Замените __VNETNAME__ и __SUBNETNAME__ именами виртуальной сети и подсети, которые необходимо создать.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    После выполнения этой команды можно установить HDInsight в виртуальную сеть.

> [!IMPORTANT]
> Данные действия только открывают доступ к службе работоспособности и управления HDInsight в облаке Azure. Весь остальной доступ к кластеру HDInsight из-за пределов виртуальной сети блокируется. Чтобы разрешить доступ из-за пределов виртуальной сети, необходимо добавить дополнительные правила группы безопасности сети.
>
> В следующем примере показано, как включить доступ по SSH через Интернет:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Пример: конфигурация DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Разрешение DNS-имен между виртуальной сетью и подключенной локальной сетью

Данный пример исходит из следующих предположений:

* Имеется виртуальная сеть Azure, подключенная к локальной сети посредством VPN-шлюза.

* Пользовательский DNS-сервер в виртуальной сети работает под управлением операционной системы Unix или Linux.

* На пользовательском DNS-сервере установлен [Bind](https://www.isc.org/downloads/bind/).

На пользовательском DNS-сервере в виртуальной сети:

1. Воспользуйтесь Azure PowerShell или Azure CLI для поиска DNS-суффикса виртуальной сети:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. На пользовательском DNS-сервере для виртуальной сети добавьте в файл `/etc/bind/named.conf.local` следующее содержимое:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Замените значение `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` DNS-суффиксом виртуальной сети.

    Такая конфигурация обеспечивает перенаправление запросов DNS-суффиксов виртуальной сети на рекурсивный сопоставитель Azure.

2. На пользовательском DNS-сервере для виртуальной сети добавьте в файл `/etc/bind/named.conf.options` следующее содержимое:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Замените значение `10.0.0.0/16` диапазоном IP-адресов виртуальной сети. Эта запись разрешает запросы на разрешение имен в этом диапазоне.

    * Добавьте диапазон IP-адресов локальной сети в раздел `acl goodclients { ... }`.  Эта запись разрешает запросы на разрешения имен с ресурсов в локальной сети.
    
    * Замените значение `192.168.0.1` IP-адресом локального DNS-сервера. Эта запись направляет все остальные DNS-запросы на локальный DNS-сервер.

3. Чтобы использовать конфигурацию, перезапустите Bind. Например, `sudo service bind9 restart`.

4. Добавьте условный сервер пересылки на локальный DNS-сервер. Настройте условный сервер пересылки на отправку запросов DNS-суффикса из шага 1 на пользовательский DNS-сервер.

    > [!NOTE]
    > Обратитесь к документации по используемому программному обеспечению DNS, чтобы ознакомиться со сведениями о добавлении условного сервера пересылки.

После выполнения этих действий можно подключаться к ресурсам в любой из этих сетей, используя полные доменные имена (FQDN). Теперь можно установить HDInsight в виртуальной сети.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Разрешение имен между двумя подключенными виртуальными сетями

Данный пример исходит из следующих предположений:

* Имеется две виртуальных сети Azure, подключенные друг к другу посредством VPN-шлюза или пиринга.

* Пользовательский DNS-сервер в обеих виртуальных сетях работает под управлением операционной системы Unix или Linux.

* На пользовательских DNS-серверах установлен [Bind](https://www.isc.org/downloads/bind/).

1. Воспользуйтесь Azure PowerShell или Azure CLI для поиска DNS-суффикса в обеих виртуальных сетях:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. На пользовательском DNS-сервере добавьте в файл `/etc/bind/named.config.local` следующее содержимое. Внесите это изменение на пользовательские DNS-серверы в обеих виртуальных сетях.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Замените значение `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` DNS-суффиксом __другой__ виртуальной сети. Эта запись направляет запросы DNS-суффикса удаленной сети на пользовательский DNS-сервер в этой сети.

3. На пользовательских DNS-серверах в обеих виртуальных сетях добавьте в файл `/etc/bind/named.conf.options` следующее содержимое:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Замените значения `10.0.0.0/16` и `10.1.0.0/16` диапазонами IP-адресов виртуальных сетей. Эта запись позволяет ресурсам в каждой сети выполнять запросы к DNS-серверам.

    Запросы, которые не предназначены для получения DNS-суффиксов виртуальных сетей (например, microsoft.com), обрабатываются рекурсивным сопоставителем Azure.

4. Чтобы использовать конфигурацию, перезапустите Bind. Например, `sudo service bind9 restart` на обоих DNS-серверах.

После выполнения этих действий можно подключаться к ресурсам в виртуальной сети, используя полные доменные имена (FQDN). Теперь можно установить HDInsight в виртуальной сети.

## <a name="next-steps"></a>Дополнительная информация

* Полный пример настройки HDInsight для подключения к локальной сети см. в статье [Подключение HDInsight к локальной сети](./connect-on-premises-network.md).
* Инструкции по настройке кластеров Hbase в виртуальных сетях Azure см. в статье [Создание кластеров HBase в HDInsight в виртуальной сети Azure](hbase/apache-hbase-provision-vnet.md).
* Инструкции по настройке георепликации HBase см. в статье [Настройка репликации кластера HBase в виртуальных сетях Azure](hbase/apache-hbase-replication.md).
* Дополнительные сведения о виртуальных сетях Azure см. в статье [Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md).

* Дополнительные сведения о группах безопасности сети см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/security-overview.md).

* Дополнительные сведения о пользовательских маршрутах см. в статье [Определяемые пользователем маршруты и IP-пересылка](../virtual-network/virtual-networks-udr-overview.md).

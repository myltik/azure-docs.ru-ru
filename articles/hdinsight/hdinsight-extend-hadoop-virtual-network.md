---
title: "Расширение возможностей HDInsight с помощью виртуальной сети | Документация Майкрософт"
description: "Узнайте, как использовать виртуальную сеть Azure для подключения HDInsight к другим облачным ресурсам или к ресурсам в центре обработки данных."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 45de2422e79215ecfbacf5bd15712eb780c49016
ms.openlocfilehash: c0a99dadc1d588942ade14267bd45eff09080315


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Расширение возможностей HDInsight с помощью виртуальной сети Azure
Виртуальная сеть Azure позволяет расширять решения Hadoop для добавления локальных ресурсов (например. SQL Server), объединения нескольких типов кластеров HDInsight или создания защищенных частных сетей между ресурсами в облаке.

## <a name="prerequisites"></a>Предварительные требования

* Azure CLI 2.0 (предварительная версия): дополнительные сведения см. в статье [Install Azure CLI 2.0 (Preview)](https://docs.microsoft.com/cli/azure/install-az-cli2) (Установка Azure CLI 2.0 (предварительная версия)).

* Azure PowerShell: дополнительные сведения см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Для выполнения действий, описанных в этом документе, требуется последняя версия Azure CLI и Azure PowerShell. Если вы используете более старую версию, то команды могут отличаться. Для получения оптимальных результатов используйте предыдущие ссылки, чтобы установить последние версии.

## <a name="a-idwhatisawhat-is-azure-virtual-network"></a><a id="whatis"></a>Что такое виртуальная сеть Azure

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).
  
    ![Диаграмма конфигурации только для облачных решений](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    При использовании виртуальной сети для привязки служб Azure к Azure HDInsight можно реализовать следующие сценарии.
  
    * **Вызов служб или заданий HDInsight** с веб-сайтов Azure или из служб, работающих в виртуальных машинах Azure.
    * **Непосредственная передача данных** между HDInsight и базой данных SQL Azure, SQL Server или другими решениями для хранения данных в виртуальной машине.
    * **Объединение нескольких серверов HDInsight** в единое решение. Кластеры HDInsight представлены разными типами, соответствующими рабочей нагрузке или технологии, для которой настроен кластер. Создать кластер, в котором бы объединились несколько типов, например Storm и HBase, нельзя. Использование виртуальной сети позволяет нескольким кластерам напрямую взаимодействовать друг с другом.

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа «сеть — сеть» или «точка — сеть») при помощи виртуальной частной сети (VPN).
  
    Конфигурация «сеть — сеть» позволяет соединить несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или службы маршрутизации и удаленного доступа.
  
    ![Диаграмма конфигурации соединения "сервер-сервер"](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    Конфигурация «точка — сеть» позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.
  
    ![Диаграмма конфигурации соединения "клиент-сервер"](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    Использование виртуальной сети для связи облака и центра обработки данных позволяет реализовать подобные сценарии в конфигурации только облака. Но при этом помимо работы с ресурсами в облаке вы также получаете возможность работы с ресурсами в центре обработки данных.
  
    * **Непосредственная передача данных** между HDInsight и центром обработки данных. Например, использование Sqoop для передачи данных на SQL Server либо из него или чтение данных, созданных бизнес-приложением.
    * **Вызов служб или заданий HDInsight** из бизнес-приложения. Например, использование API HBase Java для хранения и извлечения данных из кластера HDInsight HBase.

Дополнительные сведения о характеристиках, преимуществах и возможностях виртуальной сети см. в статье [Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Виртуальную сеть Azure необходимо создавать до подготовки кластера HDInsight. Более подробную информацию можно найти в разделе [Настройка задач виртуальной сети](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Требования к виртуальной сети

> [!IMPORTANT]
> Для создания кластера HDInsight в виртуальной сети требуются особые настройки виртуальной сети, описанные в этом разделе.

### <a name="location-based-virtual-networks"></a>Географически привязанные виртуальные сети

Azure HDInsight поддерживает только виртуальные сети на основе расположения и на данный момент не работает с виртуальными сетями на основе территориальной группы.

### <a name="classic-or-v2-virtual-network"></a>Классическая виртуальная сеть или виртуальная сеть версии 2

Для кластеров на основе Windows требуется классическая виртуальная сеть, а для кластеров на основе Linux — виртуальная сеть Azure Resource Manager. Если у вас несоответствующий тип сети, она будет бесполезна при создании кластера.

Если имеются ресурсы в виртуальной сети, которую не может использовать создаваемый кластер, то вы можете создать новую виртуальную сеть, подходящую для кластера, и подключить ее к несовместимой виртуальной сети. Затем можно создать кластер в сети требуемой версии, и ему будут доступны ресурсы в другой сети, так как эти две сети соединены. Дополнительные сведения о подключении классических и новых виртуальных сетей см. в статье [Создание подключения между виртуальными сетями из разных моделей развертывания с помощью портала](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

### <a name="custom-dns"></a>Пользовательский DNS

При создании виртуальной сети Azure предоставляет разрешение имен по умолчанию для служб Azure, установленных в сети, таких как HDInsight. В то же время в таких ситуациях, как межсетевое разрешение доменных имен, может потребоваться использование собственной системы доменных имен (DNS), например при взаимодействии между службами, расположенными в двух соединенных виртуальных сетях. При использовании виртуальной сети Azure HDInsight поддерживает разрешение имен Azure по умолчанию, а также пользовательский DNS-сервер.

Дополнительные сведения об использовании собственного DNS-сервера с виртуальной сетью Azure см. в разделе **Разрешение имен с помощью собственного DNS-сервера** статьи [Разрешение имен для ВМ и экземпляров ролей](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

### <a name="secured-virtual-networks"></a>Защищенные виртуальные сети

Служба HDInsight является управляемой службой, и при ее инициализации и работе ей требуется доступ к Интернету. С ее помощью Azure может наблюдать за работоспособностью кластера, инициировать обработку отказа ресурсов кластера, изменять количество узлов в кластере с помощью операций масштабирования и выполнять другие задачи управления.

Если необходимо установить HDInsight в защищенной виртуальной сети, нужно разрешить входящий доступ через порт 443 для следующих IP-адресов, которые позволяют Azure управлять кластером HDInsight.

> [!IMPORTANT]
> IP-адреса, которые необходимо разрешить, зависят от региона, в котором находится кластер HDInsight и виртуальная сеть. Используйте приведенный ниже список, чтобы найти IP-адреса для необходимого региона.

Регион __Южная Бразилия__:

* 191.235.84.104
* 191.235.87.113

Регион __Восточная Канада__:

* 52.229.127.96
* 52.229.123.172

Регион __Центральная Канада__:

* 52.228.37.66
* 52.228.45.222

Регион __Западно-центральная часть США__:

* 52.161.23.15
* 52.161.10.167

Регион __Западная часть США 2__:

* 52.175.211.210
* 52.175.222.222

__Другие регионы__:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Разрешение доступа входящего трафика через порт 443 с этих адресов позволит успешно установить HDInsight в защищенной виртуальной сети.

> [!IMPORTANT]
> HDInsight не поддерживает ограничение исходящего трафика, ограничить можно только входящий трафик. При определении правил группы безопасности сети для подсети, содержащей HDInsight, используйте только правила для входящего трафика.

Следующий пример иллюстрирует создание новой группы безопасности сети, разрешающей необходимые адреса, и применение этой группы безопасности к подсети в вашей виртуальной сети. Адреса, используемые в этом примере, взяты из списка __Все другие регионы__, приведенного выше. Если вы в одном из отдельно перечисленных регионов, таких как __Западно-центральная часть США__, укажите в сценарии IP-адреса для вашего региона.

Предполагается, что вы уже создали виртуальную сеть и подсеть, в которую хотите установить HDInsight. Ознакомьтесь со статьей [Создание виртуальной сети с помощью портала Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!IMPORTANT]
> Обратите внимание, что в этих примерах используется значение `priority`, а правила сетевого трафика проверяются в порядке приоритета. Когда правило, соответствующее определенным условиям, обнаружено и применено, проверка правил останавливается.
> 
> При наличии настраиваемых правил, блокирующих входящий трафик (например, правило **Запретить все**), вам может потребоваться задать в этих примерах значения приоритета или настроить собственные правила, чтобы сначала применялись правила, определенные в примере, а не правила, блокирующие доступ. В противном случае сначала выполнится проверка правила **Запретить все**, а правила, определенные в этом примере, никогда не будут применены. Кроме того, не следует блокировать правила по умолчанию для виртуальной сети Azure. Например, не нужно создавать правило **Запретить все**, применяемое перед правилом по умолчанию **Allow VNET inbound** (Разрешить входящий трафик виртуальной сети) с приоритетом 65000.
> 
> Дополнительные сведения о применении правил и правилах для входящего и исходящего трафика по умолчанию см. в статье [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md).

**Использование Azure PowerShell**

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroup $nsg

**Использование Azure CLI**

1. Используйте следующую команду, чтобы создать новую группу безопасности сети с именем `hdisecure`. Замените **RESOURCEGROUPNAME** и **LOCATION** именем группы ресурсов, которая содержит виртуальную сеть Azure, и расположением (регионом), в котором была создана группа.
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    После создания группы вы получите сведения о новой группе.

2. Используйте следующую команду для добавления правил в новую группу безопасности сети. Эти правила разрешают входящий трафик через порт 443 от службы работоспособности и управления Azure HDInsight. Замените значение **RESOURCEGROUPNAME** именем группы ресурсов, содержащей виртуальную сеть Azure.
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. После создания правил используйте следующую команду, чтобы получить уникальный идентификатор для этой группы безопасности сети:

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    Эта команда возвращает значение следующего вида:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Если вы не получили ожидаемых результатов, заключите идентификатор в команде в двойные кавычки.

4. Используйте следующую команду, чтобы применить группу безопасности сети к подсети. Замените __GUID__ и __RESOURCEGROUPNAME__ значениями, возвращенными на предыдущем шаге. Замените __VNETNAME__ и __SUBNETNAME__ именами виртуальной сети и подсети, которые необходимо использовать при создании кластера HDInsight.
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    После выполнения этой команды можно успешно установить HDInsight в защищенной виртуальной сети в подсети, используемой при выполнении этих действий.

> [!IMPORTANT]
> Указанные выше действия только открывают доступ к службе работоспособности и управления HDInsight в облаке Azure. Это позволяет установить кластер HDInsight в подсети, однако по умолчанию доступ к кластеру HDInsight извне виртуальной сети блокируется. Понадобится добавить дополнительные правила группы безопасности сети, чтобы разрешить доступ извне виртуальной сети.
> 
> Например, чтобы разрешить SSH-доступ из Интернета, нужно добавить правило, аналогичное приведенному ниже: 
> 
> * Azure PowerShell — ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Интерфейс командной строки Azure — ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

Дополнительные сведения о группах безопасности сети см. в статье [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md). Сведения об управлении маршрутизацией в виртуальной сети Azure см. в статье [Что такое определяемые пользователем маршруты и IP-пересылка?](../virtual-network/virtual-networks-udr-overview.md)

## <a name="a-idtasksatasks-and-information"></a><a id="tasks"></a>Задачи и информация

В этом разделе приведены распространенные задачи, а также информация, которая может понадобиться при использовании HDInsight в виртуальной сети.

### <a name="determine-the-fqdn"></a>Определение полного доменного имени

Кластеру HDInsight будет назначено определенное полное доменное имя для виртуального сетевого интерфейса. Это адрес, который необходимо использовать при подключении к кластеру из других ресурсов в виртуальной сети. Чтобы определить полное доменное имя, используйте следующий URL-адрес для запроса службы управления Ambari:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [!NOTE]
> Дополнительные сведения об использовании Ambari в HDInsight см. в статье [Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API](hdinsight-monitor-use-ambari-api.md).

Необходимо указать имя кластера, службу и компонент, работающий в кластере, например диспетчер ресурсов YARN.

> [!NOTE]
> Возвращенные данные представляют собой документ JavaScript Object Notation (JSON), содержащий большой объем сведений о компоненте. Чтобы извлечь только полное доменное имя, следует получить значение `host_components[0].HostRoles.host_name` с помощью средства синтаксического анализа JSON.

Например, чтобы получить полное доменное имя из кластера Hadoop HDInsight, можно использовать один из следующих способов извлечения данных для диспетчера ресурсов YARN.

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) и [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### <a name="connecting-to-hbase"></a>Подключение к HBase

Чтобы удаленно подключаться к HBase с помощью API Java, необходимо определить адреса кворума ZooKeeper для кластера HBase и указать это в приложении.

Чтобы получить адрес кворума ZooKeeper, используйте один из следующих способов для выполнения запроса к службе управления Ambari.

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) и [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> Дополнительные сведения об использовании Ambari в HDInsight см. в статье [Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API](hdinsight-monitor-use-ambari-api.md).

После получения информации о кворуме используйте ее в клиентском приложении.

Например, для приложения Java, которое использует API HBase, в проект необходимо добавить файл **hbase site.xml** и указать в нем сведения о кворуме, как показано ниже.

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### <a name="verify-network-connectivity"></a>Проверка сетевого подключения

Некоторые службы, такие как SQL Server, могут ограничивать количество входящих сетевых подключений. Это будет препятствовать нормальной работе HDInsight с этими службами.

Если у вас возникли проблемы при доступе к службе из HDInsight, см. документацию по службе, чтобы убедиться, чтобы вы включили доступ к сети. Вы также можете проверить доступ к сети, создав виртуальную машину Azure в той же виртуальной сети и с помощью клиентских служебных программ проверив, может ли эта виртуальная машина подключаться к службе через виртуальную сеть.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Дальнейшие действия

В следующих примерах демонстрируется, как использовать HDInsight в виртуальной сети Azure.

* [Анализ полученных с датчиков данных с использованием Apache Storm, концентратора событий и базы данных HBase в службе HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md). Сведения о том, как настроить кластер Storm и HBase в виртуальной сети, а также как удаленно записать данные в HBase из Storm.
* [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Сведения о подготовке кластеров Hadoop, а также сведения об использовании виртуальной сети Azure.
* [Использование Sqoop с Hadoop в HDInsight (SSH)](hdinsight-use-sqoop-mac-linux.md). Сведения об использовании Sqoop для передачи данных с помощью SQL Server по виртуальной сети.

Дополнительные сведения о виртуальных сетях Azure см. в статье [Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md).




<!--HONumber=Feb17_HO2-->



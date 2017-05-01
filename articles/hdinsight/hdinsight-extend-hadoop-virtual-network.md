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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 938f6e4bf7d9d6418c9b10531725807a0a9dbce4
ms.lasthandoff: 04/14/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Расширение возможностей HDInsight с помощью виртуальной сети Azure

Узнайте, как использовать виртуальные сети Azure с HDInsight для реализации следующих сценариев:

* Ограничение доступа к HDInsight. Например, предотвращение входящего трафика из Интернета.

* Прямой доступ к службам в HDInsight, недоступным из Интернета. Например, возможность напрямую работать с брокерами Kafka или использовать API Java для HBase.

* Подключение служб к HDInsight напрямую. Например, импорт или экспорт данных для SQL Server в центре обработки данных с помощью Oozie.

* Создание решений, использующих несколько кластеров HDInsight. Например, анализ данных, хранящихся в Kafka, с помощью Spark или Storm.

## <a name="prerequisites"></a>Предварительные требования

* Azure CLI 2.0: дополнительные сведения см. в статье [Установка и настройка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

* Azure PowerShell: дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

> [!NOTE]
> Для выполнения действий, описанных в этом документе, требуется последняя версия Azure CLI и Azure PowerShell. Если вы используете более старую версию, то команды могут отличаться. Для получения оптимальных результатов используйте предыдущие ссылки, чтобы установить последние версии.

## <a id="whatis"></a>Что такое виртуальная сеть Azure

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения.

Ниже приведены сведения об использовании HDInsight в виртуальной сети.

* __Классические виртуальные сети и виртуальные сети Resource Manager__. Воспользуйтесь следующей таблицей, чтобы определить тип сети, который следует использовать в зависимости от операционной системы кластера HDInsight:

    | Операционная система HDInsight | Классическая виртуальная сеть | Виртуальная сеть Resource Manager |
    | ---- | ---- | ---- |
    | Linux | Нет | Да |
    | Windows | Да | Нет |

    Чтобы получить доступ к ресурсам в несовместимой виртуальной сети, объедините эти две сети. Дополнительные сведения о подключении классических виртуальных сетей и виртуальных сетей Resource Manager см. в статье [Создание подключения между классическими и новыми виртуальными сетями](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

* __Пользовательский DNS-сервер__. Azure предоставляет разрешение имен для служб Azure, установленных в виртуальной сети Azure. Это разрешение имен не распространяется за пределами виртуальной сети. Чтобы включить разрешение имен для ресурсов, находящихся за пределами виртуальной сети, необходимо использовать пользовательский DNS-сервер. Дополнительные сведения об использовании пользовательских DNS-серверов см. в статье [Разрешение имен для виртуальных машин и экземпляров ролей](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

* __Принудительное туннелирование__. HDInsight не поддерживает настройку принудительного туннелирования в виртуальной сети Azure.

* __Ограничение сетевого трафика__. HDInsight поддерживает использование групп безопасности сети для ограничения сетевого трафика, но требует неограниченного доступа к нескольким IP-адресам Azure. Дополнительные сведения см. в разделе [Защищенные виртуальные сети](#secured-virtual-networks).

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>Объединение облачных ресурсов в частной сети (только для облачных решений)

![Диаграмма конфигурации только для облачных решений](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

При использовании виртуальной сети для привязки служб Azure к Azure HDInsight можно реализовать следующие сценарии.

* **Вызов служб или заданий HDInsight** с веб-сайтов Azure или из служб, работающих в виртуальных машинах Azure.

* **Непосредственная передача данных** между HDInsight и базой данных SQL Azure, SQL Server или другими решениями для хранения данных в виртуальной машине.

* **Объединение нескольких серверов HDInsight** в единое решение. Существует несколько типов кластеров HDInsight, которые соответствуют рабочей нагрузке или технологии, для которой настроен кластер. Создать кластер, в котором бы объединились несколько типов, например Storm и HBase, нельзя. Использование виртуальной сети позволяет нескольким кластерам напрямую взаимодействовать друг с другом.

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>Подключение облачных ресурсов к сети локального центра обработки данных

Конфигурация "сеть-сеть" позволяет соединить несколько ресурсов в центре обработки данных с виртуальной сетью Azure. Для подключения можно использовать VPN-устройство или службу маршрутизации и удаленного доступа.

![Диаграмма конфигурации соединения "сервер-сервер"](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

Конфигурация «точка — сеть» позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

![Диаграмма конфигурации соединения "клиент-сервер"](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

Использование виртуальной сети для связи облака и центра обработки данных позволяет реализовать подобные сценарии в конфигурации только облака. Но при этом помимо работы с ресурсами в облаке вы также получаете возможность работы с ресурсами в центре обработки данных.

* **Непосредственная передача данных** между HDInsight и центром обработки данных. Например, использование Sqoop для передачи данных на SQL Server либо из него или чтение данных, созданных бизнес-приложением.

* **Вызов служб или заданий HDInsight** из бизнес-приложения. Например, использование API HBase Java для хранения и извлечения данных из кластера HDInsight HBase.

Дополнительные сведения о характеристиках, преимуществах и возможностях виртуальной сети см. в статье [Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Создайте виртуальную сеть Azure перед подготовкой кластера HDInsight, затем укажите эту сеть при создании кластера. Более подробную информацию можно найти в разделе [Настройка задач виртуальной сети](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="secured-virtual-networks"></a>Защищенные виртуальные сети

Служба HDInsight является управляемой службой, и при ее подготовке и работе ей требуется доступ к службам управления Azure. Службы управления Azure выполняют следующие действия:

* Мониторинг работоспособности кластера
* Запуск отработки отказа для ресурсов кластера
* Изменение количества узлов в кластере с помощью операций масштабирования
* Другие задачи управления

> [!NOTE]
> Эти операции не требуют полного доступа к Интернету. При ограничении доступа к Интернету разрешите входящий трафик через порт 443 для следующих IP-адресов. Это позволит Azure управлять HDInsight:

IP-адреса, которые необходимо разрешить, зависят от региона, в котором находится кластер HDInsight и виртуальная сеть. Используйте приведенную ниже таблицу, чтобы найти IP-адреса для необходимого региона.

| Страна | Регион | Разрешенные IP-адреса | Разрешенный порт |
| ---- | ---- | ---- | ---- |
| Бразилия | Южная часть Бразилии | 191.235.84.104</br>191.235.87.113 | 443 |
| Канада | Восточная Канада | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | Центральная Канада | 52.228.37.66</br>52.228.45.222 | 443 |
| Индия | Центральная Индия | 52.172.153.209</br>52.172.152.49 | 443 |
| Великобритания | Западная часть Великобритании | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | Южная часть Великобритании | 51.140.47.39</br>51.140.52.16 | 443 |
| США | Западно-центральная часть США | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | Западный регион США 2 | 52.175.211.210</br>52.175.222.222 | 443 |

__Если вашего региона нет в таблице__, то разрешите трафик на порт __443__ по следующим IP адресам:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight не поддерживает ограничение исходящего трафика, ограничить можно только входящий трафик. При определении правил группы безопасности сети для подсети, содержащей HDInsight, __используйте только правила для входящего трафика__.

### <a name="working-with-hdinsight-in-secured-virtual-networks"></a>Работа с HDInsight в защищенных виртуальных сетях

Если заблокировать доступ к Интернету, то вы не сможете использовать службы HDInsight, которые обычно предоставляются кластеру через общедоступный шлюз. Это относится к Ambari и SSH. Вместо этого необходимо получить доступ к службам с помощью внутреннего IP-адреса головных узлов кластера.

Чтобы определить внутренний IP-адрес головных узлов, используйте сценарии, приведенные в разделе [Получение внутренних IP-адресов и полных доменных имен](#internal-ips-and-fqdns).

### <a name="example-secured-virtual-network"></a>Пример: защищенная виртуальная сеть

В следующих примерах показано, как создать группу безопасности сети, в которой разрешен входящий трафик через порт 443 из таких IP-адресов:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> Эти адреса предназначены для регионов, для которых в списке не указаны определенные IP-адреса. Чтобы найти IP-адреса для своего региона, используйте сведения в разделе [Защищенные виртуальные сети](#secured-virtual-networks).

Предполагается, что вы уже создали виртуальную сеть и подсеть, в которую хотите установить HDInsight. Ознакомьтесь со статьей [Создание виртуальной сети с помощью портала Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!WARNING]
> Правила сетевого трафика проверяются в порядке __приоритета__. Когда правило соответствует определенным условиям, оно применяется, а проверка правил для данного запроса останавливается. Если у вас есть правило, блокирующее входящий трафик (например, **Запретить все**), то оно __должно__ следовать после правила, разрешающего трафик.
>
> Дополнительные сведения о правилах групп безопасности сети см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/virtual-networks-nsg.md).

**Пример: шаблон Azure Resource Manager**

Используйте приведенный ниже шаблон Resource Manager из [коллекции шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/), чтобы создать кластер HDInsight в виртуальной сети с конфигурациями защищенных сетей.

[Развертывание защищенной виртуальной сети Azure и кластера HDInsight Hadoop в виртуальной сети](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**Пример: Azure PowerShell**

```powershell
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
# Create a Network Security Group.
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
```

**Пример: Azure CLI**

1. Используйте следующую команду, чтобы создать новую группу безопасности сети с именем `hdisecure`. Замените значение параметра **RESOURCEGROUPNAME** на имя группы ресурсов, содержащей виртуальную сеть Azure. Замените значение параметра **LOCATION** на расположение (регион), в котором была создана группа.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    После создания группы вы получите сведения о новой группе.

2. Используйте следующую команду для добавления правил в новую группу безопасности сети. Эти правила разрешают входящий трафик через порт 443 от службы работоспособности и управления Azure HDInsight. Замените значение **RESOURCEGROUPNAME** именем группы ресурсов, содержащей виртуальную сеть Azure.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. После создания правил используйте следующую команду, чтобы получить уникальный идентификатор для этой группы безопасности сети:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Эта команда возвращает значение следующего вида:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Если вы не получили ожидаемых результатов, заключите идентификатор в команде в двойные кавычки.

4. Используйте следующую команду, чтобы применить группу безопасности сети к подсети. Замените __GUID__ и __RESOURCEGROUPNAME__ значениями, возвращенными на предыдущем шаге. Замените __VNETNAME__ и __SUBNETNAME__ именами виртуальной сети и подсети, которые необходимо использовать при создании кластера HDInsight.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    После выполнения этой команды можно успешно установить HDInsight в защищенной виртуальной сети в подсети, используемой при выполнении этих действий.

> [!IMPORTANT]
> Указанные выше действия предоставляют доступ только к службе работоспособности и управления HDInsight в облачной службе Azure. Весь остальной доступ к кластеру HDInsight из-за пределов виртуальной сети блокируется. Чтобы разрешить доступ из-за пределов виртуальной сети, необходимо добавить дополнительные правила группы безопасности сети.
>
> В следующем примере показано, как включить доступ по SSH через Интернет:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

Дополнительные сведения о группах безопасности сети см. в статье [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md). Сведения об управлении маршрутизацией в виртуальной сети Azure см. в статье [Что такое определяемые пользователем маршруты и IP-пересылка?](../virtual-network/virtual-networks-udr-overview.md).

## <a name="retrieve-internal-ips-and-fqdns"></a>Получение внутренних IP-адресов и полных доменных имен

При подключении к HDInsight с помощью виртуальной сети можно подключиться непосредственно к узлам в кластере. Чтобы определить внутренний IP-адрес и полные доменные имена (FQDN) для узлов в кластере, используйте следующие сценарии:

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

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

__Интерфейс командной строки Azure__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> В примере для Azure CLI 2.0 замените значение `<resourcegroupname>` именем группы ресурсов, содержащей виртуальную сеть.

Сценарии используют запросы к виртуальным сетевым картам (NIC) кластера. Сетевые карты существуют в группе ресурсов, которая содержит виртуальную сеть, используемую HDInsight.

## <a id="nextsteps"></a>Дальнейшие действия

В следующих примерах демонстрируется, как использовать HDInsight в виртуальной сети Azure.

* [Создание кластеров HBase в виртуальной сети Azure](hdinsight-hbase-provision-vnet.md)

* [Анализ полученных с датчиков данных с использованием Apache Storm, концентратора событий и базы данных HBase в службе HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md)

* [Создание кластеров Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

* [Выполнение заданий Sqoop с помощью Azure PowerShell для Hadoop в HDInsight](hdinsight-use-sqoop-mac-linux.md)

Дополнительные сведения о виртуальных сетях Azure см. в статье [Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md).



---
title: "Подключение к Kafka HDInsight с помощью виртуальных сетей Azure | Документация Майкрософт"
description: "Узнайте, как удаленно подключиться к Kafka HDInsight с помощью клиента kafka-python. Конфигурация, описанная в этом документе, использует HDInsight в виртуальной сети Azure. Удаленный клиент подключается к виртуальной сети через VPN-шлюз типа &quot;точка — сеть&quot;."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 5da21de1f7b51a6ca38a3fe443531bab4091dd60
ms.lasthandoff: 04/20/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>Подключение к Kafka HDInsight (предварительная версия) с помощью виртуальной сети Azure

Узнайте, как подключиться к Kafka HDInsight с помощью виртуальных сетей Azure. Клиенты Kafka (производители и потребители) могут запускаться непосредственно в HDInsight или в удаленных системах. Удаленные клиенты должны подключаться к Kafka HDInsight с помощью виртуальной сети Azure. В этом документе описывается, как с помощью виртуальных сетей Azure подключить удаленные клиенты к HDInsight.

> [!IMPORTANT]
> Некоторые конфигурации, описанные в этом документе, можно использовать с клиентами Windows, macOS или Linux. Однако пример, использующий подключение типа "точка — сеть", предоставляет только VPN-клиент для Windows.
>
> В этом примере также используется клиент Python ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) для проверки обмена данными с Kafka HDInsight.

## <a name="architecture-and-planning"></a>Архитектура и планирование

Кластеры HDInsight защищены внутри виртуальной сети Azure, разрешая только входящий трафик SSH и HTTPS. Трафик поступает через общедоступный шлюз, который не маршрутизирует трафик от клиентов Kafka. Для доступа к Kafka из удаленного клиента необходимо создать виртуальную сеть Azure, предоставляющую VPN-шлюз (шлюз виртуальной частной сети). После настройки виртуальной сети и шлюза установите в виртуальной сети службу HDInsight и подключитесь к ней с помощью VPN-шлюза.

![Схема, демонстрирующая HDInsight в виртуальной сети Azure с клиентом, подключенным через VPN](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

Ниже описан процесс использования Kafka HDInsight с помощью виртуальной сети.

1. Создайте виртуальную сеть. Подробные сведения об использовании HDInsight c виртуальными сетями Azure см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md).

2. (Необязательно). Создайте в виртуальной сети виртуальную машину Azure и установите на ней пользовательский DNS-сервер. Этот DNS-сервер используется для разрешения имен удаленных клиентов в конфигурациях типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть". Дополнительные сведения см. в статье [Разрешение имен для виртуальных машин и экземпляров ролей](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

3. Создайте VPN-шлюз для виртуальной сети. Дополнительные сведения о конфигурациях VPN-шлюза см. в статье [Основные сведения о VPN-шлюзах Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

4. Создайте службу HDInsight в виртуальной сети. Если настроить для сети пользовательский DNS-сервер, то HDInsight автоматически настраивается на его использование.

5. (Необязательно). Если не настроить пользовательский DNS-сервер и не использовать разрешение имен между клиентами и виртуальной сетью, то необходимо настроить Kafka для объявления IP-адресов. Дополнительные сведения см. в разделе [Настройка Kafka для объявления IP-адресов](#configure-kafka-for-ip-advertising) этой статьи.

## <a name="create-using-powershell"></a>Создание с помощью PowerShell

В этом разделе описаны действия по созданию следующей конфигурации с помощью [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/):

* Виртуальная сеть Azure
* VPN-шлюз типа "точка — сеть"
* Учетная запись хранения Azure (используется HDInsight)
* Kafka HDInsight

1. Чтобы создать сертификаты, необходимые для шлюза, выполните действия, описанные в статье [Создание самозаверяющего корневого сертификата для подключений типа "точка — сеть" с помощью PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

2. Откройте командную строку PowerShell и используйте следующий код, чтобы войти в подписку Azure:

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Используйте следующий код, чтобы создать переменные, которые содержат сведения о конфигурации:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. Используйте следующий код, чтобы создать группу ресурсов и виртуальную сеть Azure:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > Этот процесс может занять несколько минут.

5. Используйте следующий код, чтобы создать учетную запись хранения Azure и контейнер BLOB-объектов:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Используйте следующий код, чтобы создать кластер HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > Эта процедура занимает около 20 минут.

8. Используйте следующий командлет, чтобы получить URL-адрес VPN-клиента Windows для виртуальной сети:

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Чтобы скачать VPN-клиент Windows, используйте возвращаемый универсальный код ресурса (URI) в веб-браузере.

## <a name="configure-kafka-for-ip-advertising"></a>Настройка Kafka для объявления IP-адресов

По умолчанию Zookeeper возвращает клиентам доменное имя брокеров Kafka. Эта конфигурация не работает для VPN-клиента, так как он не может использовать разрешение имен для сущностей в виртуальной сети. Выполните следующие действия, что настроить Kafka HDInsight для объявления IP-адресов вместо доменных имен:

1. С помощью веб-браузера перейдите по адресу: https://CLUSTERNAME.azurehdinsight.net. Замените __CLUSTERNAME__ именем кластера Kafka HDInsight.

    При появлении запроса введите имя пользователя и пароль HTTPS для кластера. Отобразится веб-интерфейс Ambari для кластера.

2. Чтобы просмотреть сведения о Kafka, из списка слева выберите __Kafka__. 

    ![Список служб с выделенной службой Kafka](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Чтобы просмотреть конфигурацию Kafka, выберите пункт __Configs__ (Конфигурации) в верхней части окна.

    ![Ссылка на пункт "Configs" (Конфигурации) для Kafka](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Чтобы найти конфигурацию __kafka-env__, введите `kafka-env` в поле __фильтра__ в правом верхнем углу.

    ![Конфигурация Kafka, kafka-env](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Чтобы настроить Kafka для объявления IP-адресов, добавьте следующий текст в нижнюю часть поля __kafka-env template__ (шаблон kafka-env):

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Чтобы настроить интерфейс, через который Kafka ожидает передачи данных, введите `listeners` в поле __фильтра__ в правом верхнем углу.

7. Чтобы настроить Kafka для ожидания передачи данных через все сетевые интерфейсы, измените значение в поле __listeners__ на `PLAINTEXT://0.0.0.0:92092`.

8. Нажмите кнопку __Save__ (Сохранить), чтобы сохранить изменения в конфигурации. Введите текст, описывающий изменения. После сохранения изменений нажмите кнопку __ОК__.

    ![Кнопка сохранения конфигурации](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. Для предотвращения ошибок при перезапуске Kafka нажмите кнопку __Service Actions__ (Действия со службой) и выберите __Turn On Maintenance Mode__ (Включить режим обслуживания). Чтобы завершить эту операцию, нажмите кнопку "ОК".

    ![Кнопка "Service Actions" (Действия со службой) с выделенной командой "Turn On Maintenance Mode" (Включить режим обслуживания)](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Чтобы перезапустить Kafka, нажмите кнопку __Restart__ (Перезапустить) и выберите __Restart All Affected__ (Перезапустить все затронутые). Подтвердите перезапуск, а после завершения операции нажмите кнопку __ОК__.

    ![Кнопка "Restart" (Перезапустить) с выделенной командой "Restart All Affected" (Перезапустить все затронутые)](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. Чтобы отключить режим обслуживания нажмите кнопку __Service Actions__ (Действия со службой) и выберите __Turn Off Maintenance Mode__ (Отключить режим обслуживания). Чтобы завершить эту операцию, нажмите кнопку **ОК**.

## <a name="connect-to-the-vpn-gateway"></a>Подключение к VPN-шлюзу

Для подключения к VPN-шлюзу из __клиента Windows__ используйте раздел __Подключение к Azure__ статьи [Настройка подключения типа "точка — сеть" к виртуальной сети с помощью PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameconnectapart-7---connect-to-azure).

## <a name="remote-kafka-client"></a>Удаленный клиент Kafka

Чтобы подключиться к Kafka с клиентского компьютера, необходимо использовать IP-адреса брокеров Kafka или узлы Zookeeper (зависит от того, что требуется клиенту). Выполните следующие действия для получения IP-адресов брокеров Kafka, а затем используйте их из приложения Python.

1. Чтобы получить IP-адреса узлов в кластере, используйте следующий сценарий:

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    В этом сценарии предполагается, что `$resourceGroupName` — это имя группы ресурсов Azure, содержащей виртуальную сеть. После выполнения сценария вы должны увидеть результат, аналогичный приведенному ниже.

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > Если клиент Kafka использует узлы Zookeeper вместо брокеров Kafka, то в сценарии PowerShell необходимо заменить `*workernode*` на `*zookeepernode*`.

    > [!WARNING]
    > При масштабировании кластера или в случае сбоя и замены узлов IP-адреса могут измениться. В настоящее время невозможно заранее назначить узлам в кластере HDInsight определенные IP-адреса.

2. Чтобы установить клиент [kafka-python](http://kafka-python.readthedocs.io/), используйте следующую команду:

        pip install kafka-python

3. Чтобы отправить данные в Kafka, используйте следующий код Python:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Замените записи `'ip_address'` адресами, полученными на шаге 1 этого раздела.
    
    > [!NOTE]
    > Этот код отправляет строку `test message` в раздел `testtopic`. По умолчанию Kafka HDInsight создает раздел, если он не существует.

4. Для получения сообщений от Kafka используйте следующий код Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Замените записи `'ip_address'` адресами, полученными на шаге 1 этого раздела. Выходные данные содержат тестовое сообщение, отправленное в производитель на предыдущем шаге.

## <a name="troubleshooting"></a>Устранение неполадок

При возникновении проблем с подключением к виртуальной сети или к HDInsight с помощью сети ознакомьтесь со статьей [Устранение неполадок шлюза виртуальной сети и подключений с помощью Наблюдателя за сетями Azure и PowerShell](../network-watcher/network-watcher-troubleshoot-manage-powershell.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о создании виртуальной сети Azure с VPN-шлюзом типа "точка — сеть" см. в следующих документах:

* [Настройка подключения типа "точка — сеть" к виртуальной сети с помощью портала Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Настройка подключения типа "точка — сеть" к виртуальной сети с помощью PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Дополнительные сведения о работе с Kafka HDInsight см. в следующих документах:

* [Get started with Apache Kafka on HDInsight (preview)](hdinsight-apache-kafka-get-started.md) (Приступая к работе с Apache Kafka в HDInsight (предварительная версия))
* [Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия)](hdinsight-apache-kafka-mirroring.md)


---
title: Настройка репликации HBase между двумя центрами обработки данных | Microsoft Docs
description: Узнайте, как настроить репликации HBase между двумя центрами обработки данных и о вариантах использования репликации кластера.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jgao

---
# Настройка георепликации HBase в HDInsight
> [!div class="op_single_selector"]
> * [Настройка подключения VPN](../hdinsight-hbase-geo-replication-configure-VNETs.md)
> * [Настройка DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
> * [Настройка репликации HBase](hdinsight-hbase-geo-replication.md)
> 
> 

Узнайте, как настроить репликацию HBase между двумя центрами обработки данных. Некоторые варианты использования репликации кластера:

* Резервное копирование и аварийное восстановление
* Агрегация данных
* Распределение географических данных
* Прием данных в сети в сочетании с автономной аналитикой данных

Репликация кластера использует методологию source-push. Кластер HBase может быть источником, назначением, или выполнять обе роли одновременно. Репликация выполняется асинхронно, а целью репликации в конечном итоге является согласованность. При получении источником изменения в семействе столбцов с включенной репликацией такое изменение распространяется на все кластеры назначения. При репликации данных с одного кластера на другой исходный кластер и все кластеры, которые уже потребили данные, отслеживаются для предотвращения циклических репликаций. Дополнительные сведения. В этом учебнике предстоит настроить репликацию источник-назначение. Другие топологии кластеров см. в документе [Справочное руководство по Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Это третья часть серии:

* [Настройка VPN-подключения между двумя виртуальными сетями][hdinsight-hbase-replication-vnet]
* [Настройка DNS для виртуальных сетей][hdinsight-hbase-replication-dns]
* Настройка георепликации HBase (данный учебник)

На следующей схеме показаны две виртуальные сети и сетевое подключение, созданные вами в разделах [Настройка VPN-подключения между двумя виртуальными сетями][hdinsight-hbase-geo-replication-vnet] и [Настройка DNS для виртуальных сетей][hdinsight-hbase-replication-dns]\:

![Схема виртуальной сети репликации HBase в HDInsight][img-vnet-diagram]

## <a id="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Рабочая станция с Azure PowerShell.**.
  
    Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел Использование командлета Set-ExecutionPolicy.
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
* **Две виртуальные сети Azure с настроенными VPN-подключением и службой DNS**. Инструкции см. в разделах [Настройка VPN-подключения между двумя виртуальными сетями Azure][hdinsight-hbase-replication-vnet] и [Настройка DNS между двумя виртуальными сетями Azure][hdinsight-hbase-replication-dns].

    Перед выполнением сценариев PowerShell убедитесь, что вы подключены к подписке Azure, с помощью следующего командлета.

        Add-AzureAccount

    При наличии нескольких подписок Azure используется следующий командлет для установки текущей подписки:

        Select-AzureSubscription <AzureSubscriptionName>



## Подготовка кластеров HBase в HDInsight
В разделе [Настройка VPN-подключения между двумя виртуальными сетями Azure][hdinsight-hbase-replication-vnet] вы создали виртуальную сеть в европейском центре обработки данных и виртуальную сеть в центре обработки данных, расположенном в США. Две виртуальные сети соединены через VPN. На этом занятии вы подготовите кластер HBase в каждой из виртуальных сетей. Далее в этом учебнике вы настроите репликацию одним из этих кластеров другого.

Классический портал Azure не поддерживает подготовку кластеров HDInsight с пользовательскими параметрами конфигурации. Например, с установкой для *hbase.replication* значения *true*. Если задать значение в файле конфигурации после подготовки кластера, настройка будет потеряна после повторного создания образа кластера. Дополнительные сведения см. в разделе [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision]. Один из вариантов подготовки кластера HDInsight с пользовательскими параметрами — использование Azure PowerShell.

**Подготовка кластера HBase в Contoso-VNet-EU**

1. На рабочей станции откройте Windows PowerShell ISE.
2. Задайте переменные в начале сценария и выполните сценарий.
   
        # create hbase cluster with replication enabled
   
        $azureSubscriptionName = "[AzureSubscriptionName]"
   
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
   
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
   
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
   
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
   
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
   
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
   
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
   
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
   
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
   
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
   
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
   
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential

**Подготовка кластера HBase в Contoso-VNet-US**

* Используйте тот же сценарий со следующими значениями:
  
        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'    
  
    Поскольку вы уже подключились к своей учетной записи Azure, выполнять следующие командлеты больше не требуется:
  
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName

## Настройка DNS-сервера условной пересылки
В разделе [Настройка DNS для виртуальных сетей][hdinsight-hbase-replication-dns] вы настроили DNS-серверы для двух сетей. Кластеры HBase имеют разные суффиксы доменов. Таким образом, требуется настроить дополнительные DNS-серверы условной пересылки.

Чтобы настроить сервер условной пересылки, необходимо знать суффиксы доменов двух кластеров HBase.

**Поиск суффиксов доменов двух кластеров HBase**

1. Выполните вход RDP в **Contoso-HBase-EU**. Для получения инструкций см. статью [Управление кластерами Hadoop в HDInsight с использованием классического портала Azure][hdinsight-manage-portal]. Это фактически headnode0 кластера.
2. Откройте консоль Windows PowerShell или командную строку.
3. Запустите **ipconfig** и запишите **DNS-суффикс, зависящий от подключения**.
4. Не закрывайте сеанс RDP. Он потребуется позже для проверки разрешения имени домена.
5. Повторите шаги, чтобы узнать **DNS-суффикс, зависящий от подключения** для **Contoso-HBase-US**.

**Настройка DNS-серверов пересылки**

1. Выполните вход RDP в **Contoso-DNS-EU**.
2. Нажмите клавишу Windows в левом нижнем углу.
3. Щелкните **Администрирование**.
4. Щелкните **DNS**.
5. В левой области разверните **DSN**, **Contoso-DNS-EU**.
6. Щелкните правой кнопкой мыши **Серверы условной пересылки** и выберите команду **Создать сервер условной пересылки**.
7. Введите следующие сведения:
   * **DNS-домен**: введите DNS-суффикс Contoso-HBase-US. Например: Contoso-HBase-US.f5.internal.cloudapp.net.
   * **IP-адреса главных серверов**: введите 10.2.0.4 (IP-адрес Contoso-DNS-US). Проверьте правильность IP-адреса. DNS-сервер может иметь другой IP-адрес.
8. Нажмите клавишу **ENTER**, а затем нажмите кнопку **ОК**. Теперь можно будет разрешить IP-адрес Contoso-DNS-US из Contoso-DNS-EU.
9. Повторите шаги, чтобы добавить DNS-сервер условной пересылки в службу DNS на виртуальной машине Contoso-DNS-US со следующими значениями:
   * **DNS-домен**: введите DNS-суффикс Contoso-HBase-EU.
   * **IP-адреса главных серверов**: введите 10.2.0.4 (IP-адрес Contoso-DNS-EU).

**Проверка разрешения имени домена**

1. Переключитесь на окно RDP Contoso-HBase-EU.
2. Откройте окно командной строки.
3. Выполните команду ping:
   
        ping headnode0.[DNS suffix of Contoso-HBase-US]
   
    Протокол ICM включен на рабочих узлах кластеров HBase
4. Не закрывайте сеанс RDP. Он все еще потребуется позже в данном руководстве.
5. Повторите те же действия для проверки headnode0 Contoso-HBase-EU с помощью команды ping из Contoso-HBase-US.

> [!IMPORTANT]
> Служба DNS должна работать, прежде чем можно будет перейти к дальнейшим действиям.
> 
> 

## Включение репликации между таблицами HBase
Теперь можно создать образец таблицы HBase, включить репликацию и затем протестировать ее с данными. Образец таблицы, который будет использоваться, содержит два семейства столбцов: Personal и Office.

В этом учебнике европейский кластер HBase (EU) будет служить исходным кластером, а американский (US) — кластером назначения.

Создайте таблицы HBase с одинаковыми именами и семействами столбцов как на исходном кластере, так и на кластере назначения, чтобы кластер назначения знал, где хранить получаемые данные. Дополнительные сведения об использовании оболочки HBase см. в разделе [Начало работы с Apache HBase в HDInsight][hdinsight-hbase-get-started].

**Создание таблицы HBase на Contoso-HBase-EU**

1. Переключитесь на окно RDP **Contoso-HBase-EU**.
2. На рабочем столе щелкните **Командная строка Hadoop**.
3. Измените папку на домашнюю папку HBase:
   
        cd %HBASE_HOME%\bin
4. Откройте оболочку HBase:
   
        hbase shell
5. Создайте таблицу HBase:
   
        create 'Contacts', 'Personal', 'Office'
6. Не закрывайте ни сеанс RDP, ни окно командной строки Hadoop. Они все еще потребуются позже в данном руководстве.

**Создание таблицы HBase на Contoso-HBase-US**

* Повторите те же действия для создания такой же таблицы на Contoso-HBase-US.

**Добавление Contoso-HBase-US в качестве однорангового узла репликации**

1. Переключитесь на окно RDP **Contoso-HBase-EU**.
2. Из окна оболочки HBase добавьте кластер назначения (Contoso-HBase-US) в качестве однорангового узла, например:
   
        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'
   
    В данном примере суффикс домена — *contoso-hbase-us.d4.internal.cloudapp.net*. Необходимо обновить его для соответствия суффиксу домена американского кластера HBase. Убедитесь в отсутствии пробелов между именами хостов.

**Настройка каждого из семейств столбцов для репликации на исходном кластере**

1. Из окна оболочки HBase RDP-сеанса **Contso-HBase-EU** настройте каждое семейство столбцов для репликации:
   
        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Массовая передача данных в таблицу HBase**

Образец файла данных был передан в открытый контейнер больших двоичных объектов Azure со следующим URL-адресом:

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Содержимое файла:

        8396    Calvin Raji    230-555-0191    5415 San Gabriel Dr.
        16600    Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891    Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller    397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272    Julia Lee    870-555-0110    3148 Rose Street
        4868    Jose Hayes    599-555-0171    793 Crawford Street
        4761    Caleb Alexander    670-555-0141    4775 Kentucky Dr.
        16443    Terry Chander    998-555-0171    771 Northridge Drive

Такой же файл данных можно передать в кластер HBase и импортировать данные из него.

1. Переключитесь на окно RDP **Contoso-HBase-EU**.
2. На рабочем столе щелкните **Командная строка Hadoop**.
3. Измените папку на домашнюю папку HBase:
   
        cd %HBASE_HOME%\bin
4. передача данных:
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Убедитесь, что выполняется репликация данных
Проверить наличие репликации можно путем сканирования таблиц из обоих кластеров с помощью следующих команд оболочки HBase:

        Scan 'Contacts'


## Дальнейшие действия
В этом учебнике вы изучили настройку репликации HBase между двумя центрами обработки данных. Для получения дополнительных сведений о HDInsight и HBase см. следующие ресурсы:

* [Страница службы HDInsight](https://azure.microsoft.com/services/hdinsight/)
* [Документация по HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Начало работы с Apache HBase в HDInsight][hdinsight-hbase-get-started]
* [Обзор HDInsight HBase][hdinsight-hbase-overview]
* [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]
* [Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase][hdinsight-hbase-twitter-sentiment]
* [Анализ данных датчиков с помощью Storm и HBase в HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

<!---HONumber=AcomDC_0914_2016-->
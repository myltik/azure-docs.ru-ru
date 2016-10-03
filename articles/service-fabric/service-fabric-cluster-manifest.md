<properties
   pageTitle="Настройка автономного кластера | Microsoft Azure"
   description="В этой статье описывается настройка автономного или частного кластера Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="dkshir"/>


# Параметры конфигурации для автономного кластера Windows

В этой статье описывается настройка автономного кластера Service Fabric с помощью файла _**ClusterConfig.JSON**_. Этот файл скачивается на ваш рабочий компьютер при [скачивании автономного пакета Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage). Файл ClusterConfig.JSON позволяет указать для кластера Service Fabric сведения об узлах Service Fabric и их IP-адресах, о различных типах узлов кластера, настройках безопасности, а также о топологии сети с точки зрения доменов сбоя и доменов обновления.

Ниже мы рассмотрим различные разделы этого файла.

## Общие параметры кластера
Сюда входит целый ряд параметров кластера, как показано в следующем фрагменте кода JSON.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

Для кластера Service Fabric можно задать любое понятное имя, присвоив его переменной **name**. Можно изменить **clusterManifestVersion** в соответствии с вашей конфигурацией. Это следует сделать перед обновлением конфигурации Service Fabric. Для переменной **apiVersion** можно оставить значение по умолчанию.


<a id="clusternodes"></a>
## Узлы в кластере
Можно настроить узлы в кластере Service Fabric с помощью раздела **nodes**, как показано в следующем фрагменте кода.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

У кластера Service Fabric должно быть не менее 3 узлов. Можно добавить дополнительные узлы в этот разделе в соответствии с вашей конфигурацией. В следующей таблице описаны параметры конфигурации для каждого узла.

|**Параметр узла**|**Описание**|
|-----------------------|--------------------------|
|nodeName|Узлу можно присвоить любое понятное имя.|
|iPAddress|Узнать IP-адрес узла можно, открыв командное окно и введя `ipconfig`. Запишите этот IPv4-адрес и назначьте его переменной **iPAddress**.|
|nodeTypeRef|Узлам можно назначить разные типы узла. [Типы узлов](#nodetypes) определены в следующем разделе.|
|faultDomain|Домены сбоя позволяют администраторам кластера определять физические узлы, на которых из-за наличия общих физических зависимостей могут одновременно возникать сбои.|
|upgradeDomain|Домены обновления описывают наборы узлов, которые почти одновременно завершают работу, чтобы выполнить обновления Service Fabric. Так как они не ограничены какими-либо физическими требованиями, вы можете выбрать узлы, которые следует назначить тем или иным доменам обновления.| 


## **Свойства** кластера

Раздел **properties** в файле ClusterConfig.JSON используется для настройки кластера следующим образом.

### **diagnosticsStore**
В разделе **diagnosticsStore** можно настроить параметры, чтобы включить диагностику и устранение неполадок узлов и кластера, как показано в следующем фрагменте кода.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Переменная **metadata** представляет собой описание диагностики кластера и может быть задана в соответствии с вашей конфигурацией. Эти переменные помогают собирать журналы трассировки событий Windows, аварийные дампы и данные счетчиков производительности. Прочитайте статьи [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) и [Трассировка событий Windows](https://msdn.microsoft.com/library/ms751538.aspx), чтобы больше узнать о журналах трассировки событий Windows. Все журналы, в том числе [аварийные дампы](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) и данные [счетчиков производительности](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), можно перенаправить в папку **perfCtrConnectionString** на вашем компьютере. Для хранения данных диагностики можно также использовать **AzureStorage**. Ниже приведен фрагмент кода.

	"diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### **security** 
Раздел **security** необходим для защиты автономного кластера Service Fabric. В следующем фрагменте кода показана часть этого раздела.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
		. . .
	}

Переменная **metadata** представляет собой описание защищенного кластера и может быть задана в соответствии с вашей конфигурацией. Переменные **ClusterCredentialType** и **ServerCredentialType** определяют тип безопасности, реализуемой кластером и узлами. Можно задать значение *X509* для защиты на основе сертификатов или *Windows* для защиты на основе Azure Active Directory. Остальная часть раздела **security** будет зависеть от выбранного типа безопасности. Прочитайте статью [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md) или [Защита автономного кластера под управлением Windows с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md), чтобы узнать, как заполнить остальную часть раздела **security**.

### **reliabilityLevel**
**ReliabilityLevel** определяет количество копий системных служб, которые могут выполняться на первичных узлах кластера. Это позволяет повысить надежность этих служб и, следовательно, кластера. Этой переменной можно задать значение *Bronze*, *Silver*, *Gold* или *Platinum*, чтобы разрешить выполнение 3, 5, 7 или 9 копий этих служб, соответственно. См. следующий пример.

	"reliabilityLevel": "Bronze",
	
Обратите внимание, что так как на первичном узле выполняется одна копия системных служб, вам потребуется как минимум 3 первичных узла для уровня надежности *Bronze*, 5 для уровня надежности *Silver*, 7 для уровня надежности *Gold* и 9 для уровня надежности *Platinum*.


<a id="nodetypes"></a>
### **nodeTypes**
В разделе **nodeTypes** описывается тип узлов в кластере. Для кластера нужно указать по крайней мере один тип узла, как показано в следующем фрагменте кода.

	"nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpoint": "19001",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
			"startPort": "20001",
            "endPort": "20031"
        },
        "ephemeralPorts": {
            "startPort": "20032",
            "endPort": "20062"
        },
        "isPrimary": true
    }]

**name** — понятное имя этого конкретного типа узла. Чтобы создать узел данного типа, потребуется назначить понятное имя этого типа переменной **nodeTypeRef** данного узла, как указано в разделе [Узлы в кластере](#clusternodes) выше. Для каждого типа узла можно определить различные конечные точки для подключения к этому кластеру. Для этих конечных точек подключения можно выбрать любой номер порта, если он не конфликтует с другими конечными точками в данном кластере. В кластере с несколькими типами узлов будет один тип первичного узла, для которого **isPrimary** имеет значение *true*. Для остальных узлов **isPrimary** будет иметь значение *false*. Прочитайте статью [Рекомендации по планированию загрузки кластера Service Fabric](service-fabric-cluster-capacity.md), чтобы больше узнать о значениях **nodeTypes** и **reliabilityLevel** и соответствующей емкости кластера, а также о том, в чем заключается разница между типами первичных и вторичных узлов.


### **fabricSettings**
В этом разделе можно задать корневые каталоги для данных и журналов Service Fabric. Их можно настроить только во время создания исходного кластера. Ниже приведен фрагмент кода из этого раздела.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\ProgramData\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\ProgramData\SF\Log"
    }]

Рекомендуется использовать несистемный диск (без ОС) в качестве FabricDataRoot и FabricLogRoot, так как он обеспечивает большую надежность и независимость от сбоев операционной системы. Обратите внимание, что если настроить только корневой каталог данных, то корневой каталог файлов журнала будет помещен на один уровень ниже корневого каталога данных.


## Дальнейшие действия

Завершив настройку файла ClusterConfig.JSON в соответствии с конфигурацией автономного кластера, вы можете развернуть этот кластер, следуя указаниям в статье [Создание кластера Azure Service Fabric в локальной системе или облаке](service-fabric-cluster-creation-for-windows-server.md), а затем перейти к [визуализации кластера с помощью Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!---HONumber=AcomDC_0921_2016-->
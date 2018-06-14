---
title: Настройка изолированного кластера Azure Service Fabric | Документация Майкрософт
description: Узнайте, как настраивать изолированный или локальный кластер Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e0fed608ac9dd02a6fe5563eefc30edb63d224b1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205371"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Параметры конфигурации для изолированного кластера Windows
В этой статье описана настройка изолированного кластера Azure Service Fabric с помощью файла ClusterConfig.json. Этот файл используется для указания сведений об узлах кластера, конфигурациях безопасности, а также топологии сети в плане доменов сбоя и обновления.

В [загружаемый изолированный пакет Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) также включены примеры ClusterConfig.json. Примеры с DevCluster в имени позволяют создать кластер с тремя узлами на одном и том же компьютере, используя логические узлы. По крайней мере один узел должен быть помечен как первичный. Этот тип кластера удобен для работы в среде разработки или тестирования. Но он не предназначен для использования в качестве рабочего кластера. Примеры с MultiMachine в имени позволяют создать кластер рабочего уровня, каждый узел которого размещен на отдельном компьютере. Число первичных узлов такого кластера основано на его [уровне надежности](#reliability). В выпуске 5.7 (версия API 05-2017) мы удалили свойство уровня надежности (reliability-level). Вместо этого код вычисляет наиболее оптимизированный уровень надежности для кластера. Не пытайтесь задать значение для этого свойства начиная с версии 5.7.


* В примерах ClusterConfig.Unsecure.DevCluster.json и ClusterConfig.Unsecure.MultiMachine.json показано, как создать незащищенный тестовый или рабочий кластер соответственно.

* В примерах ClusterConfig.Windows.DevCluster.json и ClusterConfig.Windows.MultiMachine.json показано, как создать тестовый или рабочий кластер, защищенный с помощью [системы безопасности Windows](service-fabric-windows-cluster-windows-security.md).

* В примерах ClusterConfig.X509.DevCluster.json и ClusterConfig.X509.MultiMachine.json показано, как создать тестовый или рабочий кластер, защищенный с помощью [системы безопасности на основе сертификатов X509](service-fabric-windows-cluster-x509-security.md).

Теперь рассмотрим различные разделы файла ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Общие параметры кластера
Общие параметры кластера охватывают обширные конфигурации конкретного кластера, как показано в следующем фрагменте кода JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Для кластера Service Fabric можно задать любое понятное имя, присвоив его переменной name. clusterConfigurationVersion — это номер версии кластера. Его следует увеличивать при каждом обновлении кластера Service Fabric. Оставьте для apiVersion значение по умолчанию.

## <a name="nodes-on-the-cluster"></a>Узлы в кластере

    <a id="clusternodes"></a>

Можно настроить узлы в кластере Service Fabric с помощью раздела nodes, как показано в следующем фрагменте кода:

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

Кластер Service Fabric должен содержать по крайней мере три узла. Можно добавить дополнительные узлы в этот раздел в соответствии с вашей конфигурацией. В следующей таблице описаны параметры конфигурации для каждого узла:

| **Параметр узла** | **Описание** |
| --- | --- |
| nodeName |Узлу можно присвоить любое понятное имя. |
| iPAddress |Узнать IP-адрес узла можно, открыв командное окно и введя `ipconfig`. Запишите этот IPv4-адрес и назначьте его переменной iPAddress. |
| nodeTypeRef |Узлам можно назначить разные типы узла. [Типы узлов](#node-types) определены в следующем разделе. |
| faultDomain |Домены сбоя позволяют администраторам кластера определять физические узлы, на которых из-за наличия общих физических зависимостей могут одновременно возникать сбои. |
| upgradeDomain |Домены обновления описывают наборы узлов, которые почти одновременно завершают работу, чтобы выполнить обновления Service Fabric. Так как они не ограничены какими-либо физическими требованиями, вы можете выбрать узлы, которые следует назначить тем или иным доменам обновления. |

## <a name="cluster-properties"></a>Свойства кластера
Раздел свойств в файле ClusterConfig.json используется для настройки кластера указанным ниже образом.

### <a name="reliability"></a>Надежность
Термин reliabilityLevel определяет количество реплик или экземпляров системных служб Service Fabric, которые могут выполняться на первичных узлах кластера. От этого зависит надежность этих служб и, следовательно, кластера. Значение вычисляется системой во время создания и обновления кластера.

    <a id="reliability"></a>

### <a name="diagnostics"></a>Диагностика
В разделе diagnosticsStore вы можете настроить параметры, чтобы включить диагностику и устранение неполадок узлов и кластера, как показано в следующем фрагменте кода: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Переменная metadata представляет собой описание диагностики кластера и может быть задана в соответствии с вашей конфигурацией. Эти переменные помогают собирать журналы трассировки событий Windows, аварийные дампы и данные счетчиков производительности. Дополнительные сведения о журналах трассировки событий Windows см. в статье [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) и [Трассировка событий Windows](https://msdn.microsoft.com/library/ms751538.aspx). Все журналы, в том числе [аварийные дампы](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) и данные [счетчиков производительности](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), можно перенаправить в папку connectionString на вашем компьютере. Службу хранилища Azure можно также использовать для хранения данных диагностики. См. следующий образец фрагмента кода:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Безопасность
Раздел security необходим для защиты изолированного кластера Service Fabric. В следующем фрагменте кода показана часть этого раздела:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Переменная metadata представляет собой описание защищенного кластера и может быть задана в соответствии с вашей конфигурацией. Переменные ClusterCredentialType и ServerCredentialType определяют тип безопасности, реализуемой кластером и узлами. Можно задать значение *X509* для защиты на основе сертификатов или *Windows* для защиты на основе Azure Active Directory. Остальная часть раздела security зависит от выбранного типа безопасности. Сведения о том, как заполнить остальную часть раздела security, см. в статьях [Защита автономного кластера под управлением Windows с помощью сертификатов X.509](service-fabric-windows-cluster-x509-security.md) или [Защита изолированного кластера под управлением Windows с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Типы узлов

    <a id="nodetypes"></a>

В разделе nodeTypes описывается тип узлов в кластере. Для кластера нужно указать по крайней мере один тип узла, как показано в следующем фрагменте кода: 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

name — понятное имя этого конкретного типа узла. Чтобы создать узел данного типа, присвойте его понятное имя переменной nodeTypeRef для этого узла, как [упоминалось раньше](#nodes-on-the-cluster). Для каждого типа узла определите конечные точки подключения, которые будут использоваться. Для этих конечных точек подключения можно выбрать любой номер порта, если он не конфликтует с другими конечными точками в данном кластере. В кластере с несколькими узлами будет один или несколько первичных узлов (т. е. isPrimary имеет значение *true*), в зависимости от значения [reliabilityLevel](#reliability). Сведения о первичных и непервичных типах узлов, в частности о параметрах nodeTypes и reliabilityLevel, см. в статье [Рекомендации по планированию загрузки кластера Service Fabric](service-fabric-cluster-capacity.md). 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Конечные точки, используемые для настройки типов узлов
* clientConnectionEndpointPort — порт, используемый клиентом для подключения к кластеру при использовании клиентских API. 
* clusterConnectionEndpointPort — порт, по которому узлы взаимодействуют друг с другом.
* leaseDriverEndpointPort — порт, используемый драйвером аренды кластера для определения того, активны ли еще узлы. 
* serviceConnectionEndpointPort — порт, используемый приложениями и службами, развернутыми на узле, для связи с клиентом Service Fabric на данном узле.
* httpGatewayEndpointPort — порт, используемый Service Fabric Explorer для подключения к кластеру.
* ephemeralPorts переопределяет [динамические порты, используемые операционной системой](https://support.microsoft.com/kb/929851). Service Fabric использует часть из них в качестве портов приложений, а остальные доступны для операционной системы. Кроме того, этот диапазон сопоставляется с существующим диапазоном в операционной системе, поэтому для любых целей вы можете использовать диапазоны, указанные в примерах файлов JSON. Убедитесь в том, что разница между номерами начального и конечного портов составляет не менее 255. Если это различие слишком мало, могут возникнуть конфликты, потому что этот диапазон также используется для ОС. Чтобы увидеть настроенный динамический диапазон портов, запустите `netsh int ipv4 show dynamicport tcp`.
* applicationPorts — порты, которые используются приложениями Service Fabric. Диапазон портов приложения должен быть достаточным для обеспечения потребностей приложений в конечных точках. Этот диапазон должен отличаться от диапазона динамических портов на компьютере, т. е. диапазона ephemeralPorts, указанного в конфигурации. Service Fabric использует их каждый раз, когда требуются новые порты, а также разрешает их использование в брандмауэре. 
* reverseProxyEndpointPort — это необязательная конечная точка обратного прокси-сервера. Дополнительные сведения см. в статье [Обратный прокси-сервер в Azure Service Fabric](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Параметры журнала
В разделе fabricSettings вы можете задать корневые каталоги для данных и журналов Service Fabric. Эти каталоги можно настроить только во время создания исходного кластера. Ниже приведен фрагмент кода этого раздела.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Рекомендуется использовать диск без ОС в качестве FabricDataRoot и FabricLogRoot. Он обеспечивает большую надежность и независимость от ситуаций, когда операционная система перестает отвечать на запросы. Если настроить только корневой каталог данных, то корневой каталог файлов журнала будет помещен на один уровень ниже корневого каталога данных.

### <a name="stateful-reliable-services-settings"></a>Параметры надежной службы с отслеживанием состояния
В разделе KtlLogger можно задать глобальные параметры конфигурации для Reliable Services. Дополнительные сведения об этих параметрах см. в статье [Настройка надежных служб с отслеживанием состояния](service-fabric-reliable-services-configuration.md). Следующий пример показывает, как изменить общий журнал транзакций, который создается для обслуживания Reliable Collections для служб с отслеживанием состояния:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Функции надстройки
Для настройки функций надстройки параметр apiVersion должен иметь значение 04-2017 или выше, а для параметра addonFeatures необходимо задать следующее:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Поддержка контейнеров
Чтобы включить в изолированных кластерах поддержку контейнеров для контейнеров Windows Server и Hyper-V, необходимо включить компонент надстройки DnsService.


## <a name="next-steps"></a>Дополнительная информация
Завершив настройку файла ClusterConfig.json в соответствии с конфигурацией изолированного кластера, вы можете развернуть этот кластер. Следуйте инструкциям в статье [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md), а затем перейдите к статье [Визуализация кластера с помощью Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).


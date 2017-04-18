---
title: "Настройка изолированного кластера Azure Service Fabric | Документация Майкрософт"
description: "Создание изолированного кластера разработки с тремя узлами, работающими на одном и том же компьютере. После выполнения этого шага вы сможете создавать кластеры с несколькими компьютерами."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6da8b21014966edd9f4cea0fd27f6973b2b820f0
ms.lasthandoff: 04/12/2017


---

# <a name="create-your-first-service-fabric-standalone-cluster"></a>Создание первого изолированного кластера Service Fabric
Вы можете создать изолированный кластер Service Fabric на локально или в облаке на виртуальных машинах или компьютерах под управлением Windows Server 2012 R2 или Windows Server 2016. Это краткое руководство поможет вам создать изолированный кластер разработки в течение нескольких минут.  Завершив работу с этим руководством, вы получите работающий на отдельном компьютере кластер с тремя узлами, в который вы можете развертывать приложения.

## <a name="before-you-begin"></a>Перед началом работы
Service Fabric предоставляет установочный пакет для создания изолированных кластеров Service Fabric.  [Скачайте пакет установки](http://go.microsoft.com/fwlink/?LinkId=730690).  Распакуйте его в папку, например *C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer*, на компьютере или виртуальной машине, где необходимо настроить кластер разработки.  Содержимое пакета установки подробно описано [здесь](service-fabric-cluster-standalone-package-contents.md).

У администратора кластера, который развертывает и настраивает кластер, должны быть привилегии администратора на компьютере. Пакет Service Fabric нельзя установить на контроллере домена.

## <a name="validate-the-environment"></a>Проверка среды
Скрипт *TestConfiguration.ps1* в изолированном пакете используется как рекомендуемый анализатор, проверяющий возможность развертывания кластера в конкретной среде. [Здесь](service-fabric-cluster-standalone-deployment-preparation.md) перечислены предварительные требования к среде при подготовке к развертыванию. Выполните сценарий чтобы проверить, можно ли создать кластер разработки:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Создание кластера
Некоторые примеры файлов конфигурации кластера устанавливаются с помощью пакета установки. *ClusterConfig.Unsecure.DevCluster.json* — это самая простая конфигурация кластера: незащищенный кластер из трех узлов, работающий на одном компьютере. Для выполнения инструкций из этого руководства вам не нужно что-то изменять в параметрах конфигурации по умолчанию.  Другие файлы конфигурации описывают кластеры с одним или несколькими компьютерами, защищенными с помощью системы безопасности Windows или сертификатов X.509.  См. дополнительные сведения о безопасности кластера Service Fabric в статье о [защите кластеров](service-fabric-cluster-security.md). 

Чтобы создать кластер разработки с тремя узлами, запустите скрипт *CreateServiceFabricCluster.ps1* из сеанса PowerShell с правами администратора:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Пакет среды выполнения Service Fabric автоматически скачивается и устанавливается во время создания кластера.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру
Теперь кластер с тремя узлами работает. Модуль PowerShell ServiceFabric устанавливается вместе со средой выполнения.  Вы можете проверить, работает ли кластер на одном компьютере или на удаленном компьютере со средой выполнения Service Fabric.  Подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster).   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Примеры других способов подключения к кластеру см. в статье о [безопасном подключении к кластеру](service-fabric-connect-to-secure-cluster.md). После подключения к кластеру используйте командлет [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode), чтобы отобразить список узлов в кластере и сведения о состоянии каждого узла. **Состояние работоспособности** каждого узла должно иметь значение *ОК*.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.5.216.0   0                     Up 03:00:07   00:00:00              Ok
                     vm1      localhost       NodeType1 5.5.216.0   0                     Up 03:00:02   00:00:00              Ok
                     vm0      localhost       NodeType0 5.5.216.0   0                     Up 03:00:01   00:00:00              Ok
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Визуализация кластера с помощью Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) — хорошее средство для визуализации кластера и управления приложениями.  Service Fabric Explorer — это служба, которая выполняется в кластере. Чтобы получить к ней доступ, перейдите в браузере по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer). 

На панели мониторинга кластера представлены общие сведения о кластере, включая общие сведения о приложении и работоспособности узла кластера. В представлении "Узлы" отображается физическая структура кластера. Для каждого узла можно просмотреть, какие приложения были развернуты на этом узле

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Удаление кластера
Чтобы удалить кластер, запустите сценарий Powershell *RemoveServiceFabricCluster.ps1* из папки пакета и передайте в него путь к файлу конфигурации JSON. При необходимости можно указать расположение журнала удаления.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Чтобы удалить среду выполнения Service Fabric с компьютера, выполните следующий скрипт PowerShell из папки пакета.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы настроили изолированный кластер разработки, перейдите к следующим шагам.
* [Настройте изолированный кластер с несколькими компьютерами](service-fabric-cluster-creation-for-windows-server.md) и включите режим безопасности.
* [Разверните приложения с помощью PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png

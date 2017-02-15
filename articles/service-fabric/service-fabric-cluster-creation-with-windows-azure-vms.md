---
title: "Создание автономного кластера из виртуальных машин Azure под управлением Windows | Документация Майкрософт"
description: "Узнайте, как создать кластер Azure Service Fabric на основе виртуальных машин Azure под управлением Windows Server и как управлять этим кластером."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/12/2016
ms.author: ryanwi;chackdan
translationtype: Human Translation
ms.sourcegitcommit: ea42f4b8cb026cc52749b3dc3b3e0dca7c82d14f
ms.openlocfilehash: ec0849b9e49b0ce5341908434248e51f1195ba37


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Создание автономного кластера Service Fabric с тремя узлами на базе виртуальных машин Azure под управлением Windows Server
В этой статье описывается создание кластера из виртуальных машин Azure под управлением Windows с помощью автономного установщика Service Fabric для Windows Server. Это частный случай [создания и администрирования кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md), при котором в качестве виртуальных машин используются [виртуальные машины Azure под управлением Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), но не создается [облачный кластер Service Fabric в Azure](service-fabric-cluster-creation-via-portal.md). Важное различие заключается в том, что автономным кластером Service Fabric, созданным с помощью следующей процедуры, полностью управляете вы, а облачные кластеры Service Fabric в Azure обновляет и администрирует поставщик ресурсов Service Fabric.

## <a name="steps-to-create-the-standalone-cluster"></a>Процедура создания автономного кластера
1. Войдите на портал Azure и создайте виртуальную машину Windows Server 2012 R2 Datacenter в группе ресурсов. Подробнее этот процесс описан в статье [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Добавьте в эту же группу ресурсов еще пару виртуальных машин Windows Server 2012 R2 Datacenter. При создании каждой из них укажите одно и то же имя пользователя и пароль администратора. После создания все три виртуальные машины появятся в одной и той же виртуальной сети.
3. Подключитесь к каждой из виртуальных машин и выключите брандмауэр Windows с помощью [диспетчера серверов и панели мониторинга локального сервера](https://technet.microsoft.com/library/jj134147.aspx). Это гарантирует, что сетевой трафик может передаваться между компьютерами. После входа на каждую из виртуальных машин узнайте ее IP-адрес, открыв командную строку и введя команду `ipconfig`. IP-адрес каждого компьютера можно также увидеть на портале, выбрав ресурс виртуальной сети для группы ресурсов и изучив сетевые интерфейсы, созданные для каждого из этих виртуальных машин.
4. Подключитесь к одной из виртуальных машин и проверьте наличие связи с остальными двумя.
5. Подключитесь к одной из виртуальных машин, [скачайте автономный пакет Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) в новую папку и извлеките содержимое пакета.
6. Откройте файл *ClusterConfig.Unsecure.MultiMachine.json* в Блокноте и измените каждый узел, введя три IP-адреса своих виртуальных машин. Измените имя кластера в начале файла и сохраните файл.  Ниже приведен неполный пример манифеста кластера.
   
    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```
7. Откройте окно [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Перейдите к папке, в которую вы ранее извлекли содержимое автономного пакета установщика и сохранили файл манифеста кластера. Выполните следующую команду PowerShell.
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```
8. Вы увидите, как сценарий PowerShell запустится, подключится к каждой виртуальной машине и создаст кластер. Примерно через минуту можно проверить работоспособность кластера, подключившись к Service Fabric Explorer по IP-адресу одной из виртуальных машин, например `http://10.7.0.5:19080/Explorer/index.html`. Чтобы сделать этот процесс безопасным (учитывая, что это автономный кластер на основе виртуальных машин Azure), необходимо [развернуть сертификаты на виртуальных машинах Azure](service-fabric-windows-cluster-x509-security.md) или настроить один из компьютеров в качестве [контроллера Windows Server Active Directory для проверки подлинности Windows](service-fabric-windows-cluster-windows-security.md). Это выполняется так же, как в обычной локальной среде.

## <a name="next-steps"></a>Дальнейшие действия
* [Создание автономных кластеров Service Fabric в Windows Server или Linux](service-fabric-deploy-anywhere.md)
* [Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md)
* [Защита автономного кластера под управлением Windows с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md)
* [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)




<!--HONumber=Dec16_HO2-->



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
ms.date: 03/24/2017
ms.author: ryanwi;chackdan
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Создание автономного кластера Service Fabric с тремя узлами на базе виртуальных машин Azure под управлением Windows Server
В этой статье описывается создание кластера виртуальных машин Azure под управлением Windows с помощью автономного установщика Service Fabric для Windows Server. Это частный случай [создания и администрирования кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md), при котором в качестве виртуальных машин используются [виртуальные машины Azure под управлением Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), но не создается [облачный кластер Service Fabric в Azure](service-fabric-cluster-creation-via-portal.md). Важное различие заключается в том, что изолированным кластером Service Fabric, созданным с помощью следующей процедуры, полностью управляете вы, а облачные кластеры Service Fabric в Azure обновляет и администрирует поставщик ресурсов Service Fabric.

## <a name="steps-to-create-the-standalone-cluster"></a>Процедура создания автономного кластера
1. Войдите на портал Azure и создайте виртуальную машину Windows Server 2012 R2 Datacenter или Windows Server 2016 Datacenter в группе ресурсов. Подробнее этот процесс описан в статье [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Добавьте еще несколько виртуальных машин в ту же группу ресурсов. При создании каждой из них укажите одно и то же имя пользователя и пароль администратора. После создания все три виртуальные машины появятся в одной и той же виртуальной сети.
3. Подключитесь к каждой из виртуальных машин и выключите брандмауэр Windows с помощью [диспетчера серверов и панели мониторинга локального сервера](https://technet.microsoft.com/library/jj134147.aspx). Это гарантирует, что сетевой трафик может передаваться между компьютерами. После входа на каждую из виртуальных машин узнайте ее IP-адрес, открыв командную строку и введя команду `ipconfig`. IP-адрес каждого компьютера можно также увидеть на портале, выбрав ресурс виртуальной сети для группы ресурсов и изучив сетевые интерфейсы, созданные для каждого из этих виртуальных машин.
4. Подключитесь к одной из виртуальных машин и проверьте наличие связи с остальными двумя.
5. Подключитесь к одной из виртуальных машин, [скачайте автономный пакет Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) в новую папку и извлеките содержимое пакета.
6. Откройте файл *ClusterConfig.Unsecure.MultiMachine.json* в Блокноте и измените каждый узел, введя три IP-адреса своих виртуальных машин. Измените имя кластера в начале файла и сохраните файл.  Ниже приведен неполный пример манифеста кластера.
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. Если планируется создать защищенный кластер, выберите меры безопасности, которые вы хотите использовать, и выполните инструкции, щелкнув соответствующую ссылку: [сертификат X509](service-fabric-windows-cluster-x509-security.md) или [система безопасности Windows](service-fabric-windows-cluster-windows-security.md). В случае настройки кластера с помощью системы безопасности Windows необходимо установить контроллер домена для управления Active Directory. Обратите внимание, что использование компьютера контроллера домена в качестве узла Service Fabric не поддерживается.
8. Откройте окно [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Перейдите к папке, в которую вы ранее извлекли содержимое автономного пакета установщика и сохранили файл конфигурации кластера. Выполните следующую команду PowerShell, чтобы развернуть кластер.
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

Сценарий удаленно настроит кластер Service Fabric и будет сообщать о ходе развертывания.

9. Примерно через минуту можно проверить, работает ли кластер, подключившись к Service Fabric Explorer по IP-адресу одной из виртуальных машин, например `http://10.1.0.5:19080/Explorer/index.html`. 

## <a name="next-steps"></a>Дальнейшие действия
* [Создание автономных кластеров Service Fabric в Windows Server или Linux](service-fabric-deploy-anywhere.md)
* [Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md)
* [Защита автономного кластера под управлением Windows с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md)
* [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)



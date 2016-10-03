<properties
   pageTitle="Добавление узлов к автономному кластеру Service Fabric или удаление узлов из него | Microsoft Azure"
   description="Узнайте, как добавлять узлы в кластер Azure Service Fabric или удалять их из него на физическом или виртуальном компьютере под управлением Windows Server, расположенном в локальной системе или в любом облаке."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него

После [создания автономного кластера Service Fabric на компьютерах под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md) потребности компании могут измениться, и вам может понадобиться добавить или удалить несколько узлов в кластере. Данная статья содержит подробные инструкции по выполнению этой задачи.


## Добавление узлов в кластер

1. Подготовьте виртуальную машину или компьютер, который необходимо добавить в кластер, выполнив действия, описанные в разделе [Шаг 2. Подготовка компьютеров для соблюдения предварительных требований](service-fabric-cluster-creation-for-windows-server.md#preparemachines).
2. Решите, в какой домен сбоя и домен обновления нужно добавить эту виртуальную машину или компьютер.
3. Подключитесь к виртуальной машине или компьютеру, который нужно добавить в кластер, с помощью удаленного рабочего стола.
4. Скопируйте или [скачайте изолированный пакет для Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) на виртуальную машину или компьютер и извлеките его содержимое.
5. Запустите PowerShell от имени администратора и перейдите к расположению распакованного пакета.
6. Запустите файл Powershell *AddNode.ps1*, указав параметры, описывающие новый узел для добавления. В следующем примере в домен сбоя 1 и домен обновления 1 добавляется новый узел с именем VM5, типом NodeType0 и IP-адресом 182.17.34.52. *ExistingClusterConnectionEndPoint* — это конечная точка подключения для узла в существующем кластере. Для этой конечной точки можно выбрать IP-адрес *любого* узла в кластере.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA true
```

## Удаление узлов из кластера

1. Подключитесь к виртуальной машине или компьютеру, который нужно удалить из кластера, с помощью удаленного рабочего стола.
2. Скопируйте или [скачайте изолированный пакет для Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) на виртуальную машину или компьютер и извлеките его содержимое.
3. Запустите PowerShell от имени администратора и перейдите к расположению распакованного пакета.
4. Запустите файл Powershell *RemoveNode.ps1*. В приведенном ниже примере текущий узел удаляется из кластера. *ExistingClusterConnectionEndPoint* — это конечная точка подключения для узла в существующем кластере. Для этой конечной точки можно выбрать IP-адрес *любого* узла в кластере.

```
.\RemoveNode.ps1 -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```


## Дальнейшие действия
- [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md)
- [Защита автономного кластера под управлением Windows с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md)
- [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)
- [Create a three node standalone Service Fabric cluster with Azure virtual machines running Windows Server (Создание автономного кластера Service Fabric с тремя узлами на виртуальных машинах Azure под управлением Windows)](service-fabric-cluster-creation-with-windows-azure-vms.md)

<!---HONumber=AcomDC_0921_2016-->
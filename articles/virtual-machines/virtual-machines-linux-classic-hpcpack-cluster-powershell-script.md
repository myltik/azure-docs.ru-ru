---
title: Сценарий PowerShell для развертывания кластера пакета HPC на основе Linux | Microsoft Docs
description: Из этой статьи вы узнаете, как с помощью сценария PowerShell развернуть кластер пакета HPC в Linux на виртуальной машине Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-service-management,hpc-pack

ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/07/2016
ms.author: danlep

---
# Создание кластера высокопроизводительных вычислительных систем (HPC) Linux с помощью сценария развертывания пакета HPC в IaaS
Выполните сценарий PowerShell для развертывания пакета HPC в IaaS, чтобы развернуть полный кластер HPC для рабочих нагрузок Linux на виртуальных машинах Azure. Кластер состоит из присоединенного к Active Directory головного узла под управлением Windows Server и пакета Microsoft HPC, а также вычислительных узлов под управлением одного из дистрибутивов Linux с поддержкой пакета HPC. Если вы хотите развернуть кластер пакета HPC в Azure для рабочих нагрузок Windows, см. раздел о [создании кластера HPC Windows с помощью сценария развертывания пакета HPC в IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). Вы также можете использовать шаблон диспетчера ресурсов Azure для развертывания HPC-кластера. Например, см. статью [Create an HPC cluster with Linux compute nodes](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) (Создание кластера пакета HPC с вычислительными узлами Linux).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Пример файла конфигурации
Следующий файл конфигурации создает новый контроллер домена и доменный лес, а также развертывает кластер пакета HPC с одним головным узлом с локальными базами данных и десятью вычислительными узлами под управлением Linux. Все облачные службы создаются сразу в расположении East Asia (Восточная Азия). Вычислительные узлы под управлением Linux создаются в двух облачных службах и двух учетных записях хранения (от *MyLnxCN-0001* до *MyLnxCN-0005* в *MyLnxCNService01* и *mylnxstorage01* и от *MyLnxCN-0006* до *MyLnxCN-0010* в *MyLnxCNService02* и *mylnxstorage02*). Вычислительные узлы создаются из образа OpenLogic CentOS версии 7.0 для Linux.

Подставьте свои значения: имя вашей подписки и имена учетной записи и служб.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## Устранение неполадок
* **Ошибка «Виртуальная сеть не существует»**. Когда вы запускаете скрипт развертывания пакета HPC по модели IaaS для одновременного развертывания нескольких кластеров в Azure в рамках одной подписки, для одного или нескольких развертываний может возникать ошибка «Виртуальная сеть *VNet\_Name* не существует». В случае возникновения этой ошибки повторно выполните скрипт для развертывания, завершившегося сбоем.
* **Проблема с доступом к Интернету из виртуальной сети Azure**. Если вы создаете кластер пакета HPC с новым контроллером домена с помощью сценария развертывания или назначаете виртуальную машину контроллером домена вручную, могут возникать проблемы при подключении виртуальных машин в виртуальной сети Azure к Интернету. Проблема может возникать, когда DNS-сервер пересылки настраивается на контроллере домена автоматически и не выполняет разрешение адресов должным образом.
  
    Чтобы решить эту проблему, войдите на контроллер домена и удалите настройки сервера пересылки или задайте допустимый DNS-сервер пересылки. Для этого в диспетчере серверов щелкните **Сервис** > **DNS**, чтобы открыть диспетчер DNS, а затем дважды щелкните **Серверы пересылки**.

## Дальнейшие действия
* Сведения о поддерживаемых дистрибутивах Linux, перемещении данных и отправке заданий в кластер пакета HPC с помощью вычислительных узлов под управлением Linux см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md).
* Чтобы научиться использовать сценарий для создания кластера и запуска рабочей нагрузки HPC в Linux, см. следующие руководства:
  * [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
  * [Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
  * [Выполнение заданий STAR-CCM+ в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

<!---HONumber=AcomDC_0713_2016-->
---
title: Сценарий PowerShell для развертывания кластера HPC на основе Linux | Документация Майкрософт
description: Из этой статьи вы узнаете, как с помощью сценария PowerShell развернуть кластер пакета HPC 2012 R2 в Linux на виртуальной машине Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 66affb47190ba0c6fccaae8e8267b310682aee46
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841849"
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Создание кластера высокопроизводительных вычислительных систем (HPC) Linux с помощью сценария развертывания пакета HPC в IaaS
Выполните сценарий PowerShell для развертывания пакета HPC в IaaS, чтобы развернуть полный кластер HPC 2012 R2 для рабочих нагрузок Linux на виртуальных машинах Azure. Кластер состоит из присоединенного к Active Directory головного узла под управлением Windows Server и пакета Microsoft HPC, а также вычислительных узлов под управлением одного из дистрибутивов Linux с поддержкой пакета HPC. Если вы хотите развернуть кластер пакета HPC в Azure для рабочих нагрузок Windows, см. статью [Создание кластера для высокопроизводительных вычислений (HPC) Windows с помощью сценария развертывания пакета HPC в IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
> [!IMPORTANT] 
> Сценарий PowerShell, описанный в этой статье, создает кластер пакета Microsoft HPC 2012 R2 в Azure с помощью классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.
> Кроме того сценарий, описанный в этой статье не поддерживает пакет HPC 2016. Сведения о шаблонах Resource Manager для HPC Pack 2012 R2 и HPC Pack 2016 в см. в статье [Варианты развертывания кластера HPC Pack в Azure](../hpcpack-cluster-options.md).


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Пример файла конфигурации
Следующий файл конфигурации создает контроллер домена и доменный лес, а также развертывает кластер пакета HPC с одним головным узлом с локальными базами данных и десятью вычислительными узлами под управлением Linux. Все облачные службы создаются сразу в расположении East Asia (Восточная Азия). Вычислительные узлы под управлением Linux создаются в двух облачных службах и двух учетных записях хранения (от *MyLnxCN-0001* до *MyLnxCN-0005* в *MyLnxCNService01* и *mylnxstorage01* и от *MyLnxCN-0006* до *MyLnxCN-0010* в *MyLnxCNService02* и *mylnxstorage02*). Вычислительные узлы создаются из образа OpenLogic CentOS версии 7.0 для Linux. 

Подставьте свои значения: имя вашей подписки и имена учетной записи и служб.

```Xml
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
## <a name="troubleshooting"></a>Устранение неполадок
* **Ошибка "Виртуальная сеть не существует"**. Когда вы запускаете сценарий развертывания IaaS пакета HPC для одновременного развертывания нескольких кластеров в Azure в рамках одной подписки, для одного или нескольких развертываний может возникать ошибка "Виртуальная сеть *имя\_виртуальной_сети* не существует".
  В случае возникновения этой ошибки повторно выполните сценарий для развертывания, завершившегося сбоем.
* **Проблема с доступом к Интернету из виртуальной сети Azure**. Если вы создаете кластер с новым контроллером домена с помощью сценария развертывания или назначаете виртуальную машину контроллером домена вручную, могут возникать проблемы при подключении виртуальных машин к Интернету. Проблема может возникать, когда DNS-сервер пересылки настраивается на контроллере домена автоматически и не выполняет разрешение адресов должным образом.
  
    Чтобы решить эту проблему, войдите на контроллер домена и удалите настройки сервера пересылки или задайте допустимый DNS-сервер пересылки. Для этого в диспетчере серверов щелкните **Сервис** > **DNS**, чтобы открыть диспетчер DNS, а затем дважды щелкните **Серверы пересылки**.

## <a name="next-steps"></a>Дополнительная информация
* Сведения о поддерживаемых дистрибутивах Linux, перемещении данных и отправке заданий в кластер пакета HPC с помощью вычислительных узлов под управлением Linux см. в статье [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](hpcpack-cluster.md).
* Чтобы научиться использовать сценарий для создания кластера и запуска рабочей нагрузки HPC в Linux, см. следующие руководства:
  * [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](hpcpack-cluster-namd.md)
  * [Выполнение заданий OpenFoam в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](hpcpack-cluster-openfoam.md)
  * [Выполнение заданий STAR-CCM+ в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](hpcpack-cluster-starccm.md)


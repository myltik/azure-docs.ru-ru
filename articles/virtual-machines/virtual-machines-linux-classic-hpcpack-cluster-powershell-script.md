<properties
   pageTitle="Сценарий PowerShell для развертывания кластера пакета HPC на основе Linux | Microsoft Azure"
   description="Развертывание кластера пакета HPC на основе Linux в службах инфраструктуры Azure с помощью сценария PowerShell."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# Создание кластера высокопроизводительных вычислительных систем (HPC) Linux с помощью сценария развертывания пакета HPC в IaaS

Выполните сценарий PowerShell развертывания пакета HPC в IaaS на клиентском компьютере, чтобы развернуть полный кластер HPC для рабочих нагрузок Linux в службах инфраструктуры Azure (IaaS). Если вы хотите развернуть кластер пакета HPC в Azure для рабочих нагрузок Windows, см. раздел о [создании кластера HPC Windows с помощью сценария развертывания пакета HPC в IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Примеры файлов конфигурации

### Пример 1

Следующий файл конфигурации создает новый доменный лес и развертывает кластера пакета HPC с одним головным узлом с локальными базами данных и 20 вычислительными узлами под управлением Linux. Все облачные службы создаются сразу в расположении East Asia (Восточная Азия). Вычислительные узлы под управлением Linux создаются в четырех облачных службах и четырех учетных записях хранения (от _MyLnxCN-0001_ до _MyLnxCN-0005_ в _MyLnxCNService01_ и _mylnxstorage01_, от _MyLnxCN-0006_ до _MyLnxCN-0010_ в _MyLnxCNService02_ и _mylnxstorage02_, от _MyLnxCN-0011_ до _MyLnxCN-0015_ в _MyLnxCNService03_ и _mylnxstorage03_, а также от _MyLnxCN-0016_ до _MyLnxCN-0020_ в _MyLnxCNService04_ и _mylnxstorage04_). Вычислительные узлы создаются из образа OpenLogic CentOS версии 7.0 для Linux.

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
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## Устранение неполадок

* **Ошибка «Виртуальная сеть не существует»**. Когда вы запускаете скрипт развертывания пакета HPC по модели IaaS для одновременного развертывания нескольких кластеров в Azure в рамках одной подписки, для одного или нескольких развертываний может возникать ошибка «Виртуальная сеть *VNet\_Name* не существует». В случае возникновения этой ошибки повторно выполните скрипт для развертывания, завершившегося сбоем.

* **Проблема с доступом к Интернету из виртуальной сети Azure**. Если вы создаете кластер пакета HPC с новым контроллером домена с помощью сценария развертывания или делаете виртуальную машину контроллером домена вручную, могут возникать проблемы при подключении виртуальных машин в виртуальной сети Azure к Интернету. Проблема может возникать, когда DNS-сервер пересылки настраивается на контроллере домена автоматически и не выполняет разрешение адресов должным образом.

    Чтобы решить эту проблему, войдите на контроллер домена и удалите настройки сервера пересылки или задайте допустимый DNS-сервер пересылки. Для этого в диспетчере серверов щелкните **Сервис** > **DNS**, чтобы открыть диспетчер DNS, а затем дважды щелкните **Серверы пересылки**.
    
## Дальнейшие действия

* Учебники, в которых рассматривается сценарий для создания кластера и запуска рабочей нагрузки HPC в Linux, см. в статье [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md) или [Запуск OpenFOAM с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Вы также можете использовать шаблон диспетчера ресурсов Azure для развертывания HPC-кластера. Например, см. раздел [Создание кластера пакета HPC с вычислительными узлами Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

<!---HONumber=AcomDC_0406_2016-->
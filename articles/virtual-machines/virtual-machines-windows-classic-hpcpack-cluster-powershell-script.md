<properties
   pageTitle="Сценарий PowerShell для развертывания кластера HPC на основе Windows | Microsoft Azure"
   description="Из этой статьи вы узнаете, как с помощью сценария PowerShell развернуть кластер пакета HPC в Windows на виртуальной машине Azure."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# Создание кластера для высокопроизводительных вычислений (HPC) Windows с помощью сценария развертывания пакета HPC в IaaS

Выполните сценарий PowerShell для развертывания пакета HPC в IaaS, чтобы развернуть полный кластер HPC для рабочих нагрузок Windows на виртуальных машинах Azure. Кластер состоит из присоединенного к Active Directory головного узла под управлением Windows Server и пакета Microsoft HPC, а также дополнительных вычислительных ресурсов Windows, которые вы указали. Если вы хотите развернуть кластер пакета HPC в Azure для рабочих нагрузок Linux, см. раздел о [создании кластера HPC Linux с помощью сценария развертывания пакета HPC в IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Вы также можете использовать шаблон диспетчера ресурсов Azure для развертывания HPC-кластера. Примеры см. в статьях [Create an HPC cluster](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) (Создание кластера HPC) и [Create an HPC cluster with a custom compute node image](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) (Создание кластера HPC с помощью пользовательского образа вычислительного узла).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Примеры файлов конфигурации

В следующих примерах подставьте свои значения идентификатора подписки, а также имен учетной записи и службы.

### Пример 1

Следующий файл конфигурации развертывает кластер пакета HPC с одним головным узлом с локальными базами данных, а также пятью вычислительными узлами под управлением операционной системы Windows Server 2012 R2. Все облачные службы создаются сразу в расположении West US (западная часть США). Головной узел выступает в качестве контроллера домена в доменном лесу.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### Пример 2

Следующий файл конфигурации развертывает HPC-кластер в существующем доменном лесу. Кластер содержит один головной узел с локальными базами данных и 12 вычислительных узлов с примененным расширением виртуальных машин BGInfo. Автоматическая установка обновлений Windows отключена для всех виртуальных машин в доменном лесу. Все облачные службы создаются сразу в расположении East Asia (Восточная Азия). Вычислительные узлы создаются в трех облачных службах и трех учетных записях хранения: от _MyHPCCN-0001_ до _MyHPCCN-0005_ в _MyHPCCNService01_ и _mycnstorage01_; от _MyHPCCN-0006_ до _MyHPCCN0010_ в _MyHPCCNService02_ и _mycnstorage02_; от _MyHPCCN-0011_ до _MyHPCCN-0012_ в _MyHPCCNService03_ и _mycnstorage03_. Вычислительные узлы создаются из существующего частного образа вычислительного узла. Служба автоматического масштабирования включена с интервалами увеличения и уменьшения по умолчанию.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### Пример 3

Следующий файл конфигурации развертывает HPC-кластер в существующем доменном лесу. Кластер содержит один головной узел, один сервер базы данных с 500 ГБ дискового пространства, два узла-брокера под управлением операционной системы Windows Server 2012 R2, а также пять вычислительных узлов под управлением операционной системы Windows Server 2012 R2. Облачная служба MyHPCCNService создается в территориальной группе *MyIBAffinityGroup*, а остальные облачные службы — в территориальной группе *MyAffinityGroup*. Интерфейс REST API планировщика заданий HPC и веб-портал HPC работают на головном узле.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### Пример 4

Следующий файл конфигурации развертывает HPC-кластер в существующем доменном лесу. Кластер содержит один головной узел с локальными базами данных. Сценарий создает два шаблона узлов Azure, а на основе шаблона узла _AzureTemplate1_ — три узла Azure среднего размера. Файл сценария будет выполняться на головном узле после настройки головного узла.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## Устранение неполадок


* **Ошибка "Виртуальная сеть не существует"**. Когда вы запускаете сценарий для одновременного развертывания нескольких кластеров в Azure в рамках одной подписки, для одного или нескольких развертываний может возникать ошибка "Виртуальная сеть *имя\_виртуальной\_сети* не существует". В случае возникновения этой ошибки повторно выполните сценарий для развертывания, завершившегося сбоем.

* **Проблема с доступом к Интернету из виртуальной сети Azure**. Если вы создаете кластер с новым контроллером домена с помощью сценария развертывания или назначаете виртуальную машину контроллером домена вручную, могут возникать проблемы при подключении виртуальных машин к Интернету. Проблема может возникать, когда DNS-сервер пересылки настраивается на контроллере домена автоматически и не выполняет разрешение адресов должным образом.

    Чтобы решить эту проблему, войдите на контроллер домена и удалите настройки сервера пересылки или задайте допустимый DNS-сервер пересылки. Для настройки этого параметра в диспетчере серверов щелкните **Сервис** > **DNS**, чтобы открыть диспетчер DNS, а затем дважды щелкните **Серверы пересылки**.

* **Проблема с доступом к сети RDMA с виртуальных машин с ресурсоемкими вычислениями**. Если добавить виртуальные машины Windows Server вычислительного узла или узла-брокера, имеющие размер A8 или A9 (с поддержкой RDMA), на этих виртуальных машинах может возникнуть проблема с подключением к сети приложений RDMA. Причина этой проблемы может быть связана с неправильной установкой расширения HpcVmDrivers при добавлении виртуальных машин в кластер. Например, расширение может оставаться в состоянии «Выполняется установка».

    Чтобы решить эту проблему, проверьте состояние расширения на виртуальных машинах. Если расширение не установлено должным образом, попробуйте удалить узлы из кластера HPC, а затем снова добавить их. Например, вы можете добавить виртуальные машины для вычислительных узлов, выполнив скрипт Add-HpcIaaSNode.ps1 на головном узле.
    
## Дальнейшие действия

* Попробуйте запустить рабочую нагрузку в кластере. См. пример в [руководстве по началу работы](https://technet.microsoft.com/library/jj884144) с пакетом HPC.

* Руководство, в котором используется сценарий для развертывания кластера и запуска рабочей нагрузки HPC, см. в статье [Приступая к работе с рабочими нагрузками Excel и SOA в кластере пакета HPC в Azure](virtual-machines-windows-excel-cluster-hpcpack.md).

* Поэкспериментируйте со средствами HPC для запуска, остановки, добавления и удаления вычислительных узлов в кластере. См. статью [Управление числом и доступностью вычислительных узлов в кластере пакета HPC в Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Чтобы выполнить настройку для отправки заданий в кластер с локального компьютера, см. статью [Отправка заданий HPC c локального компьютера в кластер на основе пакета HPC, развернутый в Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

<!---HONumber=AcomDC_0928_2016-->
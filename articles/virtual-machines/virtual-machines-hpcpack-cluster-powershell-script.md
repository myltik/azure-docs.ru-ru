<properties
   pageTitle="Скрипт PowerShell для развертывания кластера на основе пакета HPC | Microsoft Azure"
   description="Развертывание полного HPC-кластера в службах инфраструктуры Azure с помощью скрипта Windows PowerShell."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Создание высокопроизводительных вычислительных кластеров (HPC-кластеров) на виртуальных машинах Azure с помощью скрипта развертывания пакета HPC по модели IaaS

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.



Выполните скрипт PowerShell для развертывания пакета HPC по модели IaaS на клиентском компьютере, чтобы развернуть полный HPC-кластер в службах инфраструктуры Azure (IaaS). Этот скрипт поддерживает несколько вариантов развертывания, в том числе добавление узлов кластера под управлением поддерживаемых операционных систем Linux и Windows Server.

В зависимости от вашей среды и предпочтений скрипт может создать всю инфраструктуру кластера, в том числе виртуальную сеть Azure, учетные записи хранения, облачные службы, контроллер домена, удаленные и локальные базы данных SQL, головной узел, узлы-посредники, вычислительные узлы и облачную службу Azure (PaaS). Скрипт также может использовать существующую инфраструктуру Azure и создать для HPC-кластера головной узел, узлы-посредники, вычислительные узлы и узлы Azure PaaS («burst»).


Общие сведения о планировании HPC-кластера см. в статьях [Оценка и продукта и планирование](https://technet.microsoft.com/library/jj899596.aspx) и [Приступая к работе](https://technet.microsoft.com/library/jj899590.aspx) библиотеки TechNet, посвященных пакету HPC.

>[AZURE.NOTE]Вы также можете использовать шаблон диспетчера ресурсов Azure для развертывания HPC-кластера. См. примеры в статьях [Создание кластера HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/), [Создание кластера HPC с пользовательским образом вычислительного узла](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) и [Создание кластера HPC с вычислительными узлами под управлением Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

## Предварительные требования

* **Подписка на Azure**. Вы можете использовать подписку на службу Azure Global или Azure China. Ограничения подписки влияют на количество и тип узлов кластера, которые вы можете развернуть. Дополнительные сведения см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).


* **Компьютеры под управлением Windows с установленным и настроенным Azure PowerShell 0.8.7 или более поздней версии**. См. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Скрипт выполняется в службе управления Azure.


* **Скрипт развертывания IaaS из пакета HPC**. Скачайте и распакуйте последнюю версию скрипта из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=44949). Вы можете проверить версию скрипта, выполнив команду `New-HPCIaaSCluster.ps1 –Version`. В этой статье предполагается использование скрипта версии 4.4.0 или выше.

* **Файл конфигурации скрипта**. Вам потребуется создать XML-файл, с помощью которого скрипт настраивает HPC-кластер. Дополнительные сведения и примеры приведены далее.


## Синтаксис

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]Скрипт необходимо запускать от имени администратора.

### Параметры

* **ConfigFile** — путь к файлу конфигурации HPC-кластера. Дополнительные сведения см. в разделе [Файл конфигурации](#Configuration-file) этой статьи или в файле Manual.rtf в папке со скриптом.

* **AdminUserName** — имя пользователя. Если с помощью скрипта создается доменный лес, указанное здесь значение становится именем пользователя локального администратора для всех виртуальных машин, а также именем пользователя администратора домена. Если доменный лес уже существует, указанное здесь значение становится именем пользователя локального администратора, которое будет использоваться для установки пакета HPC.

* **AdminPassword** — пароль администратора. Если значение не указано в командной строке, скрипт предложит ввести пароль.

* **HPCImageName** (необязательно) — образ виртуальной машины с пакетом HPC, используемый для развертывания HPC-кластера. Это должен быть образ пакета HPC от корпорации Майкрософт (доступен в Azure Marketplace). Если значение не указано (рекомендуется в большинстве случаев), скрипт выбирает последний опубликованный образ пакета HPC.

    >[AZURE.NOTE]Если не указать действительный образ пакета HPC, развертывание завершится с ошибкой.

* **LogFile** (необязательно) — путь к файлу журнала развертывания. Если значение не указано, скрипт создаст файл журнала во временной папке на компьютере, на котором выполняется скрипт.

* **Force** (необязательно). Подавляет все окна с запросами подтверждения.

* **NoCleanOnFailure** (необязательно). Указывает, что виртуальные машины Azure, развертывание которых не удалось, не должны удаляться. Перед повторным выполнением скрипта необходимо удалить такие виртуальные машины вручную. В противном случае может произойти сбой развертывания.

* **PSSessionSkipCACheck** (необязательно). Для каждой облачной службы с виртуальными машинами, развернутыми с помощью скрипта, Azure автоматически создает самозаверяющий сертификат, и все виртуальные машины в облачной службе используют этот сертификат как стандартный сертификат удаленного управления Windows (WinRM). Чтобы развернуть компоненты HPC на этих виртуальных машинах Azure, по умолчанию скрипт временно устанавливает эти сертификаты в хранилище «Локальный компьютер\\Доверенные корневые центры сертификации», чтобы подавить ошибки безопасности «Не является доверенным ЦС» во время его выполнения. После завершения выполнения скрипта сертификаты удаляются. Если этот параметр задан, сертификаты не устанавливаются на локальный компьютер, а предупреждения системы безопасности подавляются.

    >[AZURE.IMPORTANT]Не рекомендуется использовать этот параметр для развертывания в рабочей среде.

### Пример

В следующем примере создается новый HPC-кластер с помощью файла конфигурации MyConfigFile.xml, а также указываются учетные данные администратора для установки кластера.

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### Дополнительные замечания

* Скрипт использует образ виртуальной машины на основе пакета HPC, доступный в Azure Marketplace, для создания головного узла кластера. Текущая версия образа предполагает, что установлена ОС Windows Server 2012 R2 Datacenter с пакетом HPC 2012 R2 с обновлением 3.

* Скрипт дополнительно может включать отправку заданий через веб-портал для пакета HPC или с помощью REST API для пакета HPC.


* Скрипт может дополнительно запускать другие скрипты до и после настройки головного узла (если нужно установить дополнительное программное обеспечение или настроить другие параметры).


## Файл конфигурации

Скрипт развертывания использует файл конфигурации в формате XML. Файл схемы HPCIaaSClusterConfig.xsd находится в папке скрипта развертывания пакета HPC по модели IaaS. **IaaSClusterConfig** — это корневой элемент файла конфигурации. Его дочерние элементы подробно описаны в файле Manual.rtf в папке скрипта развертывания. Файлы для разных сценариев см. в разделе [Примеры файлов конфигурации](#Example-configuration-files) этой статьи.

## Примеры файлов конфигурации

### Пример 1

Следующий файл конфигурации развертывает HPC-кластер в существующем доменном лесу. Кластер содержит один головной узел с локальными базами данных и 12 вычислительных узлов с примененным расширением виртуальных машин BGInfo. Автоматическая установка обновлений Windows отключена для всех виртуальных машин в доменном лесу. Все облачные службы создаются сразу в расположении East Asia (Восточная Азия). Вычислительные узлы создаются с помощью трех облачных служб и трех учетных записей хранения (от MyHPCCN-0001 до MyHPCCN-0005 — в MyHPCCNService01 и mycnstorage01; от MyHPCCN-0006 до MyHPCCN0010 — в MyHPCCNService02 и mycnstorage02; а также MyHPCCN-0011 и MyHPCCN-0012 — в MyHPCCNService03 и mycnstorage03). Вычислительные узлы создаются из существующего частного образа вычислительного узла. Служба автоматического масштабирования включена с интервалами увеличения и уменьшения по умолчанию.

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

### Пример 2

Следующий файл конфигурации развертывает HPC-кластер в существующем доменном лесу. Кластер содержит один головной узел, один сервер базы данных с 500 ГБ дискового пространства, два узла-посредника под управлением операционной системы Windows Server 2012 R2, а также пять вычислительных узлов под управлением операционной системы Windows Server 2012 R2. Облачная служба MyHPCCNService создается в группе сходства MyIBAffinityGroup, а все остальные облачные службы — в группе сходства MyAffinityGroup. Интерфейс REST API планировщика заданий HPC и веб-портал HPC работают на головном узле.

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

### Пример 3

Следующий файл конфигурации создает новый доменный лес и развертывает HPC-кластер с одним головным узлом с локальными базами данных и 20 вычислительными узлами под управлением Linux. Все облачные службы создаются сразу в расположении East Asia (Восточная Азия). Вычислительные узлы под управлением Linux создаются в четырех облачных службах и четырех учетных записях хранения (с MyLnxCN-0001 по MyHPCCN-0005 — в MyLnxCNService01 и mylnxstorage01, с MyLnxCN-0006 по MyLnxCN-0010 — в MyLnxCNService02 и mylnxstorage02, с MyLnxCN 0011 по MyLnxCN-0015 — в MyLnxCNService03 и mylnxstorage03, с MyLnxCN-0016 по MyLnxCN-0020 — в MyLnxCNService04 и mylnxstorage04). Вычислительные узлы создаются из образа OpenLogic CentOS версии 7.0 для Linux.

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


### Пример 4

Следующий файл конфигурации развертывает HPC-кластер с одним головным узлом с локальными базами данных, а также пятью вычислительными узлами под управлением операционной системы Windows Server 2008 R2. Все облачные службы создаются сразу в расположении East Asia (Восточная Азия). Головной узел выступает в качестве контроллера домена в доменном лесу.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
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
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### Пример 5

Следующий файл конфигурации развертывает HPC-кластер в существующем доменном лесу. Кластер содержит один головной узел с локальными базами данных; скрипт создает два шаблона узлов Azure, а на основе шаблона узла AzureTemplate1 — три узла Azure среднего размера. Файл скрипта будет выполняться на головном узле после настройки головного узла.

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
## Известные проблемы


* **Ошибка «Виртуальная сеть не существует»**. Когда вы запускаете скрипт развертывания пакета HPC по модели IaaS для одновременного развертывания нескольких кластеров в Azure в рамках одной подписки, для одного или нескольких развертываний может возникать ошибка «Виртуальная сеть *VNet\_Name* не существует». В случае возникновения этой ошибки повторно выполните скрипт для развертывания, завершившегося сбоем.

* **Проблема с доступом к Интернету из виртуальной сети Azure**. Если вы создаете HPC-кластер с новым контроллером домена с помощью скрипта развертывания или делаете виртуальную машину контроллером домена вручную, могут возникать проблемы при подключении виртуальных машин в виртуальной сети Azure к Интернету. Проблема может возникать, когда DNS-сервер пересылки настраивается на контроллере домена автоматически и не выполняет разрешение адресов должным образом.

    Чтобы решить эту проблему, войдите на контроллер домена и удалите настройки сервера пересылки или задайте допустимый DNS-сервер пересылки. Для этого в диспетчере серверов щелкните **Сервис** > **DNS**, чтобы открыть диспетчер DNS, а затем дважды щелкните **Серверы пересылки**.

* **Проблема с доступом к сети RDMA с виртуальных машин размера A8 и A9**. Если добавить виртуальные машины вычислительного узла под управлением Windows Server или узла-посредника размера A8 или A9 с помощью скрипта развертывания, на этих виртуальных машинах может возникнуть проблема с подключением к сети приложений RDMA. Причина этой проблемы может быть связана с неправильной установкой расширения HpcVmDrivers при добавлении в кластер виртуальных машин размера A8 и A9. Например, расширение может оставаться в состоянии «Выполняется установка».

    Чтобы решить эту проблему, проверьте состояние расширения на виртуальных машинах. Если расширение не установлено должным образом, попробуйте удалить узлы из кластера HPC, а затем снова добавить их. Например, вы можете добавить виртуальные машины для вычислительных узлов, выполнив скрипт Add-HpcIaaSNode.ps1 на головном узле.


## Дальнейшие действия

* Попробуйте запустить рабочую нагрузку в кластере. См. пример в [руководстве по началу работы](https://technet.microsoft.com/library/jj884144) с пакетом HPC.

* Учебник, в котором рассматривается скрипт для создания кластера и запуска рабочей нагрузки HPC, см. в статьях [Начало работы с кластером пакета HPC в Azure для запуска рабочих нагрузок Excel и SOA](virtual-machines-excel-cluster-hpcpac), [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-cluster-hpcpack-namd.md) или [Запуск OpenFOAM с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md).

* Поэкспериментируйте со средствами HPC для запуска, остановки, добавления и удаления вычислительных узлов в кластере. См. статью [Управление вычислительными узлами в кластере на основе пакета HPC в Azure](virtual-machines-hpcpack-cluster-node-manage.md).

<!---HONumber=AcomDC_1210_2015-->
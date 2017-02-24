---
title: "Управление несколькими средами в Service Fabric | Документация Майкрософт"
description: "Приложения Service Fabric могут выполняться в кластерах размером от одного до нескольких тысяч компьютеров. Иногда для этих различных сред необходимо по-разному настроить приложение. В этой статье объясняется, как определить различные параметры приложения в каждой среде."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/06/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: b57655c8041fa366d0aeb13e744e30e834ec85fa
ms.openlocfilehash: 7432e45ef33bd4d51fca8e8db8ec880e8beaf3ab


---
# <a name="manage-application-parameters-for-multiple-environments"></a>Управление параметрами приложения для нескольких сред
Вы можете создавать кластеры Service Fabric Azure, включая в них от одного до нескольких тысяч компьютеров. Хотя двоичные файлы приложения могут выполняться без изменений в различных средах, часто требуется по-разному настроить приложение в зависимости от количества компьютеров, на которых выполняется развертывание.

В качестве простого примера рассмотрим параметр `InstanceCount` для службы без отслеживания состояния. При запуске приложения в Azure для этого параметра, как правило, указывается особое значение: -1. Это гарантирует, что служба запущена в каждом узле кластера (или в каждом узле определенного типа при установке ограничения для размещения). Тем не менее такая конфигурация не подходит для кластера с одним компьютером, так как нельзя запускать несколько процессов, которые прослушиваются в одной и той же конечной точке на одном компьютере. Вместо этого установите для параметра `InstanceCount` значение&1;.

## <a name="specifying-environment-specific-parameters"></a>Выбор параметров среды
Решить эту проблему с конфигурацией можно с помощью набора параметризованных служб по умолчанию и файлов параметров приложения, которые заполнят значения этих параметров для данной среды. Параметры служб и приложений по умолчанию настраиваются в манифестах приложений и служб. Определение схемы для файла ServiceManifest.xml и ApplicationManifest.xml устанавливается с пакетом SDK и средствами для Service Fabric по адресу *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Службы по умолчанию
Приложения Service Fabric состоят из коллекции экземпляров службы. Хотя вы можете создать пустое приложение, а затем динамически создать все экземпляры службы, в большинстве приложений есть набор основных служб, которые всегда необходимо создавать во время создания экземпляра приложения. Такие службы называются службами по умолчанию. Они указываются в манифесте приложения с заполнителями (в квадратных скобках) для конфигурации каждой среды:

```xml
    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>
```

Все именованные параметры должны быть определены в элементе Parameters манифеста приложения:

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

Атрибут DefaultValue указывает значение, которое используется при отсутствии более конкретного параметра для данной среды.

> [!NOTE]
> Не все параметры экземпляра службы подходят для конфигурации среды. В примере выше значения LowKey и HighKey для схемы секционирования службы явно заданы для всех экземпляров службы, так как диапазон разделов — это функция домена данных, а не среды.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Параметры конфигурации службы в среде
[Модель приложения Service Fabric](service-fabric-application-model.md) позволяет службам включать пакеты конфигураций, содержащие пользовательские пары «ключ — значение», которые считываются во время выполнения. Среда может различать значения этих параметров, если в манифесте приложения указать класс `ConfigOverride` .

Предположим, что в файле Config\Settings.xml для службы `Stateful1` указан следующий параметр:

```xml
    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>
```
Чтобы переопределить это значение для конкретной пары «приложение — среда», создайте `ConfigOverride` при импорте манифеста служб в манифест приложения.

```xml
    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Как показано выше, позднее среда может настроить этот параметр. Для этого объявите его в разделе параметров в манифесте приложения и укажите конкретные значения для среды в файлах параметров приложения.

> [!NOTE]
> Что касается параметров конфигурации службы, значение ключа можно задать в трех местах: в пакете конфигурации службы, в манифесте приложения и в файле параметров приложения. Service Fabric сначала выберет значения из файла параметров приложения (если он указан), затем из манифеста приложения и наконец из пакета конфигурации.
> 
> 

### <a name="setting-and-using-environment-variables"></a>Настройка и использование переменных среды 
Вы можете указать и задать переменные среды в файле ServiceManifest.xml, а затем в файле ApplicationManifest.xml переопределить их для каждого экземпляра.
В приведенном ниже примере показаны две переменные среды: одна с установленным значением, а другая будет переопределена. Параметры приложения можно использовать для задания значений переменных среды так же, как и при переопределении конфигурации.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
Чтобы переопределить переменные среды в файле ApplicationManifest.xml, укажите ссылку на пакет кода в ServiceManifest в элементе `EnvironmentOverrides`.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 После создания именованного экземпляра службы можно получить доступ к переменным среды из кода. Например в C# можно сделать следующее

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Переменные среды Service Fabric
В Service Fabric есть встроенные переменные среды, задаваемые для каждого экземпляра службы. Полный список переменных среды указан ниже. Полужирным шрифтом выделены переменные, которые будут использоваться в службе. Остальные переменные используются средой выполнения Service Fabric. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **Fabric_Endpoint_[имя_вашей_службы]TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

В примере кода ниже показано, как вывести список переменных среды Service Fabric.
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
Ниже приведен пример переменных среды для типа приложения `GuestExe.Application` с типом службы `FrontEndService` при запуске на локальном компьютере разработки.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = Code**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>Файлы параметров приложений
Проект приложения Service Fabric может включать один или несколько файлов параметров приложения. Каждый из них определяет конкретные значения для параметров, определенных в манифесте приложения:

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
По умолчанию новое приложение включает три файла параметров приложения: Local.1Node.xml, Local.5Node.xml и Cloud.xml.

![Файлы параметров приложения в обозревателе решений][app-parameters-solution-explorer]

Чтобы создать файл параметров, скопируйте и вставьте существующий файл, а затем присвойте ему имя.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Определение параметров среды во время развертывания
Во время развертывания необходимо выбрать соответствующий файл параметров для использования с приложением. Это можно сделать в диалоговом окне «Публикация» в Visual Studio или с помощью PowerShell.

### <a name="deploy-from-visual-studio"></a>Развертывание из Visual Studio
Во время публикации приложения в Visual Studio вы можете выбрать файл в списке доступных файлов параметров.

![Выберите файл параметров в диалоговом окне «Публикация»][publishdialog]

### <a name="deploy-from-powershell"></a>Развертывание из PowerShell
Сценарий PowerShell `Deploy-FabricApplication.ps1` , включенный в шаблон проекта приложения, принимает профиль публикации как параметр, а PublishProfile содержит ссылку на файл параметров приложения.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о некоторых основных понятиях, описанных здесь, см. в статье [Общие сведения о терминологии Service Fabric](service-fabric-technical-overview.md). Сведения о других возможностях управления приложениями, доступными в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png



<!--HONumber=Feb17_HO2-->



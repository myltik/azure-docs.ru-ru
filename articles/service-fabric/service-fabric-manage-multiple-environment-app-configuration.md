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
ms.date: 11/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: cd256c403cc8094a135157cdc69dbdd3971978ca
ms.openlocfilehash: 9f8a898f265bc27fc47ea2e3d00d123f3f47dad6


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



<!--HONumber=Dec16_HO2-->



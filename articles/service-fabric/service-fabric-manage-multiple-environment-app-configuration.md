<properties
   pageTitle="Управление несколькими средами в Service Fabric | Microsoft Azure"
   description="Приложения Service Fabric могут выполняться в кластерах размером от одного до нескольких тысяч компьютеров. Иногда для этих различных сред необходимо по-разному настроить приложение. В этой статье объясняется, как определить различные параметры приложения в каждой среде."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/08/2016"
   ms.author="seanmck"/>

# Управление параметрами приложения для нескольких сред

Вы можете создавать кластеры Service Fabric Azure, включая в них от одного до нескольких тысяч компьютеров. Хотя двоичные файлы приложения могут выполняться без изменений в различных средах, часто требуется по-разному настроить приложение в зависимости от количества компьютеров, на которых выполняется развертывание.

В качестве простого примера рассмотрим параметр `InstanceCount` для службы без отслеживания состояния. При запуске приложения в Azure для этого параметра, как правило, указывается особое значение: -1. Это гарантирует запуск службы на каждом узле в кластере. Тем не менее такая конфигурация не подходит для кластера с одним компьютером, так как нельзя запускать несколько процессов, которые прослушиваются в одной и той же конечной точке на одном компьютере. Вместо этого установите для параметра `InstanceCount` значение 1.

## Выбор параметров среды

Решить эту проблему с конфигурацией можно с помощью набора параметризованных служб по умолчанию и файлов параметров приложения, которые заполнят значения этих параметров для данной среды.

### Службы по умолчанию

Приложения Service Fabric состоят из коллекции экземпляров службы. Хотя вы можете создать пустое приложение, а затем динамически создать все экземпляры службы, в большинстве приложений есть набор основных служб, которые всегда необходимо создавать во время создания экземпляра приложения. Такие службы называются службами по умолчанию. Они указываются в манифесте приложения с заполнителями (в квадратных скобках) для конфигурации каждой среды:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Все именованные параметры должны быть определены в элементе Parameters манифеста приложения:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

Атрибут DefaultValue указывает значение, которое используется при отсутствии более конкретного параметра для данной среды.

>[AZURE.NOTE] Не все параметры экземпляра службы подходят для конфигурации среды. В примере выше значения LowKey и HighKey для схемы секционирования службы явно заданы для всех экземпляров службы, так как диапазон разделов — это функция домена данных, а не среды.


### Параметры конфигурации службы в среде

[Модель приложения Service Fabric](service-fabric-application-model.md) позволяет службам включать пакеты конфигураций, содержащие пользовательские пары «ключ — значение», которые считываются во время выполнения. Среда может различать значения этих параметров, если в манифесте приложения указать класс `ConfigOverride`.

Предположим, что в файле Config\\Settings.xml для службы `Stateful1` указан следующий параметр:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Чтобы переопределить это значение для конкретной пары «приложение — среда», создайте `ConfigOverride` при импорте манифеста служб в манифест приложения.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Как показано выше, позднее среда может настроить этот параметр. Для этого объявите его в разделе параметров в манифесте приложения и укажите конкретные значения для среды в файлах параметров приложения.

>[AZURE.NOTE] Что касается параметров конфигурации службы, значение ключа можно задать в трех местах: в пакете конфигурации службы, в манифесте приложения и в файле параметров приложения. Service Fabric сначала выберет значения из файла параметров приложения (если он указан), затем из манифеста приложения и наконец из пакета конфигурации.


### Файлы параметров приложений

Проект приложения Service Fabric может включать один или несколько файлов параметров приложения. Каждый из них определяет конкретные значения для параметров, определенных в манифесте приложения:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

По умолчанию новое приложение включает два файла параметров приложения: Local.xml и Cloud.xml:

![Файлы параметров приложения в обозревателе решений][app-parameters-solution-explorer]

Чтобы создать файл параметров, скопируйте и вставьте существующий файл, а затем присвойте ему имя.

## Определение параметров среды во время развертывания

Во время развертывания необходимо выбрать соответствующий файл параметров для использования с приложением. Это можно сделать в диалоговом окне «Публикация» в Visual Studio или с помощью PowerShell.

### Развертывание из Visual Studio

Во время публикации приложения в Visual Studio вы можете выбрать файл в списке доступных файлов параметров.

![Выберите файл параметров в диалоговом окне «Публикация»][publishdialog]

### Развертывание из PowerShell

Сценарий PowerShell `DeployCreate-FabricApplication.ps1` принимает файл параметров в качестве параметра.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Дальнейшие действия

Дополнительные сведения об основных понятиях, описанных в этом разделе, см. в статье [Технический обзор платформы Service Fabric](service-fabric-technical-overview.md). Сведения о других возможностях управления приложением, доступных в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=AcomDC_0309_2016-->
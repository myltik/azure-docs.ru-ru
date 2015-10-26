<properties
 pageTitle="Автомасштабирование вычислительных ресурсов в кластере HPC | Microsoft Azure"
 description="Дополнительные сведения о способах автоматического увеличения и сжатия вычислительных ресурсов в кластере пакета HPC в Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Автоматическое масштабирование вычислительных ресурсов Azure в кластере пакета HPC согласно рабочей нагрузке кластера

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


Если вы развертываете в кластере пакета HPC «расширительные» узлы Azure или создаете кластер пакета HPC на виртуальных машинах Azure, вам может потребоваться способ автоматического увеличения или сжатия вычислительных ресурсов Azure в соответствии с текущей рабочей нагрузкой заданий и задач в кластере. Это позволяет более эффективно использовать ресурсы Azure и управлять затратами на них. Чтобы сделать это, используйте сценарий PowerShell HPC **AzureAutoGrowShrink.ps1**, который устанавливается вместе с пакетом HPC.

>[AZURE.TIP]Начиная с пакета HPC 2012 R2 с обновлением 2, пакет HPC включает в себя встроенную службу для автоматического увеличения или сжатия расширительных узлов Azure или вычислительных узлов виртуальной машины Azure. Настройте службу с помощью параметра в [сценарии развертывания IaaS пакета HPC](virtual-machines-hpcpack-cluster-powershell-script.md) или вручную установите свойство кластера **AutoGrowShrink**. См. раздел [Новые возможности пакета Microsoft HPC 2012 R2 с обновлением 2](https://technet.microsoft.com/library/mt269417.aspx).

## Предварительные требования

* **Кластер узла HPC 2012 R2 с обновлением 1 или более поздней версии** — сценарий **AzureAutoGrowShrink.ps1** установлен в папку %CCP\_HOME%bin. Головной узел кластера может быть развернут локально или на виртуальной машине Azure. См. раздел [Настройка гибридного кластера с пакетом HPC](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md), чтобы приступить к работе с локальными головными узлами и «расширительными» узлами Azure. См. раздел [Сценарий развертывания IaaS пакета HPC](virtual-machines-hpcpack-cluster-powershell-script.md)) для быстрого развертывания кластера пакета HPC на виртуальных машинах Azure.

* **Для кластеров с расширительными узлами Azure** — запустите сценарий на клиентском компьютере с установленным пакетом HPC или на головном узле. При выполнении на клиентском компьютере важно убедиться, что переменная $env:CCP\_SCHEDULER задана правильно и указывает на головной узел. «Расширительные» узлы Azure должны быть уже добавлены в кластер, однако они могут находиться в неразвернутом состоянии.


* **Для кластера, развернутого на виртуальных машинах Azure** —запустите сценарий на виртуальной машине головного узла, так как он зависит от сценариев **Start-HpcIaaSNode.ps1** и **Stop-HpcIaaSNode.ps1**, которые там установлены. Кроме того, эти сценарии требуют сертификат управления Azure или файл параметров публикации (см. раздел [Управление вычислительными узлами в кластере на основе пакета HPC в Azure](virtual-machines-hpcpack-cluster-node-manage.md)). Убедитесь, что все необходимые виртуальные машины вычислительных узлов уже добавлены в кластер, однако они могут находиться в остановленном состоянии.

## Синтаксис

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## Параметры

 * **NodeTemplates** — имена шаблонов узла для определения области увеличения и сжатия узлов. Если не указан (значение по умолчанию — @()), все узлы в группе узлов **AzureNodes** входят в область действия, когда **NodeType** имеет значение AzureNodes, и все узлы в группе узлов **ComputeNodes** входят в область действия, когда **NodeType** имеет значение ComputeNodes.

 * **JobTemplates** — имена шаблонов задания для определения области увеличения узлов.

 * **NodeType** — тип узла для увеличения и сжатия. Поддерживаемые значения:

     * **AzureNodes** — для (расширительных) узлов PaaS Azure в локальной среде или в кластере IaaS Azure.

     * **ComputeNodes** — для виртуальных машин вычислительных узлов только в кластере IaaS Azure.

* **NumOfQueuedJobsPerNodeToGrow** — количество заданий в очереди, требуемое для увеличения одного узла.

* **NumOfQueuedJobsToGrowThreshold** — пороговое значение числа заданий в очереди для запуска процесса увеличения.

* **NumOfActiveQueuedTasksPerNodeToGrow** — число активных задач в очереди, необходимое для увеличения одного узла. Если для **NumOfQueuedJobsPerNodeToGrow** указано значение больше 0, этот параметр пропускается.

* **NumOfActiveQueuedTasksToGrowThreshold** — пороговое значение числа активных задач в очереди для запуска процесса увеличения.

* **NumOfInitialNodesToGrow** — начальное минимальное число узлов для увеличения, если все узлы в области действия находятся в состоянии **Не развернут** или **Остановлен (распределение отменено)**.

* **GrowCheckIntervalMins** — интервал в минутах между проверками на увеличение.

* **ShrinkCheckIntervalMins** — интервал в минутах между проверками на сжатие.

* **ShrinkCheckIdleTimes** — количество последовательных проверок на сжатие (с интервалом **ShrinkCheckIntervalMins**), указывающих на простой узла.

* **UseLastConfigurations** — предыдущие конфигурации, сохраненные в файле аргументов.

* **ArgFile** — имя файла аргументов, используемого в целях сохранения и обновления конфигураций для выполнения сценария.

* **LogFilePrefix** — префикс файла журнала. Можно указать путь. По умолчанию журнал записывается в текущий рабочий каталог.

### Пример 1

В следующем примере расширительные узлы Azure, развернутые с помощью шаблона AzureNode по умолчанию, настраиваются для автоматического увеличения и сжатия. Если все узлы изначально находятся в состоянии **Не развернут**, запускается не менее 3 узлов. Если количество заданий в очереди превышает 8, сценарий запускает узлы, пока их количество не превысит отношение числа заданий в очереди к значению **NumOfQueuedJobsPerNodeToGrow**. Если узел обнаружен простаивающим 3 раза подряд, он останавливается.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### Пример 2

В следующем примере виртуальные машины вычислительного узла Azure, развернутые с помощью шаблона ComputeNode по умолчанию, настраиваются для автоматического увеличения и сжатия. Задания, настроенные с помощью шаблона задания по умолчанию, определяют область действия рабочей нагрузки в кластере. Если все узлы изначально находятся в остановленном состоянии, запускается не менее 5 узлов. Если количество активных задач в очереди превышает 15, сценарий запускает узлы, пока их количество не превысит отношение числа активных задач в очереди к значению **NumOfActiveQueuedTasksPerNodeToGrow**. Если узел обнаружен простаивающим 10 раз подряд, он останавливается.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=Oct15_HO3-->
---
title: Автомасштабирование узлов кластера пакета HPC | Документация Майкрософт
description: Автоматическое увеличение и уменьшение количества вычислительных узлов кластера пакета HPC в Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: ''
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 4a2350183bc0cb9360e9315cd8a351be20b66584
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603850"
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Автоматическое изменение размера ресурсов кластера пакета HPC в Azure в соответствии с рабочей нагрузкой кластера
Если вы развертываете в кластере пакета HPC "расширительные" узлы Azure или создаете кластер пакета HPC на виртуальных машинах Azure, может потребоваться способ автоматического увеличения или сжатия ресурсов кластера (например, узлов или ядер) в соответствии с рабочей нагрузкой в кластере. Такой способ масштабирования ресурсов позволяет более эффективно использовать ресурсы Azure и управлять затратами на них.

В этой статье показано два способа автомасштабирования вычислительных ресурсов, предоставляемых пакетом HPC:

* Свойство кластера пакета HPC **AutoGrowShrink**.

* Сценарий HPC PowerShell **AzureAutoGrowShrink.ps1**.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

В настоящее время можно автоматически увеличивать и сжимать только вычислительные узлы пакета HPC под управлением ОС Windows Server.


## <a name="set-the-autogrowshrink-cluster-property"></a>Настройка свойства кластера AutoGrowShrink
### <a name="prerequisites"></a>предварительным требованиям

* **Кластер пакета HPC 2012 R2 с обновлением 2 или более поздней версии**. Головной узел кластера можно развернуть локально или на виртуальной машине Azure. Ознакомьтесь со статьей [Настройка гибридного кластера с пакетом HPC](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md), чтобы приступить к работе с локальными головными узлами и "расширительными" узлами Azure. Ознакомьтесь со статьей [о сценарии развертывания IaaS пакета HPC](hpcpack-cluster-powershell-script.md) для быстрого развертывания кластера пакета HPC на виртуальных машинах Azure.

* **Для кластера с головным узлом в Azure (модель развертывания Resource Manager)**. Начиная с версии пакета HPC 2016 аутентификация сертификата в приложении Azure Active Directory используется для автоматического увеличения и сжатия виртуальных машин кластера, развернутых с помощью Azure Resource Manager. Настройте сертификат следующим образом:

  1. Развернув кластер, подключитесь к одному головному узлу с помощью удаленного рабочего стола.

  2. Передайте сертификат (в формате PFX с закрытым ключом) на каждый головной узел и установите его в папку Cert: \LocalMachine\My и Cert: \LocalMachine\Root.

  3. Запустите Azure PowerShell с правами администратора и выполните следующие команды на одном головном узле:

    ```powershell
        cd $env:CCP_HOME\bin

        Connect-AzureRmAccount
    ```
        
    Если ваша учетная запись используется в нескольких клиентах Azure Active Directory или подписках Azure, можно выполнить следующую команду, чтобы выбрать правильный клиент и подписку:
  
    ```powershell
        Connect-AzureRmAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Выполните следующую команду для просмотра текущего выбранного клиента и подписки:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Выполните следующий сценарий.

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    где:

    **DisplayName** — отображаемое имя активного приложения Azure. Если приложение не существует, оно создается в Azure Active Directory.

    **HomePage** — домашняя страница приложения. Вы можете настроить фиктивный URL-адрес, как показано в предыдущем примере.

    **IdentifierUri** — идентификатор приложения. Вы можете настроить фиктивный URL-адрес, как показано в предыдущем примере.

    **CertificateThumbprint** — отпечаток сертификата, установленного на головном узле на шаге 1.

    **TenantId** — идентификатор клиента каталога Azure Active Directory. Идентификатор клиента можно узнать на портале Azure Active Directory на странице **Свойства**.

    Чтобы получить дополнительные сведения о **ConfigARMAutoGrowShrinkCert.ps1**, выполните `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **Для кластера с головным узлом в Azure (классическая модель развертывания)**. При использовании сценария развертывания IaaS пакета HPC для создания кластера в классической модели развертывания включите свойство кластера **AutoGrowShrink**, задав параметр AutoGrowShrink в файле конфигурации кластера. Дополнительные сведения см. в сопроводительной документации [файла загрузки сценария](https://www.microsoft.com/download/details.aspx?id=44949).

    Кроме того, включите свойство кластера **AutoGrowShrink** после развертывания кластера с помощью команд HPC PowerShell, описанных в следующем разделе. Чтобы подготовиться к этому, выполните описанные ниже действия.

  1. Настройте сертификат управления Azure на головном узле и в подписке Azure. Для тестового развертывания можно использовать самозаверяющий сертификат Microsoft HPC Azure по умолчанию, который пакет HPC устанавливает на головном узле, а затем отправить этот сертификат в подписку Azure. Сведения о параметрах и необходимых действиях см. в [руководстве библиотеки TechNet](https://technet.microsoft.com/library/gg481759.aspx).

  2. Выполните на головном узле команду **regedit**, перейдите к расположению HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo и добавьте строковое значение. Для имени значения задайте значение "Отпечаток", а для данных значения — отпечаток сертификата, указанный в шаге 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Команды HPC PowerShell для настройки свойства AutoGrowShrink
Ниже приведены примеры команд HPC PowerShell для настройки свойства **AutoGrowShrink** и дополнительных параметров его поведения. Полный список параметров см. в разделе [Параметры AutoGrowShrink](#AutoGrowShrink-parameters) далее в этой статье.

Чтобы выполнить эти команды, запустите HPC PowerShell на головном узле кластера с правами администратора.

**Включение свойства AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Выключение свойства AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Изменение интервала увеличения в минутах**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Изменение интервала уменьшения в минутах**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Просмотр текущей конфигурации AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Исключение групп узлов из AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Этот параметр поддерживается, начиная с версии пакета HPC 2016.
>

### <a name="autogrowshrink-parameters"></a>Параметры AutoGrowShrink
Ниже перечислены параметры AutoGrowShrink, которые можно изменить с помощью команды **Set-HpcClusterProperty** .

* **EnableGrowShrink** — параметр для включения или отключения свойства **AutoGrowShrink**.
* **ParamSweepTasksPerCore** — число задач параметрического анализа для увеличения одного ядра. Значение по умолчанию — увеличение одного ядра в каждой задаче.

  > [!NOTE]
  > Пакет HPC QFE KB3134307 изменяет параметр **ParamSweepTasksPerCore** на **TasksPerResourceUnit**. Он основан на типе ресурса задания и может быть узлом, сокетом или ядром.
  >
  >
* **GrowThreshold** — пороговое количество задач, находящихся в очереди, для запуска автоматического увеличения. Значение по умолчанию — 1. Это означает, что, если в очереди есть одна или несколько задач, увеличение узлов начнется автоматически.
* **GrowInterval** — интервал в минутах для активации автоматического увеличения. Значение по умолчанию — 5 минут.
* **ShrinkInterval** — интервал в минутах для активации автоматического уменьшения. Значение по умолчанию — 5 минут.|
* **ShrinkIdleTimes** — количество непрерывных проверок на уменьшение, выполняемых для определения того, находится ли узел в состоянии простоя. Значение по умолчанию — 3 раза. Например, если для параметра **ShrinkInterval** установлено значение 5 минут, пакет HPC проверяет, находится ли узел в состоянии простоя, каждые 5 минут. Если узлы находятся в состоянии простоя, после 3 последовательных проверок (15 минут), пакет HPC уменьшает этот узел.
* **ExtraNodesGrowRatio** — дополнительный процент узлов, которые следует увеличить, для заданий интерфейса передачи сообщений (MPI). Значение по умолчанию — 1. Это означает, что пакет HPC увеличивает узлы на 1 % для заданий MPI.
* **GrowByMin** — параметр для указания того, основана ли политика автоматического увеличения на минимальном количестве ресурсов, необходимых для задания. Значение по умолчанию — false. Это означает, что пакет HPC увеличивает узлы для заданий на основании максимального количества ресурсов, необходимых для заданий.
* **SoaJobGrowThreshold** — пороговое значение входящих запросов SOA для автоматической активации увеличения. Значение по умолчанию — 50000.

  > [!NOTE]
  > Этот параметр поддерживается, начиная с версии пакета HPC 2012 R2 с обновлением 3.
  >
  >
* **SoaRequestsPerCore** — количество входящих запросов SOA для увеличения одного ядра. Значение по умолчанию — 20 000.

  > [!NOTE]
  > Этот параметр поддерживается, начиная с версии пакета HPC 2012 R2 с обновлением 3.
  >
* **ExcludeNodeGroups**. Узлы в указанных группах узлов не изменяют размер автоматически.
  
  > [!NOTE]
  > Этот параметр поддерживается, начиная с версии пакета HPC 2016.
  >

### <a name="mpi-example"></a>Пример MPI
По умолчанию пакет HPC увеличивает дополнительные узлы на 1 % для заданий MPI (для параметра **ExtraNodesGrowRatio** задано значение 1). Это связано с тем, что для MPI может потребоваться несколько узлов, а задание может выполняться только в том случае, когда готовы все узлы. Иногда при запуске узлов Azure одному из них может потребоваться больше времени для запуска, чем другим. Это приведет к тому, что другие узлы будут в состоянии простоя, ожидая, пока запустится этот узел. Увеличивая размер дополнительных узлов, пакет HPC сокращает это время ожидания ресурса и потенциально сокращает расходы. Для увеличения количества дополнительных узлов для заданий MPI (например, на 10 %) выполните команду, подобную следующей:

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Пример SOA
По умолчанию для параметра **SoaJobGrowThreshold** задано значение 50000, а для **SoaRequestsPerCore** — 20000. Если отправить одно задание SOA с 70 000 запросов, будет существовать одна задача, поставленная в очередь, и 70 000 входящих запросов. В данном случае пакет HPC увеличит 1 ядро для задачи в очереди и 1 ядро для входящих запросов (по формуле (70 000 – 50 000)/20 000 = 1), поэтому в итоге для этого задания SOA увеличатся 2 ядра.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Запуск скрипта AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>предварительным требованиям

* **Кластер узла HPC 2012 R2 с обновлением 1 или более поздней версии** — сценарий **AzureAutoGrowShrink.ps1** установлен в папку %CCP_HOME%bin. Головной узел кластера может быть развернут локально или на виртуальной машине Azure. Ознакомьтесь со статьей [Настройка гибридного кластера с пакетом HPC](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md), чтобы приступить к работе с локальными головными узлами и "расширительными" узлами Azure. Выполните быстрое развертывание кластера пакета HPC на виртуальных машинах Azure, как указано в [описании сценария развертывания IaaS пакета HPC](hpcpack-cluster-powershell-script.md), или см. [краткое руководство по шаблонам Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Azure PowerShell 1.4.0.** В настоящее время сценарий зависит от конкретной версии Azure PowerShell.
* **Для кластеров с расширительными узлами Azure** — запустите сценарий на клиентском компьютере с установленным пакетом HPC или на головном узле. При выполнении на клиентском компьютере важно убедиться, что переменная $env:CCP_SCHEDULER задана и указывает на головной узел. "Расширительные" узлы Azure должны быть добавлены в кластер, однако они могут находиться в неразвернутом состоянии.
* **Для кластера, развернутого на виртуальных машинах Azure (модель развертывания Resource Manager).** Для кластера виртуальных машин Azure, развернутого с использованием модели Resource Manager, сценарий поддерживает два метода аутентификации Azure: вход в учетную запись Azure для постоянного выполнения сценария (запустив `Connect-AzureRmAccount`) или настройка субъекта-службы для аутентификации с помощью сертификата. Пакет HPC содержит сценарий **ConfigARMAutoGrowShrinkCert.ps** для создания субъекта-службы с сертификатом. Сценарий создает приложение Azure Active Directory (Azure AD) и субъект-службу, а также присваивает ей роль участника. Чтобы выполнить сценарий, запустите Azure PowerShell с правами администратора и выполните следующие команды:

    ```powershell
    cd $env:CCP_HOME\bin

    Connect-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Чтобы получить дополнительные сведения о **ConfigARMAutoGrowShrinkCert.ps1**, выполните `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.

* **Для кластера, развернутого на виртуальных машинах Azure (классическая модель развертывания).** Запустите сценарий на виртуальной машине головного узла, так как он зависит от сценариев **Start-HpcIaaSNode.ps1** и **Stop-HpcIaaSNode.ps1**, которые там установлены. Кроме того, эти сценарии требуют сертификат управления Azure или файл параметров публикации (см. статью [Управление вычислительными узлами в кластере на основе пакета HPC в Azure](hpcpack-cluster-node-manage.md)). Убедитесь, что все необходимые виртуальные машины вычислительных узлов уже добавлены в кластер. Они могут находиться в остановленном состоянии.



### <a name="syntax"></a>Синтаксис
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Параметры
* **NodeTemplates** — имена шаблонов узла для определения области увеличения и сжатия узлов. Если не указан (значение по умолчанию — @()), все узлы в группе узлов **AzureNodes** входят в область действия, если **NodeType** имеет значение AzureNodes, и все узлы в группе узлов **ComputeNodes** входят в область действия, если **NodeType** имеет значение ComputeNodes.
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
* **ArgFile**— имя файла аргументов, используемого в целях сохранения и обновления конфигураций для выполнения сценария.
* **LogFilePrefix** — префикс файла журнала. Можно указать путь. По умолчанию журнал записывается в текущий рабочий каталог.

### <a name="example-1"></a>Пример 1
В следующем примере расширительные узлы Azure, развернутые с помощью шаблона AzureNode по умолчанию, настраиваются для автоматического увеличения и сжатия. Если все узлы изначально находятся в состоянии **Не развернут** , запускается не менее 3 узлов. Если количество заданий в очереди превышает 8, сценарий запускает узлы, пока их количество не превысит отношение числа заданий в очереди к значению **NumOfQueuedJobsPerNodeToGrow**. Если узел обнаружен простаивающим 3 раза подряд, он останавливается.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Пример 2
В следующем примере виртуальные машины вычислительного узла Azure, развернутые с помощью шаблона ComputeNode по умолчанию, настраиваются для автоматического увеличения и сжатия.
Задания, настроенные с помощью шаблона задания по умолчанию, определяют область действия рабочей нагрузки в кластере. Если все узлы изначально находятся в остановленном состоянии, запускается не менее 5 узлов. Если количество активных задач в очереди превышает 15, сценарий запускает узлы, пока их количество не превысит отношение числа активных задач в очереди к значению **NumOfActiveQueuedTasksPerNodeToGrow**. Если узел обнаружен простаивающим 10 раз подряд, он останавливается.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

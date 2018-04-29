---
title: Рабочие нагрузки контейнера в пакетной службе Azure | Документация Майкрософт
description: Выполнение приложения из образов контейнеров в пакетной службе Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 02/26/2018
ms.author: danlep
ms.openlocfilehash: fc8af53b0e0cfbe19a6509e8d126646badd0abbb
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Выполнение контейнерных приложений в пакетной службе Azure

Пакетная служба Azure позволяет выполнять и масштабировать большие количества заданий в пакетной обработке данных в Azure. До этого момента такие задачи выполнялись напрямую на виртуальных машинах в пуле пакетной службы, однако теперь пул пакетной службы можно настроить для выполнения задач в контейнерах Docker.

Использование контейнеров предоставляет простой способ запуска задач пакетной службы без необходимости управлять пакетами приложений и зависимостями. Контейнеры развертывают приложения в качестве упрощенных, переносимых, самодостаточных единиц, которые могут выполняться в различных средах. Например, можно создать и протестировать контейнер локально, а затем отправить образ контейнера в реестр в Azure или в другое расположение. Модель развертывания контейнера гарантирует, что среда выполнения приложения всегда правильно установлена и настроена, независимо от того, где размещено приложение. В этом руководстве показано, как использовать пакет SDK для .NET пакетной службы для создания пула вычислительных узлов, которые поддерживают выполнение задач контейнера, и как выполнять эти задачи в пуле.

В этой статье предполагается, что вы уже знакомы с основными понятиями контейнера Docker и тем, как создавать пул и задания пакетной службы с помощью пакета SDK для .NET. Фрагменты кода предназначены для использования в клиентском приложении аналогично [примеру DotNetTutorial](batch-dotnet-get-started.md). Они необходимы для поддержки в контейнерных приложениях в пакетной службе.


## <a name="prerequisites"></a>предварительным требованиям

* Версии SDK. Пакеты SDK для пакетной службы поддерживают образы контейнеров в таких версиях:
    * REST API пакетной службы версии 2017-09-01.6.0;
    * пакет SDK для .NET пакетной службы версии 8.0.0;
    * пакет SDK для Python пакетной службы версии 4.0;
    * пакет SDK для Java пакетной службы версии 3.0;
    * пакет SDK для Node.js пакетной службы версии 3.0.

* Учетные записи. В своей учетной записи Azure необходимо создать учетную запись пакетной службы и при необходимости создать учетную запись службы хранилища Azure.

* Поддерживаемый образ виртуальной машины. Контейнеры поддерживаются только в пулах, созданных с использованием конфигурации виртуальной машины из образов, описанных в следующем разделе: "Поддерживаемые образы виртуальных машин".

* Если предоставляется пользовательский образ, приложение должно использовать проверку подлинности Azure Active Directory (Azure AD) для запуска рабочих нагрузок на основе контейнера. При использовании образа Azure Marketplace нет необходимости в проверке подлинности Azure AD, так как будет работать проверка подлинности на основе общего ключа. Поддержка Azure AD пакетной службой Azure описана в статье [Аутентификация решений пакетной службы с помощью Active Directory](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Поддерживаемые образы виртуальных машин

Для создания пула вычислительных узлов виртуальных машин необходимо предоставить образ Windows или Linux.

### <a name="windows-images"></a>Образы Windows

Для рабочих нагрузок контейнера Windows пакетная служба в настоящее время поддерживает пользовательские образы, создаваемые на основе виртуальных машин, выполняющих Docker в Windows. Также можно использовать Windows Server 2016 Datacenter в образах контейнеров из Azure Marketplace. Этот образ совместим с номером SKU агента узла `batch.node.windows amd64`. В настоящее время поддерживается только тип контейнера Docker.

### <a name="linux-images"></a>Образы Linux

Для рабочих нагрузок контейнера Linux пакетная служба в настоящее время поддерживает только пользовательские образы, создаваемые на основе виртуальной машины, выполняющей Docker на следующих дистрибутивах Linux: Ubuntu 16.04 LTS или CentOS 7.3. Если вы решите предоставить собственный пользовательский образ Linux, ознакомьтесь с инструкциями в статье [Использование управляемого пользовательского образа для создания пула виртуальных машин](batch-custom-images.md).

Можно использовать [Docker Community Edition (CE)](https://www.docker.com/community-edition) или [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Если вы хотите воспользоваться преимуществами производительности GPU в виртуальных машинах размеров Azure NC или NV, в образ необходимо установить драйверы NVIDIA. Кроме того, необходимо установить и запустить служебную программу подсистемы Docker для GPU NVIDIA — [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Чтобы получить доступ к сети Azure RDMA, используйте виртуальные машины следующих размеров: A8, A9, H16r, H16mr или NC24r. Необходимые драйверы RDMA устанавливаются в образах CentOS 7.3 HPC и Ubuntu 16.04 LTS из Azure Marketplace. Для выполнения рабочих нагрузок MPI может потребоваться дополнительная настройка. Дополнительные сведения см. в статье [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Ограничения

* Пакетная служба обеспечивает поддержку RDMA только для контейнеров, выполняемых в пулах Linux.


## <a name="authenticate-using-azure-active-directory"></a>Проверка подлинности с помощью Azure Active Directory

При использовании пользовательского образа виртуальной машины для создания пула пакетной службы клиентское приложение должно пройти встроенную проверку подлинности Azure AD (проверка подлинности на основе общего ключа не работает). Перед запуском приложения убедитесь, что оно зарегистрировано в Azure AD, чтобы установить его идентификатор и указать разрешения для других приложений.

Кроме того, при использовании пользовательского образа виртуальной машины необходимо предоставить управление доступом IAM приложению для доступа к образу виртуальной машины. На портале Azure откройте **Все ресурсы**, выберите образ контейнера и в колонке образа в разделе **Управление доступом (IAM)** щелкните **Добавить**. В колонке **Добавление разрешений** укажите **роль**, в поле **Назначение доступа к** выберите **Пользователь, группа или приложение Azure AD**, а затем в поле **Выбрать** введите имя приложения.

В приложении передайте маркер проверки подлинности Azure AD при создании клиента пакетной службы с использованием [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), как описано в статье [Аутентификация решений пакетной службы с помощью Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Указание ссылки на образ виртуальной машины для создания пула

В коде приложения предоставьте ссылку на образ виртуальной машины, используемый для создания вычислительных узлов пула. Для этого создайте объект [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference). Образ для использования можно указать одним из следующих способов:

* При использовании пользовательского образа укажите идентификатор ресурса Azure Resource Manager для образа виртуальной машины. Идентификатор образа имеет такой формат пути, как показано в следующем примере:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Чтобы получить этот идентификатор образа на портале Azure, откройте **Все ресурсы**, выберите пользовательский образ и в разделе **Обзор** колонки образа скопируйте путь в поле **Идентификатор ресурса**.

* При использовании образа [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) предоставьте группу параметров, описывающих образ (издатель, тип предложения, номер SKU и версия образа), как указано в разделе [Список образов виртуальных машин](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Конфигурация контейнера для пула пакетной службы

Пул пакетной службы — это коллекция вычислительных узлов, на которых пакетная служба выполняет задачи в задании. При создании пула можно предоставить конфигурацию виртуальной машины для вычислительных узлов. Объект [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) содержит ссылку на объект [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration). Чтобы выполнять рабочие нагрузки контейнера в пуле, укажите параметры `ContainerConfiguration`. В конфигурации виртуальной машины также указывается ссылка на образ и номер SKU агента узла образа.

Есть несколько вариантов создания пула. Можно создать пул с предварительно полученными образами контейнеров или без них. 

Процесс извлечения (или предварительного получения) позволяет предварительно загрузить образы контейнеров из центра Docker или другого реестра контейнеров в Интернете. Преимущество предварительного получения образов контейнеров заключается в том, что при первом запуске задач не нужно ожидать скачивания образа контейнера. Конфигурация контейнера извлекает образы контейнеров на виртуальную машину при создании пула. Задачи, выполняющиеся в пуле, могут ссылаться на список образов контейнеров и параметры выполнения контейнера.



### <a name="pool-without-prefetched-container-images"></a>Пул без предварительно полученных образов контейнеров

Для настройки пула без предварительно полученных контейнерных образов определите объекты `ContainerConfiguration` и `VirtualMachineConfiguration`, как показано в следующем примере. В этом и следующих примерах подразумевается, что используется пользовательский образ Ubuntu 16.04 LTS с установленной подсистемой Docker.

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Предварительное получение образов для конфигурации контейнера

Для предварительной выборки образов контейнеров в пуле добавьте список образов контейнеров (`containerImageNames`) в `ContainerConfiguration` и присвойте этому списку имя. В следующем примере предполагается, что используется пользовательский образ Ubuntu 16.04 LTS, предварительно полученный образ TensorFlow из [центра Docker](https://hub.docker.com), а TensorFlow запускается в задаче запуска.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Предварительно полученные образы из закрытого реестра контейнеров

Можно также выполнять предварительное получение образов контейнеров путем проверки подлинности на сервере закрытого реестра контейнеров. В следующем примере объекты `ContainerConfiguration` и `VirtualMachineConfiguration` используют пользовательский образ Ubuntu 16.04 LTS и выполняют предварительную выборку закрытого образа TensorFlow из закрытого реестра контейнеров Azure.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Параметры контейнера для задачи

При настройке задач для выполнения на вычислительных узлах необходимо указать параметры для конкретного контейнера, такие как варианты выполнения задачи, используемые образы и реестр.

Используйте свойство ContainerSettings в классах задач для настройки параметров конкретного контейнера. Эти параметры определяются классом [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

При запуске задач на образах контейнеров для [задач облака](/dotnet/api/microsoft.azure.batch.cloudtask) и [диспетчера заданий](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) требуются параметры контейнера. Тем не менее [задача запуска](/dotnet/api/microsoft.azure.batch.starttask), [задача подготовки задания](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) и [задача снятия задания](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) не требуют параметров контейнера (то есть их можно выполнить в контексте контейнера или напрямую на узле).

При настройке параметров для контейнеров все каталоги рекурсивно ниже `AZ_BATCH_NODE_ROOT_DIR` (корень каталогов пакетной службы Azure на узле) сопоставляются в контейнере, все переменные среды задач сопоставляются в контейнере, а командная строка задач выполняется в контейнере.

В примере кода из раздела [Предварительное получение образов для конфигурации контейнера](#prefetch-images-for-container-configuration) показано, как задать конфигурацию контейнера для задачи запуска. В следующем примере кода показано, как задать конфигурацию контейнера для задачи облака:

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь также с документацией по набору средств [Batch Shipyard](https://github.com/Azure/batch-shipyard), чтобы легко развертывать рабочие нагрузки контейнера в пакетной службе Azure, следуя инструкциям [Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Дополнительные сведения об установке и использовании Docker CE в Linux см. в документации [Docker](https://docs.docker.com/engine/installation/).

* Дополнительные сведения об использовании пользовательских образов см. в статье [Использование управляемого пользовательского образа для создания пула виртуальных машин](batch-custom-images.md).

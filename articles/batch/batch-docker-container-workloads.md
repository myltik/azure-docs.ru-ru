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
ms.date: 05/07/2018
ms.author: danlep
ms.openlocfilehash: 8c9f772c9d3908e450961239797f6ce2bd4982e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885882"
---
# <a name="run-container-applications-on-azure-batch"></a>Выполнение контейнерных приложений в пакетной службе Azure

Пакетная служба Azure позволяет выполнять и масштабировать большие количества заданий в пакетной обработке данных в Azure. До этого момента такие задачи выполнялись напрямую на виртуальных машинах в пуле пакетной службы, однако теперь пул пакетной службы можно настроить для выполнения задач в контейнерах Docker. В этой статье показано, как использовать пакет SDK для .NET пакетной службы для создания пула вычислительных узлов, которые поддерживают выполнение задач контейнера, и как выполнять эти задачи в пуле.

Использование контейнеров предоставляет простой способ запуска задач пакетной службы без необходимости управлять средой и зависимостями. Контейнеры развертывают приложения в качестве упрощенных, переносимых, самодостаточных единиц, которые могут выполняться в различных средах. Например, можно создать и протестировать контейнер локально, а затем отправить образ контейнера в реестр в Azure или в другое расположение. Модель развертывания контейнера гарантирует, что среда выполнения приложения всегда правильно установлена и настроена, независимо от того, где размещено приложение. Для решения контейнерных задач в пакетной службе также можно воспользоваться преимуществами функций неконтейнерных задач, включая пакеты приложений и управление файлами ресурсов и выходными файлами. 

В этой статье предполагается, что вы уже знакомы с основными понятиями контейнера Docker и тем, как создавать пул и задания пакетной службы с помощью пакета SDK для .NET. Фрагменты кода предназначены для использования в клиентском приложении аналогично [примеру DotNetTutorial](batch-dotnet-get-started.md). Они необходимы для поддержки в контейнерных приложениях в пакетной службе.


## <a name="prerequisites"></a>предварительным требованиям

* **Версии SDK**. Пакеты SDK для пакетной службы поддерживают образы контейнеров в таких версиях:
    * REST API пакетной службы версии 2017-09-01.6.0;
    * пакет SDK для .NET пакетной службы версии 8.0.0;
    * пакет SDK для Python пакетной службы версии 4.0;
    * пакет SDK для Java пакетной службы версии 3.0;
    * пакет SDK для Node.js пакетной службы версии 3.0.

* **Учетные записи**. В своей подписке Azure необходимо создать учетную запись пакетной службы и при необходимости создать учетную запись службы хранилища Azure.

* **Поддерживаемый образ виртуальной машины**. Контейнеры поддерживаются только в пулах, созданных с использованием конфигурации виртуальной машины из образов, описанных в следующем разделе: "Поддерживаемые образы виртуальных машин". Если предоставляется пользовательский образ, приложение должно использовать [проверку подлинности Azure Active Directory](batch-aad-auth.md) (Azure AD) для запуска рабочих нагрузок на основе контейнера. 


## <a name="supported-virtual-machine-images"></a>Поддерживаемые образы виртуальных машин

Для создания пула узлов вычислений виртуальной машины для рабочих нагрузок контейнера необходимо использовать поддерживаемый образ Windows или Linux.

### <a name="windows-images"></a>Образы Windows

Для рабочих нагрузок контейнера Windows пакетная служба в настоящее время поддерживает пользовательские образы, создаваемые на основе виртуальных машин, выполняющих Docker в Windows. Также можно использовать Windows Server 2016 Datacenter в образах контейнеров из Azure Marketplace. Этот образ совместим с номером SKU агента узла `batch.node.windows amd64`. В настоящее время поддерживается только тип контейнера Docker.

### <a name="linux-images"></a>Образы Linux

Для рабочих нагрузок контейнера Linux пакетная служба в настоящее время поддерживает только пользовательские образы, создаваемые на основе виртуальной машины, выполняющей Docker на следующих дистрибутивах Linux: Ubuntu 16.04 LTS или CentOS 7.3. Если вы решите предоставить собственный пользовательский образ Linux, ознакомьтесь с инструкциями в статье [Использование управляемого пользовательского образа для создания пула виртуальных машин](batch-custom-images.md).

Для поддержки Docker установите [Docker Community Edition (CE)](https://www.docker.com/community-edition) или [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Если вы хотите воспользоваться преимуществами производительности GPU в виртуальных машинах размеров Azure NC или NV, в образ необходимо установить драйверы NVIDIA. Кроме того, необходимо установить и запустить служебную программу подсистемы Docker для GPU NVIDIA — [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Для доступа к сети Azure RDMA используйте виртуальную машину с поддержкой RDMA таких размеров, как A8, A9, H16r, H16mr или NC24r. Необходимые драйверы RDMA устанавливаются в образах CentOS 7.3 HPC и Ubuntu 16.04 LTS из Azure Marketplace. Для выполнения рабочих нагрузок MPI может потребоваться дополнительная настройка. Дополнительные сведения см. в статье [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Ограничения

* Пакетная служба обеспечивает поддержку RDMA только для контейнеров, выполняемых в пулах Linux.


## <a name="authenticate-using-azure-active-directory"></a>Проверка подлинности с помощью Azure Active Directory

При использовании пользовательского образа виртуальной машины для создания пула пакетной службы клиентское приложение должно пройти встроенную проверку подлинности Azure AD (проверка подлинности на основе общего ключа не работает). Перед запуском приложения убедитесь, что оно зарегистрировано в Azure AD, чтобы установить его идентификатор и указать разрешения для других приложений.

Кроме того, при использовании пользовательского образа виртуальной машины необходимо предоставить управление доступом IAM приложению для доступа к образу виртуальной машины. На портале Azure нажмите кнопку **Все ресурсы**, выберите образ контейнера и на странице образа в разделе **Управление доступом (IAM)** щелкните **Добавить**. На странице **Добавление разрешений** укажите **роль**, в поле **Назначение доступа к** выберите **Пользователь, группа или приложение Azure AD**, а затем в поле **Выбрать** введите имя приложения.

При создании клиента пакетной службы передайте маркер аутентификации Azure AD в приложение. При разработке с помощью пакета SDK для .NET пакетной службы используйте [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), как описано в разделе [Аутентификация решений пакетной службы с помощью Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Указание ссылки на образ виртуальной машины для создания пула

В коде приложения предоставьте ссылку на образ виртуальной машины, используемый для создания вычислительных узлов пула. Для этого создайте объект [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference). Образ для использования можно указать одним из следующих способов:

* При использовании пользовательского образа укажите идентификатор ресурса Azure Resource Manager для образа виртуальной машины. Идентификатор образа имеет такой формат пути, как показано в следующем примере:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Чтобы получить этот идентификатор образа на портале Azure, откройте **Все ресурсы**, выберите пользовательский образ и в разделе **Обзор** страницы образа скопируйте путь в поле **Идентификатор ресурса**.

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

Чтобы включить пул пакетной службы для выполнения рабочих нагрузок контейнера, необходимо указать параметр [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) в пуле объекта [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration).

Можно создать пул с включенным контейнером с предварительно загруженными образами контейнеров или без них, как показано в следующих примерах. Процесс извлечения (или предварительного получения) позволяет предварительно загрузить образы контейнеров из центра Docker или другого реестра контейнеров в Интернете. Преимущество предварительного получения образов контейнеров заключается в том, что при первом запуске задач не нужно ожидать скачивания образа контейнера. Конфигурация контейнера извлекает образы контейнеров на виртуальную машину при создании пула. Задачи, выполняющиеся в пуле, могут ссылаться на список образов контейнеров и параметры выполнения контейнера.



### <a name="pool-without-prefetched-container-images"></a>Пул без предварительно полученных образов контейнеров

Для настройки пула с включенным контейнером без предварительно полученных контейнерных образов определите объекты `ContainerConfiguration` и `VirtualMachineConfiguration`, как показано в следующем примере. В этом и следующих примерах подразумевается, что используется пользовательский образ Ubuntu 16.04 LTS с установленной подсистемой Docker.

```csharp
// Specify container configuration. This is required even though there are no prefetched images.
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

Для предварительной выборки образов контейнеров в пуле добавьте список образов контейнеров (`containerImageNames`) в `ContainerConfiguration` и присвойте этому списку имя. В следующем примере предполагается, что вы используете пользовательский образ Ubuntu 16.04 LTS и предварительно загружаете образ TensorFlow из [центра Docker](https://hub.docker.com). Данный пример включает задачу запуска, выполняемую на узле виртуальной машины на узлах пула. Можно запустить стартовую задачу в узле, например, для вставки файлового сервера, доступ к которому можно получить из контейнеров.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

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

Для запуска задач контейнера на вычислительных узлах необходимо указать параметры для конкретного контейнера, такие как варианты выполнения задачи, используемые образы и реестр.

Используйте свойство `ContainerSettings` в классах задач для настройки параметров конкретного контейнера. Эти параметры определяются классом [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

При запуске задач на образах контейнеров для [задач облака](/dotnet/api/microsoft.azure.batch.cloudtask) и [диспетчера заданий](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) требуются параметры контейнера. Тем не менее [задача запуска](/dotnet/api/microsoft.azure.batch.starttask), [задача подготовки задания](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) и [задача снятия задания](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) не требуют параметров контейнера (то есть их можно выполнить в контексте контейнера или напрямую на узле).

При настройке параметров для контейнеров все каталоги рекурсивно ниже `AZ_BATCH_NODE_ROOT_DIR` (корень каталогов пакетной службы Azure на узле) сопоставляются в контейнере, все переменные среды задач сопоставляются в контейнере, а командная строка задач выполняется в контейнере.

В примере кода из раздела [Предварительное получение образов для конфигурации контейнера](#prefetch-images-for-container-configuration) показано, как задать конфигурацию контейнера для задачи запуска. В следующем примере кода показано, как задать конфигурацию контейнера для задачи облака:

```csharp
// Simple container task command

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

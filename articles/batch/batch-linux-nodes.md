<properties
	pageTitle="Узлы Linux в пулах пакетной службы Azure | Microsoft Azure"
	description="Узнайте, как обрабатывать параллельные вычислительные рабочие нагрузки в пулах виртуальных машин Linux в пакетной службе Azure."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="na"
	ms.date="09/08/2016"
	ms.author="marsma" />

# Подготовка вычислительных узлов Linux в пулах пакетной службы Azure

Пакетная служба Azure позволяет выполнять параллельные вычислительные рабочие нагрузки на виртуальных машинах Linux и Windows. В этой статье описывается создание пулов вычислительных узлов Linux в пакетной службе с помощью клиентских библиотек [Python][py_batch_package] и [.NET][api_net].

> [AZURE.NOTE] [Application packages](batch-application-packages.md) сейчас не поддерживаются на вычислительных узлах Linux.

## Конфигурация виртуальной машины

При создании пула вычислительных узлов в пакетной службе есть два варианта для выбора размера узла и операционной системы: Cloud Services Configuration (Конфигурация облачных служб) и "Конфигурация виртуальной машины".

Параметр **Cloud Services Configuration** (Конфигурация облачных служб) предоставляет *только* вычислительные узлы Windows. Доступные размеры вычислительных узлов перечислены в статье [Размеры для облачных служб](../cloud-services/cloud-services-sizes-specs.md), а доступные операционные системы — в статье [Таблица совместимости выпусков гостевых ОС Azure и пакетов SDK](../cloud-services/cloud-services-guestos-update-matrix.md). При создании пула, содержащего узлы облачных служб Azure, необходимо указать только размер узла и его "семейство ОС", которые можно найти в упомянутых ранее статьях. Для пулов вычислительных узлов Windows чаще всего используются облачные службы.

Параметр **Конфигурация виртуальной машины** предоставляет образы Windows и Linux для вычислительных узлов. Доступные размеры вычислительных узлов перечислены в статьях [Размеры виртуальных машин в Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) и [Размеры виртуальных машин в Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). При создании пула, содержащего узлы конфигурации виртуальных машин, необходимо указать размер узлов, ссылку на образ виртуальной машины и номер SKU агента узла пакетной службы для установки на узлах.

### Ссылка на образ виртуальной машины

Для предоставления вычислительных узлов Linux пакетная служба использует [наборы для масштабирования виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Образы операционной системы для этих виртуальных машин доступны в [Azure Marketplace][vm_marketplace]. При настройке ссылки на образ виртуальной машины задаются свойства образа виртуальной машины из Marketplace. Приведенные ниже свойства являются обязательными при создании ссылки на образ виртуальной машины.

| **Свойства ссылки на образ** | **Пример** |
| ----------------- | ------------------------ |
| Издатель | Canonical |
| ПРЕДЛОЖЕНИЕ | UbuntuServer |
| SKU | 14\.04.4-LTS |
| Версия | последних |

> [AZURE.TIP] Дополнительные сведения об этих свойствах и о том, как получить список образов из Marketplace, см. в статье [Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Обратите внимание, что не все образы из Marketplace в настоящее время совместимы с пакетной службой. Дополнительные сведения см. в разделе [Номер SKU агента узла](#node-agent-sku).

### Номер SKU агента узла

Агент узла пакетной службы — это программа, которая выполняется на каждом узле в пуле и предоставляет интерфейс контроля и управления между узлом и пакетной службой. Существуют различные реализации агента узла, известные как номера SKU, для различных операционных систем. По существу, при создании конфигурации виртуальной машины сначала следует указать ссылку на образ виртуальной машины, а затем указать агент узла, который будет установлен на этом образе. Как правило, каждый номер SKU агента узла совместим с несколькими образами виртуальных машин. Вот несколько примеров номеров SKU агентов узлов:

* batch.node.ubuntu 14.04;
* batch.node.centos 7;
* batch.node.windows amd64

> [AZURE.IMPORTANT] Не все образы виртуальных машин, доступные в Marketplace, совместимы с доступными на данный момент агентами узлов пакетной службы. Для получения списка доступных номеров SKU агентов узлов и образов виртуальных машин, с которыми они совместимы, необходимо использовать пакеты SDK пакетной службы. Дополнительные сведения см. далее в этой статье в разделе [Список образов виртуальных машин](#list-of-virtual-machine-images).

## Создание пула Linux: библиотека Python для пакетной службы

В следующем фрагменте кода показан пример создания пула вычислительных узлов Ubuntu Server с помощью [клиентской библиотеки пакетной службы Microsoft Azure для Python][py_batch_package]. Справочную документацию по модулю Python пакетной службы можно найти в пакете [azure.batch package ][py_batch_docs] на сайте Read the Docs.

В этом фрагменте кода явно создается объект [ImageReference][py_imagereference] и указывается каждое из его свойств (publisher, offer, SKU, version). Однако в рабочем коде рекомендуется использовать метод [list\_node\_agent\_skus][py_list_skus] для определения и выбора доступных сочетаний образа и номера SKU агента узла в среде выполнения.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Как упоминалось ранее, вместо явного создания объекта [ImageReference][py_imagereference] мы рекомендуем использовать метод [list\_node\_agent\_skus][py_list_skus] для динамического выбора из поддерживаемых в настоящее время сочетаний агента узла и образа из Marketplace. Следующий фрагмент кода Python демонстрирует использование этого метода.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## Создание пула Linux: библиотека .NET для пакетной службы

В следующем фрагменте кода показан пример создания пула вычислительных узлов Ubuntu Server с помощью клиентской библиотеки [.NET для пакетной службы][nuget_batch_net]. [Справочную документацию по .NET для пакетной службы][api_net] можно найти на сайте MSDN.

В следующем фрагменте кода используется метод [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] для выбора из списка поддерживаемых в настоящее время сочетаний образа из Marketplace и номера SKU агента узла. Этот способ является предпочтительным, так как список поддерживаемых сочетаний может меняться время от времени. Чаще всего добавляются поддерживаемые сочетания.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Несмотря на то, что в приведенном ранее фрагменте используется метод [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] для динамического перечисления и выбора поддерживаемых сочетаний образа и номера SKU агента узла (рекомендуемый), можно также настроить объект [ImageReference][net_imagereference] явным образом:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## Список образов виртуальных машин

В следующей таблице указаны образы виртуальных машин из Marketplace, которые совместимы с доступными агентами узлов пакетной службы на момент написания этой статьи. Важно отметить, что этот список не является окончательным, так как в любое время образы и агенты узлов могут добавляться или удаляться. Рекомендуем всегда использовать в службах и приложениях пакетной службы методы [list\_node\_agent\_skus][py_list_skus] \(Python) и [ListNodeAgentSkus][net_list_skus] \(.NET для пакетной службы) для определения и выбора доступных в настоящее время номеров SKU.

> [AZURE.WARNING] Следующий список может меняться в любое время. При выполнении заданий пакетной службы следует всегда использовать методы **получения списка номеров SKU агентов узлов**, доступные в API-интерфейсах пакетной службы, для перечисления и выбора совместимых виртуальных машин и номеров SKU агентов узлов.

| **Издатель** | **ПРЕДЛОЖЕНИЕ** | **Номер SKU образа** | **Версия** | **Идентификатор SKU агента узла** |
| ------- | ------- | ------- | ------- | ------- |
| Canonical | UbuntuServer | 14\.04.0-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.1-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.2-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.3-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.4-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.5-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 16\.04.0-LTS | последних | batch.node.ubuntu 16.04 |
| Credativ | Debian | 8 | последних | batch.node.debian 8 |
| OpenLogic | CentOS | 7\.0 | последних | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.1. | последних | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7\.1. | последних | batch.node.centos 7 |
| OpenLogic | CentOS | 7,2 | последних | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7\.0 | последних | batch.node.centos 7 |
| SUSE | openSUSE | 13\.2 | последних | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42\.1 | последних | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12 | последних | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | последних | batch.node.opensuse 42.1 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | последних | batch.node.windows amd64 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | последних | batch.node.centos 7 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | последних | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | последних | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | последних | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | последних | batch.node.windows amd64 |

## Подключение к узлам Linux

Во время разработки или устранения неполадок может потребоваться войти на узлы в пуле. В отличие от вычислительных узлов Windows, для подключения к узлам Linux нельзя использовать протокол удаленного рабочего стола (RDP). Вместо этого пакетная служба включает доступ по протоколу SSH на каждом узле для удаленного подключения.

В следующем фрагменте кода Python создается пользователь на каждом узле пула, который необходим для удаленного подключения. Затем выводятся сведения о подключении SSH для каждого узла.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Ниже приведен пример выходных данных вышеупомянутого кода для пула, содержащего четыре узла Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Обратите внимание, что при создании пользователя на узле вместо пароля можно указать открытый ключ SSH. В пакете SDK для Python это делается с помощью параметра **ssh\_public\_key** в [ComputeNodeUser][py_computenodeuser]. В среде .NET — с помощью свойства [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## Цены

Пакетная служба Azure основана на технологии виртуальных машин Azure и облачных служб Azure. Сама пакетная служба предоставляется бесплатно. Это означает, что плата взимается только за вычислительные ресурсы, используемые решениями пакетной службы. При выборе режима **Cloud Services Configuration** (Конфигурация облачных служб) плата будет взиматься в зависимости от структуры [цен на облачные службы][cloud_services_pricing]. При выборе режима **Конфигурация виртуальной машины** плата будет взиматься в зависимости от структуры [цен на виртуальные машины][vm_pricing].

## Дальнейшие действия

### Учебник по пакетной службе (Python)

Более подробные инструкции по работе с пакетной службой с использованием Python см. в статье [Приступая к работе с клиентом Python пакетной службы Azure](batch-python-tutorial.md). Сопутствующий [пример кода][github_samples_pyclient] включает в себя вспомогательную функцию `get_vm_config_for_distro`, которая предоставляет другой метод получения конфигурации виртуальной машины.

### Примеры кода Python для пакетной службы

Ознакомьтесь с другими [примерами кода Python][github_samples_py] в репозитории [azure-batch-samples][github_samples] на портале GitHub для нескольких сценариев, в которых показано, как выполнять распространенные пакетные операции, такие как создание пула, задания и задачи. Файл [README][github_py_readme], прилагаемый к примерам кода Python, содержит подробные сведения об установке необходимых пакетов.

### Форум по Пакетной службе

На [форуме по пакетной службе Azure][forum] на сайте MSDN можно обсудить пакетную службу и задать вопросы о ней. Изучайте полезные "прикрепленные" публикации и задавайте вопросы, возникающие во время сборки решений пакетной службы.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/ru-RU/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

<!---HONumber=AcomDC_0914_2016-->
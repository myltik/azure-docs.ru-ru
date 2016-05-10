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
	ms.date="04/19/2016"
	ms.author="marsma" />

# Подготовка вычислительных узлов Linux в пулах пакетной службы Azure

Пакетная служба Azure позволяет выполнять параллельные вычислительные рабочие нагрузки на виртуальных машинах Linux и Windows. В этой статье описывается создание пулов вычислительных узлов Linux в пакетной службе с помощью клиентских библиотек [Python][py_batch_package] и [.NET][api_net] для пакетной службы.

> [AZURE.NOTE] Поддержка Linux в пакетной службе в настоящее время находится в статусе предварительной версии. Некоторые аспекты описанной здесь функции могут быть изменены до выхода общедоступной версии. [Пакеты приложений](batch-application-packages.md) и [задачи с несколькими экземплярами](batch-mpi.md) **в настоящее время не поддерживаются** на вычислительных узлах Linux.

## Конфигурация виртуальной машины

При создании пула вычислительных узлов в пакетной службе есть два варианта для выбора размера узла и операционной системы: **Cloud Services Configuration** (Конфигурация облачных служб) и **Конфигурация виртуальной машины**.

Параметр **Cloud Services Configuration** (Конфигурация облачных служб) предоставляет *только* вычислительные узлы Windows. Доступные размеры вычислительных узлов перечислены в статье [Размеры для облачных служб](../cloud-services/cloud-services-sizes-specs.md), а доступные операционные системы — в статье [Таблица совместимости выпусков гостевых ОС Azure и пакетов SDK](../cloud-services/cloud-services-guestos-update-matrix.md). При создании пула, содержащего узлы облачных служб, необходимо указать только размер узла и его "семейство ОС", которые можно найти в этих статьях. При создании пулов вычислительных узлов Windows чаще всего используются облачные службы.

Параметр **Конфигурация виртуальной машины** предоставляет образы Windows и Linux для вычислительных узлов. Доступные размеры вычислительных узлов перечислены в статье [Размеры виртуальных машин в Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) и [Размеры виртуальных машин в Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). При создании пула, содержащего узлы конфигурации виртуальных машин, необходимо указать не только размер узлов, но и **ссылку на образ виртуальной машины** и **номер SKU агента узла** пакетной службы для установки на узлах.

### Ссылка на образ виртуальной машины

Пакетная служба скрыто использует [наборы для масштабирования виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) для предоставления вычислительных узлов Linux, а образы операционных систем для этих виртуальных машин предоставляются [магазином Marketplace виртуальных машин Azure][vm_marketplace]. При настройке ссылки на образ виртуальной машины задаются свойства образа виртуальной машины из Marketplace. Приведенные ниже свойства являются обязательными при создании ссылки на образ виртуальной машины.

| **Свойства ссылки на образ** | **Пример** |
| ----------------- | ------------------------ |
| Издатель | Canonical |
| ПРЕДЛОЖЕНИЕ | UbuntuServer |
| SKU | 14\.04.4-LTS |
| Версия | последних |

> [AZURE.TIP] Дополнительные сведения об этих свойствах и о том, как получить список образов из Marketplace, см. в статье [Просмотр и выбор образов виртуальных машин Linux в Azure с помощью интерфейса командной строки или оболочки PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Обратите внимание, что не все образы из Marketplace в настоящее время совместимы с пакетной службой — см. раздел [Номер SKU агента узла](#node-agent-sku) ниже.

### Номер SKU агента узла

Агент узла пакетной службы — это программа, которая выполняется на каждом узле в пуле и предоставляет интерфейс контроля и управления между узлом и пакетной службой. Существуют различные реализации агента узла, известные как номера SKU, для различных операционных систем. По существу при создании конфигурации виртуальной машины сначала следует указать ссылку на образ виртуальной машины, а затем указать, какой агент узла будет установлен на этом образе. Как правило, каждый номер SKU агента узла совместим с несколькими образами виртуальных машин. Вот несколько примеров номеров SKU агентов узлов:

* batch.node.ubuntu 14.04;
* batch.node.centos 7;
* batch.node.windows amd64.

> [AZURE.IMPORTANT] Не все образы виртуальных машин, доступные в Marketplace, совместимы с доступными на данный момент агентами узлов пакетной службы. Для получения списка доступных номеров SKU агентов узлов и образов виртуальных машин, с которыми они совместимы, необходимо использовать пакеты SDK пакетной службы. Дополнительные сведения см. ниже в разделе [Список образов виртуальных машин](#list-of-virtual-machine-images).

## Создание пула Linux: библиотека Python для пакетной службы

В следующем фрагменте кода показано создание пула вычислительных узлов Ubuntu Server с помощью [клиентской библиотеки пакетной службы Microsoft Azure для Python][py_batch_package]. Справочную документацию по модулю Python пакетной службы можно найти в пакете[azure.batch package ][py_batch_docs] на сайте Read the Docs.

В этом фрагменте кода мы явно создаем объект [ImageReference][py_imagereference], указывая каждое из его свойств (publisher, offer, sku, version). Однако в рабочем коде рекомендуется использовать метод [list\_node\_agent\_skus][py_list_skus] для определения и выбора доступных сочетаний образа и номера SKU агента узла в среде выполнения.

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

Как упоминалось выше, мы рекомендуем вместо явного создания объекта [ImageReference][py_imagereference] использовать метод [list\_node\_agent\_skus][py_list_skus] для динамического выбора из поддерживаемых в настоящее время сочетаний агента узла и образа из Marketplace. Следующий фрагмент кода Python демонстрирует использование этого метода.

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

В следующем фрагменте кода показано создание пула вычислительных узлов Ubuntu Server с помощью клиентской библиотеки [.NET для пакетной службы][nuget_batch_net]. [Справочную документацию по .NET для пакетной службы][api_net] можно найти на сайте MSDN.

В приведенном ниже фрагменте кода используется метод [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] для выбора из списка поддерживаемых в настоящее время сочетаний образа из Marketplace и номера SKU агента узла. Этот способ является предпочтительным, так как список поддерживаемых сочетаний может меняться время от времени (чаще всего добавляются поддерживаемые сочетания).

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

Несмотря на то что в приведенном выше фрагменте используется метод [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] для динамического перечисления и выбора поддерживаемых сочетаний образа и номера SKU агента узла (рекомендуемый), можно также настроить объект [ImageReference][net_imagereference] явным образом:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## Список образов виртуальных машин

В таблице ниже указано, какие образы виртуальных машин из Marketplace совместимы с доступными агентами узлов пакетной службы **на момент написания этой статьи**. Важно отметить, что этот список не является окончательным, так как в любое время образы и агенты узлов могут добавляться или удаляться. Рекомендуем всегда использовать в службах и приложениях пакетной службы методы [list\_node\_agent\_skus][py_list_skus] (Python) и [ListNodeAgentSkus][net_list_skus] (.NET для пакетной службы) для определения и выбора доступных в настоящее время номеров SKU.

> [AZURE.WARNING] Приведенный ниже список может измениться в любой момент. При выполнении заданий пакетной службы следует всегда использовать методы **получения списка номеров SKU агентов узлов**, доступные в API-интерфейсах пакетной службы, для перечисления и выбора совместимых виртуальных машин и номеров SKU агентов узлов.

| **Издатель** | **ПРЕДЛОЖЕНИЕ** | **Номер SKU образа** | **Версия** | **Идентификатор SKU агента узла** |
| ------- | ------- | ------- | ------- | ------- |
| Canonical | UbuntuServer | 14\.04.0-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.1-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.2-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.3-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.4-LTS | последних | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 15\.10 | последних | batch.node.debian 8 |
| Credativ | Debian | 8 | последних | batch.node.debian 8 |
| OpenLogic | CentOS | 7\.0 | последних | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.1. | последних | batch.node.centos 7 |
| OpenLogic | CentOS | 7,2 | последних | batch.node.centos 7 |
| Oracle | Oracle-Linux-7 | OL70 | последних | batch.node.centos 7 |
| SUSE | SLES | 12 | последних | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | последних | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12 | последних | batch.node.opensuse 42.1 |
| SUSE | openSUSE | 13\.2 | последних | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42\.1 | последних | batch.node.opensuse 42.1 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | последних | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | последних | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | последних | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | последних | batch.node.windows amd64 |

## Подключение к узлам Linux

Во время разработки или устранения неполадок может потребоваться вход на узлы в пуле. В отличие от вычислительных узлов Windows, для подключения к узлам Linux нельзя использовать протокол удаленного рабочего стола (RDP). Вместо этого пакетная служба включает доступ по протоколу SSH на каждом узле для удаленного подключения.

В следующем фрагменте кода Python создается пользователь на каждом узле пула (что требуется для удаленного подключения), а затем выводятся сведения о подключении по протоколу SSH для каждого узла.

```python
import getpass

# Specify the username and prompt for a password
username = "linuxuser"
password = getpass.getpass()

# Create the user that will be added to each node
# in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = client.compute_node.get_remote_login_settings(pool_id,
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

Обратите внимание, что, создавая пользователя на узле, вместо пароля можно указать открытый ключ SSH. В пакете SDK для Python это делается с помощью параметра **ssh\_public\_key** в [ComputeNodeUser][py_computenodeuser], а в среде .NET — с помощью свойства [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## Цены

Пакетная служба Azure основана на технологии виртуальных машин Azure и облачных служб Azure. Сама пакетная служба предоставляется бесплатно. Это означает, что плата взимается только за вычислительные ресурсы, используемые решениями пакетной службы. При выборе режима **Cloud Services Configuration** (Конфигурация облачных служб) плата будет взиматься в зависимости от структуры [цен на облачные службы][cloud_services_pricing]. При выборе режима **Конфигурация виртуальной машины** плата будет взиматься в зависимости от структуры [цен на виртуальные машины][vm_pricing].

## Дальнейшие действия

### Примеры кода Python для пакетной службы

Ознакомьтесь с [примерами кода Python][github_samples_py] в репозитории [azure-batch-samples][github_samples] на портале GitHub для нескольких скриптов, в которых показано, как выполнять распространенные пакетные операции, такие как создание пула, задания и задачи, и многое другое. В файле [README][github_py_readme], прилагаемом к примерам кода Python, содержатся подробные сведения об установке необходимых пакетов.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Общая схема: пакеты приложений"

<!---HONumber=AcomDC_0427_2016-->
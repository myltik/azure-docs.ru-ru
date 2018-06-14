---
title: Создание виртуальных машин Windows в Azure с помощью Python и управление ими | Документация Майкрософт
description: Сведения об использовании Python для создания виртуальных машин Windows в Azure с помощью Python и управления ими.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: dbe8f1603433f381c3c28cb47d2dbda543b462e0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528347"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Развертывание виртуальной машины Azure с помощью Python

[Виртуальной машине Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) требуется несколько вспомогательных ресурсов Azure. В этой статье описывается создание, управление и удаление ресурсов виртуальной машины с помощью Python. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание проекта Visual Studio
> * Установка пакетов.
> * Создание учетных данных
> * Создание ресурсов.
> * Выполнение задач управления.
> * Удаление ресурсов.
> * Выполнение приложения

На выполнение этих действий требуется примерно 20 минут.

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Если вы этого еще не сделали, установите [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). На странице рабочих нагрузок выберите **Разработка на Python** и нажмите кнопку **Установить**. В сводке вы увидите, что вариант **64-разрядная версия Python 3 (3.6.0)** выберется автоматически. Если вы уже установили Visual Studio, можно добавить рабочую нагрузку Python с помощью средства запуска Visual Studio.
2. После установки и запуска Visual Studio щелкните **Файл** > **Создать** > **Проект**.
3. В разделе **Шаблоны** > **Python** > **Приложение Python** укажите имя *myPythonProject* и расположение проекта, а затем нажмите кнопку **ОК**.

## <a name="install-packages"></a>Установка пакетов

1. В обозревателе решений в разделе *myPythonProject* щелкните правой кнопкой мыши **Python Environments** (Среды Python) и выберите **Add virtual environment** (Добавление виртуальной среды).
2. На странице добавления виртуальной среды примите имя по умолчанию *env*, убедитесь, что в качестве базового интерпретатора выбран *Python 3.6 (64-разрядная версия)* и нажмите кнопку **Создать**.
3. Щелкните правой кнопкой мыши созданную среду *env*, затем щелкните **Install Python Package** (Установить пакет Python), введите *azure* в поле поиска и нажмите клавишу ВВОД.

В окнах вывода вы увидите, что пакеты Аzure успешно установлены. 

## <a name="create-credentials"></a>Создание учетных данных

Прежде чем выполнить этот шаг, убедитесь, что у вас есть [субъект-служба Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Кроме того, необходимо записать идентификатор приложения, ключ проверки подлинности и идентификатор клиента, которые понадобятся позже.

1. Откройте созданный файл *myPythonProject.py*, а затем добавьте следующий код, который позволит запускать приложение:

    ```python
    if __name__ == "__main__":
    ```

2. Чтобы импортировать необходимый код, добавьте эти операторы в начало PY-файла:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Затем в PY-файле добавьте переменные после операторов импорта, чтобы указать общие значения, используемые в коде:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Замените **subscription-id** идентификатором вашей подписки.

4. Чтобы создать учетные данные Active Directory, необходимые для выполнения запросов, добавьте эту функцию после переменных в PY-файле:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Замените **application-id**, **authentication-key** и **tenant-id** значениями, записанными при создании субъекта-службы Azure Active Directory.

5. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Создание ресурсов
 
### <a name="initialize-management-clients"></a>Создание клиентов управления

Клиенты управления необходимы для создания ресурсов и управления ими с помощью пакета SDK Python в Azure. Чтобы создать клиенты управления, добавьте этот код после оператора **if** в конце PY-файла:

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Создание виртуальной машины и ресурсов поддержки

Все ресурсы должны содержаться в [группе ресурсов](../../azure-resource-manager/resource-group-overview.md).

1. Чтобы создать группу ресурсов, добавьте эту функцию после переменных в PY-файле:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Группы доступности](tutorial-availability-sets.md) упрощают обслуживание виртуальных машин, используемых приложением.

1. Чтобы создать группу доступности, добавьте эту функцию после переменных в PY-файле:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

[Общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) необходим для взаимодействия с виртуальной машиной.

1. Чтобы создать общедоступный IP-адрес для виртуальной машины, добавьте эту функцию после переменных в PY-файле:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Виртуальная машина должна быть в подсети [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

1. Чтобы создать виртуальную сеть, добавьте эту функцию после переменных в PY-файле:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Чтобы добавить подсеть к виртуальной сети, добавьте эту функцию после переменных в PY-файле:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Для обмена данными в виртуальной сети виртуальной машине нужен сетевой интерфейс.

1. Чтобы создать сетевой интерфейс, добавьте эту функцию после переменных в PY-файле:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Теперь, когда вы создали все вспомогательные ресурсы, можно создать виртуальную машину.

1. Чтобы создать виртуальную машину, добавьте эту функцию после переменных в PY-файле:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > В этом учебнике создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Выполнение задач управления.

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать код для автоматизации повторяющихся или сложных задач.

### <a name="get-information-about-the-vm"></a>Получение информации о виртуальной машине

1. Чтобы получить сведения о виртуальной машине, добавьте эту функцию после переменных в PY-файле:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Остановка виртуальной машины

Вы можете остановить виртуальную машину, сохранив все ее настройки (при этом за нее будет продолжать взиматься плата) или остановить ее и отменить ее распределение. При этом освобождаются все ресурсы, связанные с ней, а также прекращается выставление счетов за эту виртуальную машину.

1. Чтобы остановить виртуальную машину без ее освобождения, добавьте эту функцию после переменных в PY-файле:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Если вы хотите отменить распределение виртуальной машины, измените вызов power_off на следующий код:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Запуск виртуальной машины

1. Чтобы запустить виртуальную машину, добавьте эту функцию после переменных в PY-файле:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Изменение размера виртуальной машины

При выборе размера виртуальной машины необходимо учесть многие аспекты развертывания. Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](sizes.md).

1. Чтобы изменить размер виртуальной машины, добавьте эту функцию после переменных в PY-файле:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Добавление диска данных в виртуальную машину

Виртуальные машины могут иметь один или несколько [дисков данных](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), которые хранятся на виртуальных жестких дисках.

1. Чтобы добавить диск данных в виртуальную машину, добавьте эту функцию после переменных в PY-файле: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Удаление ресурсов.

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Если вы хотите удалить виртуальные машины и все вспомогательные ресурсы, достаточно удалить группу ресурсов.

1. Чтобы удалить группу ресурсов и все ресурсы, добавьте эту функцию после переменных в PY-файле.
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код после оператора **if** в конце PY-файла:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Сохраните *myPythonProject.py*.

## <a name="run-the-application"></a>Выполнение приложения

1. Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio.

2. Нажмите клавишу **ВВОД** после возвращения сведений о состоянии каждого ресурса. В сведениях о состоянии должно отобразиться состояние подготовки **Успешно**. После создания виртуальной машины вы можете удалить все созданные ресурсы. Прежде чем нажать клавишу **ВВОД** и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы. Если у вас открыт портал Azure, может потребоваться обновить колонку, чтобы увидеть новые ресурсы.  

    На полное выполнение этого консольного приложения потребуется примерно 5 минут. После закрытия приложения на удаление ресурсов и групп ресурсов может потребоваться несколько минут.


## <a name="next-steps"></a>Дополнительная информация

- При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении неполадок развертываний групп ресурсов с помощью портала Azure](../../resource-manager-troubleshoot-deployments-portal.md).
- Узнайте больше о [библиотеке Azure для Python](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python).


---
title: Использование API управления службами (Python) — руководство по функциям
description: Узнайте, как программными средствами Python реализовать общие задачи управления службами.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: b89f1aad46621d35728934ea068a5893ba674094
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124805"
---
# <a name="use-service-management-from-python"></a>Использование управления службами в Python
В этом руководстве показано, как программными средствами Python выполнять общие задачи управления службами. Класс **ServiceManagementService** в [пакете SDK Azure для Python](https://github.com/Azure/azure-sdk-for-python) поддерживает программный доступ к большинству функций, связанных с управлением службами, которые доступны на [портале Azure][management-portal]. Эти функции можно использовать для создания, обновления и удаления облачных служб, развернутых служб, служб управления данными и виртуальных машин. Эта возможность может быть полезна при создании приложений, которым требуется программный доступ к управлению службами.

## <a name="WhatIs"> </a>Что такое управление службами?
API управления службами Azure обеспечивает программный доступ к большинству функций управления службами, доступных на [портале Azure][management-portal]. Пакет Azure SDK для Python можно использовать для управления облачными службами и учетными записями хранения.

Чтобы использовать API управления службами, необходимо [создать учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Основные понятия
Пакет SDK Azure для Python служит оболочкой для [API управления службами][svc-mgmt-rest-api], который является интерфейсом REST API. Все операции API выполняются по SSL и проходят взаимную проверку подлинности с помощью сертификатов X.509 v3. Доступ к службе управления можно получить из службы, работающей в Azure, или непосредственно через Интернет из любого приложения, которое может отправить HTTPS-запрос и получить HTTPS-ответ.

## <a name="Installation"> </a>Установка
Все функции, описанные в этой статье, доступны в пакете `azure-servicemanagement-legacy`, который можно установить с помощью pip. Дополнительные сведения об установке (например, если вы не работали с Python) см. в статье: [Установка Python и пакета SDK](../python-how-to-install.md).

## <a name="Connect"> </a> Подключение к управлению службами
Чтобы подключиться к конечной точке управления службами, необходимо указать идентификатор подписки Azure и допустимый сертификат управления. Идентификатор подписки можно получить на [портале Azure][management-portal].

> [!NOTE]
> Теперь можно использовать сертификаты, созданные OpenSSL при работе в ОС Windows. Требуется Python версии 2.7.4 или более поздней. Корпорация Майкрософт рекомендует использовать OpenSSL вместо PFX-сертификатов, потому что поддержка PFX-сертификатов, скорее всего, будет удалена в будущем.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Сертификаты управления в Windows/Mac/Linux (OpenSSL)
Можно также использовать [OpenSSL](http://www.openssl.org/) для создания сертификата управления. Необходимо создать два сертификата: один для сервера (`.cer`-файл) и один для клиента (`.pem`-файл). Чтобы создать `.pem` -файл, выполните следующую команду.

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Чтобы создать `.cer` -файл сертификата, выполните следующую команду.

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Дополнительные сведения о сертификатах Azure см. в статье [Certificates overview for Azure Cloud Services](cloud-services-certs-create.md) (Общие сведения о сертификатах для облачных служб Azure). Полное описание параметров OpenSSL см. в документации по адресу [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

После создания этих файлов отправьте в Azure файл `.cer`. На [портале Azure][management-portal] на вкладке **Параметры** выберите **Отправить**. Обратите внимание на то, где был сохранен файл `.pem`.

После получения идентификатора подписки, создания сертификата и отправки файла `.cer` в Azure можно подключиться к конечной точке управления Azure. Подключение достигается благодаря передаче объекту **ServiceManagementService** идентификатора подписки и пути к файлу `.pem`.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

В предыдущем примере `sms` является объектом **ServiceManagementService** . Класс **ServiceManagementService** – это основной класс, используемый для управления службами Asure.

### <a name="management-certificates-on-windows-makecert"></a>Управление сертификатами в Windows (MakeCert)
Вы можете создать самозаверяющий сертификат управления на своем компьютере с помощью программы `makecert.exe`. Откройте **окно командной строки Visual Studio** от имени **администратора** и используйте следующую команду, заменив *AzureCertificate* на имя сертификата, которое необходимо использовать.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Эта команда создает `.cer`-файл и устанавливает его в **личное** хранилище сертификатов. Дополнительные сведения см. в статье [Certificates overview for Azure Cloud Services](cloud-services-certs-create.md) (Общие сведения о сертификатах для облачных служб Azure).

После создания сертификата отправьте в Azure файл `.cer`. На [портале Azure][management-portal] на вкладке **Параметры** выберите **Отправить**.

После получения идентификатора подписки, создания сертификата и отправки файла `.cer` в Azure можно подключиться к конечной точке управления Azure. Подключитесь, передав идентификатор подписки и расположение сертификата из **личного** хранилища сертификатов в **ServiceManagementService** (снова замените *AzureCertificate* именем вашего сертификата).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

В предыдущем примере `sms` является объектом **ServiceManagementService** . Класс **ServiceManagementService** – это основной класс, используемый для управления службами Asure.

## <a name="ListAvailableLocations"> </a>Получение списка доступных расположений
Чтобы получить список расположений, доступных для размещения служб, используйте метод **list\_locations**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

При создании облачной службы или службы хранения необходимо указать допустимое расположение. Метод **list\_locations** всегда возвращает текущий список доступных расположений. На момент написания этой статьи были доступны следующие расположения:

* Западная Европа
* Северная Европа
* Юго-Восточная Азия
* Восточная Азия
* Центральный регион США
* Северо-центральный регион США
* Южно-центральный регион США
* Запад США
* Восток США
* Восточная часть Японии
* Западная часть Японии
* Южная Бразилия
* Восточная часть Австралии
* Юго-Восточная часть Австралии

## <a name="CreateCloudService"> </a>Создание облачной службы
При создании приложения и его запуске в Azure код и настройки вместе называются [облачной службой][cloud service] Azure. (В более ранних выпусках Azure она была известна как *размещенная служба*.) Чтобы создать новую размещенную службу, можно использовать метод **create\_hosted\_service**. При этом укажите для нее имя (которое должно быть уникальным в Azure), метку (автоматически кодируется в base64), описание и расположение.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Вы можете создать список всех размещенных служб для вашей подписки при помощи метода **create\_hosted\_service**.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Чтобы получить сведения о конкретной размещенной службе, передайте имя размещенной службы в метод **get\_hosted\_service\_properties**.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

После того, как облачная служба была создана, разверните свой код в службе с помощью метода **create\_deployment**.

## <a name="DeleteCloudService"> </a>Удаление облачной службы
Облачную службу можно удалить, передав ее в метод **delete\_hosted\_service**.

    sms.delete_hosted_service('myhostedservice')

Прежде чем удалить службу, необходимо сначала удалить все ее развернутые службы. Дополнительные сведения см. в разделе [Практическое руководство. Удаление развертывания](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Удаление развертывания
Чтобы удалить развертывание, используйте метод **delete\_deployment**. В следующем примере показано, как удалить развертывание с именем `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Создание службы хранения
Служба [хранения](../storage/common/storage-create-storage-account.md) предоставляет доступ к [большим двоичным объектам](../storage/blobs/storage-python-how-to-use-blob-storage.md), [таблицам](../cosmos-db/table-storage-how-to-use-python.md) и [запросам](../storage/queues/storage-python-how-to-use-queue-storage.md) Azure. Для создания службы хранения требуется указать ее имя (длиной от 3 до 24 символов нижнего регистра, оно должно быть уникальным в пределах Azure). Также необходимо добавить описание, метку (не более 100 символов, автоматически кодируется в base64) и расположение службы. В следующем примере показано, как создать службу хранения путем указания расположения.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

В описанном выше примере состояние операции **create\_storage\_account** можно извлекать путем передачи результатов, возвращенных **create\_storage\_account**, в метод **get\_operation\_status**. 

Можно вывести список ваших учетных записей хранения и их свойств с помощью метода **list\_storage\_accounts**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Удаление службы хранения
Чтобы удалить службу хранения, передайте ее в метод **delete\_storage\_account**. Удаление службы хранилища приводит к удалению всех данных, хранящихся в службе (больших двоичных объектов, таблиц и очередей).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Перечисление доступных операционных систем
Чтобы получить список операционных систем, доступных для служб размещения, используйте метод **list\_operating\_systems**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Кроме того, можно использовать метод **list\_operating\_system\_families**, который группирует операционные системы по семействам.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Создание образа операционной системы
Чтобы добавить образ операционной системы в репозиторий образов, используйте метод **add\_os\_image**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Чтобы получить список доступных образов операционных систем, используйте метод **list\_os\_images**. Сюда входят все образы платформ и пользовательские образы.

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Удаление образа операционной системы
Для удаления пользовательского образа используйте метод **delete\_os\_image**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Создание виртуальной машины
Чтобы создать виртуальную машину, необходимо сначала создать [облачную службу](#CreateCloudService). После этого создайте развертывание виртуальной машины, с помощью метода **create\_virtual\_machine\_deployment**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Удаление виртуальной машины
Чтобы удалить виртуальную машину, необходимо сначала удалить развертывание с помощью метода **delete\_deployment**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Затем можно удалить облачную службу с помощью метода **delete\_hosted\_service**.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Создание виртуальной машины из записанного образа виртуальной машины
Чтобы записать образ виртуальной машины, сначала вызовите метод **capture\_vm\_image**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Чтобы убедиться, что образ записан успешно, используйте API **list\_vm\_images**. Убедитесь, что выбранный образ отображается в результатах.

    images = sms.list_vm_images()

Наконец, чтобы создать виртуальную машину с помощью записанного образа, используйте метод **create\_virtual\_machine\_deployment** как раньше, но на этот раз передайте vm_image_name.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Дополнительные сведения о записи образа виртуальной машины Linux в классической модели развертывания см. в статье [Запись классической виртуальной машины Linux в виде образа](../virtual-machines/linux/classic/capture-image-classic.md).

Дополнительные сведения о записи образа виртуальной машины Windows в классической модели развертывания см. в статье [Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Дальнейшие действия
Теперь, когда вы ознакомились с основами службы управления, можно приступить к [полной справочной документации по API для пакета SDK Azure Python](http://azure-sdk-for-python.readthedocs.org/) и легко выполнять сложные задачи, управляя своим приложением Python.

Дополнительную информацию можно найти в [Центре разработчика Python](/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/

<properties urlDisplayName="Service Management" pageTitle="Использование API  для управления службами (Python) &mdash; руководство по функциям" metaKeywords="" description="Узнайте, как программными средствами Python реализовать общие задачи управления службами." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Использование управления службами в Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Использование управления службами в Python

В этом руководстве показано, как программными средствами Python реализовать общие задачи управления службами. Класс **ServiceManagementService** в [Azure SDK для Python][Azure SDK для Python] поддерживает программный доступ к большинству функциональных возможностей, связанных с управлением службами, которые доступны на [портале управления][портале управления] (например, **создание, обновление и удаление облачных служб, развертываний, служб управления данными, виртуальных машин и территориальными группами**). Эта возможность может быть полезна при создании приложений, которым требуется программный доступ к управлению службами.

## Оглавление

-   [Что такое управление службами][Что такое управление службами]
-   [Основные понятия][Основные понятия]
-   [Практическое руководство. Подключение к управлению службами][Практическое руководство. Подключение к управлению службами]
-   [Практическое руководство. Список доступных местоположений][Практическое руководство. Список доступных местоположений]
-   [Практическое руководство. Создание облачной службы][Практическое руководство. Создание облачной службы]
-   [Практическое руководство. Удаление облачной службы][Практическое руководство. Удаление облачной службы]
-   [Практическое руководство. Создание развертывания][Практическое руководство. Создание развертывания]
-   [Практическое руководство. Обновление развертывания][Практическое руководство. Обновление развертывания]
-   [Практическое руководство. Перемещение развертываний из промежуточного хранения в рабочее развертывание][Практическое руководство. Перемещение развертываний из промежуточного хранения в рабочее развертывание]
-   [Практическое руководство. Удаление развертывания][Практическое руководство. Удаление развертывания]
-   [Практическое руководство. Создание службы хранения][Практическое руководство. Создание службы хранения]
-   [Практическое руководство. Удаление службы хранения][Практическое руководство. Удаление службы хранения]
-   [Практическое руководство. Создание территориальной группы][Практическое руководство. Создание территориальной группы]
-   [Практическое руководство. Удаление территориальной группы][Практическое руководство. Удаление территориальной группы]
-   [Практическое руководство. Список доступных операционных систем][Практическое руководство. Список доступных операционных систем]
-   [Практическое руководство. Создание образа операционной системы][Практическое руководство. Создание образа операционной системы]
-   [Практическое руководство. Удаление образа операционной системы][Практическое руководство. Удаление образа операционной системы]
-   [Практическое руководство. Создание виртуальной машины][Практическое руководство. Создание виртуальной машины]
-   [Практическое руководство. Удаление виртуальной машины][Практическое руководство. Удаление виртуальной машины]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="WhatIs"> </a>Что такое управление службами

API управления службой обеспечивает программный доступ к большинству функциональных возможностей управления службами, доступных через [портал управления][портале управления]. Пакет Azure SDK для Python позволяет управлять облачными службами, учетными записями хранения и территориальными группами.

Чтобы использовать API-интерфейс управления службами, необходимо [создать учетную запись Azure][создать учетную запись Azure].

## <a name="Concepts"> </a>Основные понятия

Пакет Azure SDK для Python служит оболочкой для [API-интерфейса управления службами Azure][API-интерфейса управления службами Azure], который является интерфейсом REST API. Все операции API выполняются по SSL и проходят взаимную проверку подлинности с использованием сертификатов X.509 v3. Доступ к службе управления можно получить из службы, работающей в Azure или непосредственно через Интернет из любого приложения, которое может отправить HTTPS-запрос и получить HTTPS-ответ.

## <a name="Connect"> </a>Практическое руководство. Подключение к управлению службами

Чтобы подключиться к конечной точке управления службами, необходимо указать ИД подписки Azure и допустимый сертификат управления. Идентификатор подписки можно получить на [портале управления][портале управления].

> [WACOM.NOTE] Начиная с версии Azure SDK для Python v0.8.0, появилась возможность использовать сертификаты, созданные OpenSSL при работе в Windows. Для этого требуется Python 2.7.4 или более поздняя версия.

### Управление сертификатами в Windows (MakeCert)

Можно создать самозаверяющий сертификат управления на своем компьютере с помощью средства `makecert.exe`. Откройте **окно командной строки Visual Studio** от имени **администратора** и используйте следующую команду, заменив *AzureCertificate* на имя сертификата, которое следует использовать.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Команда приведет к созданию файла `.cer`, после чего файл будет установлен в **Личное** хранилище сертификатов. Дополнительные сведения содержатся в разделе [Создание и передача сертификата управления для Azure][Создание и передача сертификата управления для Azure]

После создания сертификата необходимо передать `.cer`-файл в Azure с помощью действия "Загрузить" на вкладке "Параметры" [портала управления][портале управления].

После получения ИД подписки, создания сертификата и передачи `.cer`-файла в Azure можно подключиться к конечной точке управления Azure, передав ИД подписки и расположение сертификата в своем **личном** хранилище сертификатов в **ServiceManagementService** (и снова заменить *AzureCertificate* на имя вашего сертификата).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

В примере выше `sms` является объектом **ServiceManagementService**. Класс **ServiceManagementService** – это основной класс, используемый для управления службами Asure.

### Сертификаты управления в Windows/Mac/Linux (OpenSSL)

Можно также использовать [OpenSSL][OpenSSL] для создания сертификата управления. Фактически необходимо создать два сертификата: один для сервера (файл `.cer`) и один для клиента (файл `.pem`). Чтобы создать `.pem`-файл, выполните следующие действия.

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Чтобы создать сертификат `.cer`, выполните следующие действия.

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Дополнительные сведения о сертификатах Azure см. в разделе [Управление сертификатами в Windows Azure][Управление сертификатами в Windows Azure]. Полное описание параметров OpenSSL см. в документации по адресу <http://www.openssl.org/docs/apps/openssl.html>

После создания этих файлов необходимо передать `.cer`-файл в Azure с помощью действия "Отправить" на вкладке "Параметры" на [портале управления][портале управления], затем запомнить, куда был сохранен `.pem`.

После получения ИД подписки, создания сертификата и передачи `.cer`-файла в Azure можно подключиться к конечной точке управления Azure, передав ИД подписки и путь к `.pem` в **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

В примере выше `sms` является объектом **ServiceManagementService**. Класс **ServiceManagementService** – это основной класс, используемый для управления службами Asure.

## <a name="ListAvailableLocations"> </a>Практическое руководство. Список доступных местоположений

Чтобы получить список расположений, доступных для размещения служб, используйте метод **list\_locations**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

При создании облачной службы, служба хранилища или территориальной группы необходимо предоставить допустимое расположение. Метод **list\_locations** всегда будет возвращать текущий список доступных в настоящее время расположений. На момент написания этой статьи были доступны следующие расположения:

-   Западная Европа
-   Юго-Восточная Азия
-   Восточная Азия
-   Северо-центральный регион США
-   Северная Европа
-   Южно-центральный регион США
-   Запад США
-   Восток США

## <a name="CreateCloudService"> </a>Практическое руководство. создание облачной службы

При создании приложения и запуска его в Windows Azure совокупность кода и конфигурации называется [облачной службой][облачной службой] Windows Azure (в более ранних выпусках Azure это сочетание называлось *размещенной службой*). Метод **create\_hosted\_service** позволяет создать новую размещенную службу, предоставив имя размещенной службы (которое должно быть уникальным в Azure), метку (автоматически кодируется в base64), описание и местоположение. Можно указать территориальную группу вместо расположения службы.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    # You can either set the location or an affinity_group
    sms.create_hosted_service(name, label, desc, location)

Вы можете создать список всех размещенных служб для вашей подписки при помощи метода **list\_hosted\_services**.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Если вы хотите получить сведения о конкретной размещенной службе, можно передать имя размещенной службы в метод **get\_hosted\_service\_properties**.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
    print('Location: ' + hosted_service.hosted_service_properties.location)
            

После того как вы создали облачную службу, вы можете развернуть свой код в службе при помощи метода **create\_deployment**.

## <a name="DeleteCloudService"> </a>Практическое руководство. удаление облачной службы

Можно удалить облачную службу, передав облачную службу в метод **delete\_hosted\_service**.

    sms.delete_hosted_service('myhostedservice')

Обратите внимание: прежде чем удалить службу, необходимо сначала удалить все развернутые приложения этой службы. (См. [Практическое руководство. Удаление развернутых объектов][Практическое руководство. Удаление развертывания] для получения подробных сведений.)

## <a name="CreateDeployment"> </a>Практическое руководство. Создание развертывания

Метод **create\_deployment** служит для передачи нового [пакета служб][пакета служб] и создания нового развертывания в тестовой или производственной среде. Параметры этого метода приведены ниже:

-   **name**: имя размещенной службы.
-   **deployment\_name**: имя развертывания.
-   **slot**: строка, указывающая на гнездо `staging` или `production`.
-   **package\_url**: URL-адрес пакета развертывания (CSPGK-файл). Файл пакета должны быть сохранен в учетной записи хранилища BLOB-объектов Azure в рамках той же подписки, что и размещенная служба, в которую был передан пакет. Можно создать пакет развертывания с помощью [командлетов Azure PowerShell][командлетов Azure PowerShell] или с помощью [средства командной строки cspack][средства командной строки cspack]
-   **configuration**: файл конфигурации службы (CSCFG-файл), кодированный в base64.
-   **label**: метка имени размещенной службы (автоматически кодируется в base64).

В следующем примере создается новое развертывание `v1` для размещенной службы с именем `myhostedservice`.

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    slot = 'production'
    package_url = 'URL_for_.cspkg_file'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
    label = 'myhostedservice'

    result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Обратите внимание, что в вышеописанном примере статус операции **create\_deployment** можно извлекать путем передачи результатов, возвращенных **create\_deployment** в метод **get\_operation\_status**.

Вы можете выполнить доступ к свойствам развертывания при помощи методов **get\_deployment\_by\_slot** или **get\_deployment\_by\_name**. В следующем примере развертывание получается после указания области развертывания. В этом примере также перебираются все экземпляры развертывания.

    result = sms.get_deployment_by_slot('myhostedservice', 'production')

    print('Name: ' + result.name)
    print('Slot: ' + result.deployment_slot)
    print('Private ID: ' + result.private_id)
    print('Status: ' + result.status)
    print('Instances:')
    for instance in result.role_instance_list:
        print('Instance name: ' + instance.instance_name)
        print('Instance status: ' + instance.instance_status)
        print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>Практическое руководство. Обновление развертывания

Развертывание можно обновить посредством использования метода **change\_deployment\_configuration** или **update\_deployment\_status**.

Метод **change\_deployment\_configuration** позволяет передавать новый файл конфигурации службы (`.cscfg`), что приведет к изменению некоторых параметров службы (включая количество экземпляров в развертывании). Дополнительные сведения см. в статье [Схема конфигурации служб Azure (CSCFG)][Схема конфигурации служб Azure (CSCFG)]. В следующем примере продемонстрировано, как передавать новый файл конфигурации службы:

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

    result = sms.change_deployment_configuration(name, deployment_name, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Обратите внимание, что в вышеописанном примере статус операции **change\_deployment\_configuration** можно извлекать путем передачи результатов, возвращенных **change\_deployment\_configuration** в метод **get\_operation\_status**.

Метод **update\_deployment\_status** позволяет указать состояние развертывания ВЫПОЛНЯЕТСЯ или ПРИОСТАНОВЛЕНО. Следующий пример демонстрирует, как задать состояние ВЫПОЛНЯЕТСЯ для развертывания с именем `v1` для размещенной службы с именем `myhostedservice`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'

    result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>Практическое руководство. Перемещение развертываний из промежуточного хранения в рабочее развертывание

Azure предоставляет две среды развертывания: промежуточную и производственную. Обычно служба развертывается в промежуточной среде для тестирования перед развертыванием в рабочей среде. Когда требуется перенести службы из промежуточной среды в рабочую, это можно сделать без повторного развертывания всей службы. Для этого можно заменить развертывания. (Дополнительные сведения о переключении развертываний см. в разделе [Развертывание службы Azure][Развертывание службы Azure]).

Следующий пример демонстрирует, как с помощью метода **swap\_deployment** переключаться между двумя развертываниями (с именами `v1` и `v2`). В этом примере перед вызовом метода **swap\_deployment** развертывание `v1` находится в рабочей области, а развертывание `v2` — в тестовой. После вызова метода **swap\_deployment** развертывание `v2` переходит в рабочую область, а `v1` — в тестовую.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>Практическое руководство. удаление развертывания

Для удаления сущности используйте метод **delete\_deployment**. В следующем примере показано, как удалить развертывание `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Практическое руководство. Создание службы хранения

Служба [хранилища][хранилища] предоставляет доступ к [Blob-объектам][Blob-объектам], [таблицам][таблицам] и [запросам][запросам] Azure. Для создания службы хранения требуется имя службы (от 3 до 24 символов нижнего регистра, уникальное имя в Azure), описание, метка (до 100 символов, автоматически кодируется в base64) и расположение или территориальная группа. В следующем примере показано, как создать службу хранилища путем указания расположения. Если вы хотите использовать территориальную группу, вам сначала потребуется ее создать (см. [Практическое руководство. Создание территориальной группы][Практическое руководство. Создание территориальной группы]) и присвоить ей параметр **affinity\_group**.

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

Обратите внимание, что в вышеописанном примере статус операции **create\_storage\_account** можно извлекать путем передачи результатов, возвращенных **create\_storage\_account** в метод **get\_operation\_status**.

Можно вывести список ваших учетных записей хранения и их свойств с помощью метода **list\_storage\_accounts**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Affinity group: ' + account.storage_service_properties.affinity_group)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Практическое руководство. Удаление службы хранения

Можно удалить службу хранения, передав облачную службу в метод **delete\_storage\_account**. Удаление службы хранилища приведет к удалению всех данных, хранящихся в службе (BLOB-объектов, таблиц и очередей).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>Практическое руководство. Создание территориальной группы

Территориальная группа – это логическое группирование служб Azure, которое позволяет Azure разместить службы для оптимизации производительности. Например, можно создать территориальную группу в расположении "Запад США", затем создать [облачную службу][Практическое руководство. Создание облачной службы] в этой территориальной группе. При создании службы хранилища в той же территориальной группе Azure помещает ее в расположение "Запад США" и оптимизирует в центре обработки данных для наилучшей производительности с использованием облачных служб в той же территориальной группе.

Чтобы создать территориальную группу, необходимо имя, метку (автоматически кодируется в base64) и расположение. При необходимости можно предоставить описание:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myAffinityGroup'
    label = 'myAffinityGroup'
    location = 'West US'
    desc = 'my affinity group'

    sms.create_affinity_group(name, label, location, desc)

После создания территориальной группы при [создании службы хранилища][Практическое руководство. Создание службы хранения] можно указать группу (вместо расположения).

Можно отобразить территориальные группы и просмотреть их свойства, вызвав метод **list\_affinity\_groups**.

    result = sms.list_affinity_groups()

    for group in result:
        print('Name: ' + group.name)
        print('Description: ' + group.description)
        print('Location: ' + group.location)
        print('')

## <a name="DeleteAffinityGroup"> </a>Практическое руководство. Удаление территориальной группы

Можно удалить территориальную группу, передав имя группы в метод **delete\_affinity\_group**. Обратите внимание, что перед удалением территориальной группы следует отменить сопоставление этой группы с любыми службами (или необходимо удалить службы, которые используют эту территориальную группу).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>Практическое руководство. Список доступных операционных систем

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

## <a name="CreateVMImage"> </a>Практическое руководство. Создание образа операционной системы

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

Чтобы получить список доступных образов операционных систем, используйте метод **list\_os\_images**. Сюда входят все образы платформ и пользовательские образы:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Affinity group: ' + image.affinity_group)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Практическое руководство. Удаление образа операционной системы

Для удаления пользовательского образа используйте метод **delete\_os\_image**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Практическое руководство. Создание виртуальной машины

Чтобы создать виртуальную машину, необходимо сначала создать [облачную службу][Практическое руководство. Создание облачной службы]. После этого создайте развертывание виртуальной машины, используя метод **create\_virtual\_machine\_deployment**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    # You can either set the location or an affinity_group
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-ru-ru-30GB.vhd'

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

## <a name="DeleteVM"> </a>Практическое руководство. удаление виртуальной машины

Чтобы удалить виртуальную машину, необходимо сначала удалить развертывание с использованием метода **delete\_deployment**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Затем можно удалить облачную службу при помощи метода **delete\_hosted\_service**.

    sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a> Дальнейшие действия

Вы узнали основные сведения об управлении службами. Для решения более сложных задач, перейдите по следующим ссылкам.

-   См. справочник MSDN: [Облачные службы][пакета служб]
-   См. справочник MSDN: [Виртуальные машины][Виртуальные машины]

  [Azure SDK для Python]: https://www.windowsazure.com/ru-ru/develop/python/common-tasks/install-python/
  [портале управления]: https://manage.windowsazure.com/
  [Что такое управление службами]: #WhatIs
  [Основные понятия]: #Concepts
  [Практическое руководство. Подключение к управлению службами]: #Connect
  [Практическое руководство. Список доступных местоположений]: #ListAvailableLocations
  [Практическое руководство. Создание облачной службы]: #CreateCloudService
  [Практическое руководство. Удаление облачной службы]: #DeleteCloudService
  [Практическое руководство. Создание развертывания]: #CreateDeployment
  [Практическое руководство. Обновление развертывания]: #UpdateDeployment
  [Практическое руководство. Перемещение развертываний из промежуточного хранения в рабочее развертывание]: #MoveDeployments
  [Практическое руководство. Удаление развертывания]: #DeleteDeployment
  [Практическое руководство. Создание службы хранения]: #CreateStorageService
  [Практическое руководство. Удаление службы хранения]: #DeleteStorageService
  [Практическое руководство. Создание территориальной группы]: #CreateAffinityGroup
  [Практическое руководство. Удаление территориальной группы]: #DeleteAffinityGroup
  [Практическое руководство. Список доступных операционных систем]: #ListOperatingSystems
  [Практическое руководство. Создание образа операционной системы]: #CreateVMImage
  [Практическое руководство. Удаление образа операционной системы]: #DeleteVMImage
  [Практическое руководство. Создание виртуальной машины]: #CreateVM
  [Практическое руководство. Удаление виртуальной машины]: #DeleteVM
  [Дальнейшие действия]: #NextSteps
  [создать учетную запись Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/
  [API-интерфейса управления службами Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460799.aspx
  [Создание и передача сертификата управления для Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg551722.aspx
  [OpenSSL]: http://www.openssl.org/
  [Управление сертификатами в Windows Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg981929.aspx
  [облачной службой]: http://windowsazure.com/ru-ru/documentation/articles/cloud-services-what-is
  [пакета служб]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj155995.aspx
  [командлетов Azure PowerShell]: https://www.windowsazure.com/ru-ru/develop/php/how-to-guides/powershell-cmdlets/
  [средства командной строки cspack]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg432988.aspx
  [Схема конфигурации служб Azure (CSCFG)]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758710.aspx
  [Развертывание службы Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433027.aspx
  [хранилища]: https://www.windowsazure.com/ru-ru/manage/services/storage/what-is-a-storage-account/
  [Blob-объектам]: https://www.windowsazure.com/ru-ru/develop/python/how-to-guides/blob-service/
  [таблицам]: https://www.windowsazure.com/ru-ru/develop/python/how-to-guides/table-service/
  [запросам]: https://www.windowsazure.com/ru-ru/develop/python/how-to-guides/queue-service/
  [Виртуальные машины]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156003.aspx

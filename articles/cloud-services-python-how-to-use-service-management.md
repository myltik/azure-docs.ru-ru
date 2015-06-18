<properties
	pageTitle="Использование API для управления службами (Python) — руководство по функциям"
	description="Узнайте, как программными средствами Python реализовать общие задачи управления службами."
	services="cloud-services"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="03/11/2015"
	ms.author="huvalo"/>

# Использование управления службами в Python

В этом руководстве показано, как программными средствами Python реализовать общие задачи управления службами. Класс **ServiceManagementService** в [Azure SDK для Python][download-SDK-Python] поддерживает программный доступ к большинству функциональных возможностей, связанных с управлением службами, которые доступны на [портале управления][management-portal] (например, **создание, обновление и удаление облачных служб, развертываний, служб управления данными и виртуальных машин**). Эта возможность может быть полезна при создании приложений, которым требуется программный доступ к управлению службами.

## <a name="WhatIs"> </a>Что такое управление службами
API управления службой обеспечивает программный доступ к большинству функциональных возможностей управления службами, доступных через [портал управления][management-portal]. Пакет Azure SDK для Python позволяет управлять облачными службами и учетными записями хранения.

Чтобы использовать API-интерфейс управления службами, необходимо [создать учетную запись Azure](http://www.windowsazure.com/pricing/free-trial/).

## <a name="Concepts"> </a>Основные понятия
Пакет Azure SDK для Python служит оболочкой для [API-интерфейса управления службами Azure][svc-mgmt-rest-api], который является интерфейсом REST API. Все операции API выполняются по SSL и проходят взаимную проверку подлинности с использованием сертификатов X.509 v3. Доступ к службе управления можно получить из службы, работающей в Azure или непосредственно через Интернет из любого приложения, которое может отправить HTTPS-запрос и получить HTTPS-ответ.

## <a name="Connect"> </a>Практическое руководство. Подключение к управлению службами
Чтобы подключиться к конечной точке управления службами, необходимо указать ИД подписки Azure и допустимый сертификат управления. Идентификатор подписки можно получить на [портале управления][management-portal].

> [AZURE.NOTE]Начиная с версии Azure SDK для Python v0.8.0, появилась возможность использовать сертификаты, созданные OpenSSL при работе в Windows. Для этого требуется Python 2.7.4 или более поздняя версия. Корпорация Майкрософт рекомендует пользователям использовать OpenSSL вместо PFX-сертификатов, поскольку поддержка PFX-сертификатов, скорее всего, будет удалена в будущем.

### Сертификаты управления в Windows/Mac/Linux (OpenSSL)
Можно также использовать [OpenSSL](http://www.openssl.org/) для создания сертификата управления. Фактически необходимо создать два сертификата: один для сервера (`.cer`-файл) и один для клиента (`.pem`-файл). Чтобы создать `.pem`-файл, выполните следующие действия.

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Чтобы создать сертификат `.cer`, выполните следующие действия.

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Дополнительные сведения о сертификатах Azure см. в разделе [Управление сертификатами в Windows Azure](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx). Полное описание параметров OpenSSL см. в документации по адресу [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

После создания этих файлов необходимо отправить `.cer`-файл в Azure с помощью действия «Отправить» на вкладке «Параметры» [портала управления][management-portal], и обязательно записать, куда был сохранен `.pem`-файл.

После получения идентификатора подписки, создания сертификата и отправки `.cer`-файла в Azure можно подключиться к конечной точке управления Azure, передав идентификатор подписки и путь в `.pem`-файл в **ServiceManagementService**.

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'

	sms = ServiceManagementService(subscription_id, certificate_path)

В примере выше `sms` является объектом **ServiceManagementService**. Класс **ServiceManagementService** – это основной класс, используемый для управления службами Asure.

### Управление сертификатами в Windows (MakeCert)

Вы можете создать самозаверяющий сертификат управления на своем компьютере с помощью программы `makecert.exe`. Откройте **окно командной строки Visual Studio** от имени **администратора** и используйте следующую команду, заменив *AzureCertificate* на имя сертификата, которое следует использовать.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Эта команда создаст `.cer`-файл и установит этот сертификат в **личное** хранилище сертификатов. Дополнительные сведения содержатся в разделе [Создание и передача сертификата управления для Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)

После создания сертификата необходимо отправить `.cer`-файл в Azure с помощью действия «Отправить» на вкладке «Параметры» [портала управления][management-portal].

После получения идентификатора подписки, создания сертификата и отправки `.cer`-файла в Azure можно подключаться к конечной точке управления Azure, передав идентификатор подписки и расположение сертификата в своем **личном** хранилище сертификатов в **ServiceManagementService** (и снова заменить *AzureCertificate* на имя вашего сертификата).

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

В примере выше `sms` является объектом **ServiceManagementService**. Класс **ServiceManagementService** – это основной класс, используемый для управления службами Asure.

## <a name="ListAvailableLocations"> </a>Практическое руководство. Отображение списка доступных расположений

Чтобы получить список расположений, доступных для размещения служб, используйте метод **list_locations**.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

При создании облачной службы или службы хранилища необходимо предоставить допустимое расположение. Метод **list_locations** всегда будет возвращать текущий список доступных в настоящее время расположений. На момент написания этой статьи были доступны следующие расположения:

- Западная Европа
- Северная Европа
- Юго-Восточная Азия
- Восточная Азия
- Центральная часть США
- Северо-центральный регион США
- Южно-центральный регион США
- Запад США
- Восток США
- Восточная часть Японии
- Западная часть Японии
- Южная Бразилия
- Восточная часть Австралии
- Юго-Восточная часть Австралии

## <a name="CreateCloudService"> </a>Практическое руководство. Создание облачной службы

При создании приложения и запуска его в Windows Azure совокупность кода и конфигурации называется [облачной службой] Windows Azure (в более ранних выпусках Azure это сочетание называлось *размещенной службой*). Метод **create_hosted_service** позволяет создать новую размещенную службу, предоставив имя размещенной службы (которое должно быть уникальным в Azure), метку (автоматически кодируется в base64), описание и местоположение.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	sms.create_hosted_service(name, label, desc, location)

Вы можете создать список всех размещенных служб для вашей подписки при помощи метода **list_hosted_services**.

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Если вы хотите получить сведения о конкретной размещенной службе, можно передать имя размещенной службы в метод **get_hosted_service_properties**.

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Location: ' + hosted_service.hosted_service_properties.location)

После того как вы создали облачную службу, вы можете развернуть свой код в службе при помощи метода **create_deployment**.

## <a name="DeleteCloudService"> </a>Практическое руководство. Удаление облачной службы

Можно удалить облачную службу, передав облачную службу в метод **delete_hosted_service**.

	sms.delete_hosted_service('myhostedservice')

Обратите внимание: прежде чем удалить службу, необходимо сначала удалить все развернутые приложения этой службы. (Дополнительные сведения см. в разделе [Практическое руководство. Удаление развертывания](#DeleteDeployment).)

## <a name="DeleteDeployment"> </a>Практическое руководство. Удаление развертывания

Для удаления сущности используйте метод **delete_deployment**. В следующем примере показано, как удалить развертывание с именем `v1`.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Практическое руководство. Создание службы хранилища

Служба [хранилища] предоставляет доступ к [Blob-объектам][azure-blobs], [таблицам][azure-tables] и [запросам][azure-queues] Azure. Для создания службы хранения требуется имя службы (от 3 до 24 символов нижнего регистра, уникальное имя в рамках Azure), описание, метка (до 100 символов, автоматически кодируется в base64) и расположение. В следующем примере показано, как создать службу хранилища путем указания расположения.

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

Обратите внимание, что в вышеописанном примере статус операции **create_storage_account** можно извлекать путем передачи результатов, возвращенных **create_storage_account** в метод **get_operation_status**.

Можно вывести список ваших учетных записей хранения и их свойств с помощью метода **list_storage_accounts**.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>Практическое руководство. Удаление службы хранилища

Можно удалить службу хранения, передав облачную службу в метод **delete_storage_account**. Удаление службы хранилища приведет к удалению всех данных, хранящихся в службе (BLOB-объектов, таблиц и очередей).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Практическое руководство. Список доступных операционных систем

Чтобы получить список операционных систем, доступных для служб размещения, используйте метод **list_operating_systems**.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

Кроме того, можно использовать метод **list_operating_system_families**, который группирует операционные системы по семействам.

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>Практическое руководство. Создание образа операционной системы

Чтобы добавить образ операционной системы в репозиторий образов, используйте метод **add_os_image**.

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

Чтобы получить список доступных образов операционных систем, используйте метод **list_os_images**. Сюда входят все образы платформ и пользовательские образы:

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

## <a name="DeleteVMImage"> </a>Практическое руководство. Удаление образа операционной системы

Для удаления пользовательского образа используйте метод **delete_os_image**.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Практическое руководство. Создание виртуальной машины

Чтобы создать виртуальную машину, необходимо сначала создать [облачную службу](#CreateCloudService). После этого создайте развертывание виртуальной машины, используя метод **create_virtual_machine_deployment**.

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

## <a name="DeleteVM"> </a>Практическое руководство. Удаление виртуальной машины

Чтобы удалить виртуальную машину, необходимо сначала удалить развертывание с использованием метода **delete_deployment**.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

Затем можно удалить облачную службу при помощи метода **delete_hosted_service**.

	sms.delete_hosted_service(service_name='myvm')

##Практическое руководство. Создание виртуальной машины из записанного образа виртуальной машины

Чтобы записать образ виртуальной машины, сначала вызовите метод **capture_vm_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	# replace the below three parameters with actual values
	hosted_service_name = 'hs1'
	deployment_name = 'dep1'
	vm_name = 'vm1'

	image_name = vm_name + 'image'
	image = CaptureRoleAsVMImage	('Specialized',
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

Далее, чтобы убедиться, что образ успешно записан, используйте API **list_vm_images** и проверьте, отображается ли ваш образ в результатах:

	images = sms.list_vm_images()

Наконец, чтобы создать виртуальную машину с помощью записанного образа, используйте метод **create_virtual_machine_deployment** как раньше, но на этот раз передайте vm_image_name.

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

Дополнительные сведения о записи образа виртуальной машины Linux см. в разделе [Запись образа виртуальной машины Linux.](virtual-machines/virtual-machines-linux-capture-image.md)

Дополнительные сведения о записи образа виртуальной машины Windows см. в разделе [Запись образа виртуальной машины Windows.](virtual-machines/virtual-machines-capture-image-windows-server.md)

## <a name="What's Next"> </a>Дальнейшие действия

Теперь, когда вы ознакомились с основами службы управления, можно приступить к [полной справочной документации по API для пакета SDK Azure Python](http://azure-sdk-for-python.readthedocs.org/en/documentation/index.html) и легко выполнять сложные задачи, управляя своим приложением python.



[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/develop/python/common-tasks/install-python/
[облачной службой]: http://windowsazure.com/documentation/articles/cloud-services-what-is
[service package]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Azure PowerShell cmdlets]: https://www.windowsazure.com/develop/php/how-to-guides/powershell-cmdlets/
[cspack commandline tool]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[Deploying an Azure Service]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[хранилища]: https://www.windowsazure.com/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/develop/python/how-to-guides/queue-service/
[Azure Service Configuration Schema (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[Cloud Services]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Virtual Machines]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx

<!--HONumber=54-->
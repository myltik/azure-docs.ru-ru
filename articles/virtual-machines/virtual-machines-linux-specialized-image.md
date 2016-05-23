<properties
	pageTitle="Создание копии виртуальной машины Linux | Microsoft Azure"
	description="Узнайте, как скопировать виртуальную машину Azure под управлением Linux в модели развертывания диспетчера ресурсов, создав *специализированный образ*."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Создание копии виртуальной машины Linux в модели развертывания диспетчера ресурсов



В этой статье показано, как создать копию виртуальной машины Azure под управлением Linux в модели развертывания диспетчера ресурсов, используя интерфейс командной строки Azure и портал Azure. Здесь демонстрируется процесс создания **_специализированного_** образа виртуальной машины Azure, содержащего учетные записи пользователей и другие о состоянии исходной виртуальной машины. Специализированный образ пригодится в таких ситуациях, как перевод виртуальной машины Linux из классической модели развертывания в модель развертывания диспетчера ресурсов или создание резервной копии виртуальной машины Linux, созданной в модели развертывания диспетчера ресурсов. Таким образом можно скопировать операционную систему и диски данных и настроить сетевые ресурсы для создания новой виртуальной машины.

В случае массового развертывания схожих виртуальных машин Linux требуется *обобщенный* образ (см. статью [Запись образа виртуальной машины Linux](virtual-machines-linux-capture-image.md)).



## Перед началом работы

В этой статье предполагается, что у вас есть следующие компоненты.

1. **Виртуальная машина Azure под управлением Linux** в классической модели развертывания или в модели развертывания Resource Manager с настроенной операционной системой, присоединенными дисками данных и установленными необходимыми приложениями. Справочные сведения о создании этой виртуальной машины см. в разделе [Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки](virtual-machines-linux-quick-create-cli.md). 

1. **Интерфейс командной строки Azure**, установленный на компьютере, а также выполненный вход в подписку Azure. Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md).

1. **Группа ресурсов** с **учетной записью хранения** и созданный в ней **контейнер больших двоичных объектов** для копирования виртуальных жестких дисков. Дополнительные сведения см. в статье [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](../storage/storage-azure-cli.md).



> [AZURE.NOTE] Аналогичная процедура подходит для виртуальной машины, созданной с использованием одной из двух моделей развертывания как исходный образ. Незначительные отличия будут указаны.


## Копирование виртуальных жестких дисков в учетную запись хранения для диспетчера ресурсов


1. Сначала освободите виртуальный жесткий диск, используемый исходной виртуальной машиной, одним из следующих способов:

	- Если вы хотите **_скопировать_** исходную виртуальную машину, **остановите** ее и **отмените распределение**. На портале последовательно выберите пункты **Обзор** > **Виртуальные машины** или **Виртуальные машины (классика)** > *ваша виртуальная машина* > **Остановить**. Для виртуальных машин, созданных в модели развертывания Resource Manager, можно также использовать команду интерфейса командной строки Azure `azure vm stop <yourResourceGroup> <yourVmName>` с дополнением `azure vm deallocate <yourResourceGroup> <yourVmName>`. Обратите внимание, что значение *Состояние* виртуальной машины на портале изменится с **Выполняется** на **Остановлено (освобождено)**.
	
	- Если вы хотите **_перенести_** исходную виртуальную машину, **удалите** ее и используйте оставшийся виртуальный жесткий диск. **Найдите** свою виртуальную машину на [портале](https://portal.azure.com) и нажмите кнопку **Удалить**.
	
1. Найти ключ доступа к учетной записи хранения, в которую входит исходный виртуальный жесткий диск. Дополнительные сведения о ключах доступа см. в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

	- Если исходная виртуальная машина создана с использованием классической модели развертывания, последовательно щелкните **Обзор** > **Учетные записи хранения (классические)** > *ваша учетная запись хранения* > **Все параметры** > **Ключи** и скопируйте ключ с пометкой **Первичный ключ доступа**. Либо в интерфейсе командной строки Azure перейдите в классический режим с помощью команды `azure config mode asm`, а затем выполните команду `azure storage account keys list <yourSourceStorageAccountName>`.

	- Для виртуальной машины, созданной с использованием модели развертывания Resource Manager, последовательно выберите пункты **Обзор** > **Учетные записи хранения** > *ваша учетная запись хранения* > **Все параметры** > **Ключи доступа** и скопируйте текст с пометкой **key1**. Либо в интерфейсе командной строки Azure перейдите в режим Resource Manager с помощью команды `azure config mode arm`, а затем выполните команду `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>`.

1. Скопируйте файлы виртуального жесткого диска, используя [команды Azure CLI для службы хранилища](../storage/storage-azure-cli.md), как описано ниже. Если вы предпочитаете решать эти задачи с помощью пользовательского интерфейса, используйте [обозреватель службы хранилища Microsoft Azure](http://storageexplorer.com/). </br>
	1. Настройте строку подключения для учетной записи хранения. Строка подключения будет содержать ключ доступа к этой учетной записи хранения.
	
			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>
	
	2. Создайте [подписанный URL-адрес](../storage/storage-dotnet-shared-access-signature-part-1.md) для файла виртуального жесткого диска в исходной учетной записи хранения. Запишите **URL-адрес общего доступа**, полученный в результате выполнения следующей команды:
	
			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>
	
	3. Скопируйте файл виртуального жесткого диска из исходного хранилища в место назначения, используя следующую команду:
	
			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>
	
	4. Файл виртуального жесткого диска будет скопирован асинхронно. Проверить ход выполнения можно с помощью следующей команды:
	
			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>
		
</br>

>[AZURE.NOTE] Диски операционной системы и данных необходимо копировать отдельно, как описано выше.


## Создание виртуальной машины с помощью скопированного виртуального жесткого диска

Описанные ниже шаги показывают, как использовать интерфейс командной строки Azure для создания виртуальной машины Linux на основе диспетчера ресурсов в новой виртуальной сети, используя виртуальный жесткий диск, скопированный при выполнении описанных выше шагов. Виртуальный жесткий диск должен находиться в той же учетной записи хранения, что и создаваемая виртуальная машина.


Сначала настройте виртуальную сеть и сетевой адаптер для новой виртуальной машины, как указано ниже. Используйте значения для переменных, соответствующие вашему приложению.

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


Теперь создайте новую виртуальную машину с помощью одного или нескольких скопированных виртуальных жестких дисков, используя следующую команду. </br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>

	
URL-адреса дисков данных и дисков операционной системы выглядят так: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Их можно узнать на портале. Для этого найдите контейнер хранилища, щелкните скопированный виртуальный жесткий диск операционной системы или данных, а затем скопируйте содержимое **URL-адреса**.
	
	
Если команда будет выполнена успешно, вы увидите примерно такой результат:

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

Созданная виртуальная машина появится на [портале Azure](https://portal.azure.com) в разделе **Обзор** > **Виртуальные машины**.

Подключитесь к новой виртуальной машине с помощью SSH-клиента на свой выбор и воспользуйтесь учетными данными исходной виртуальной машины, например `ssh OldAdminUser@<IPaddressOfYourNewVM>`. Дополнительные сведения о подключении по протоколу SSH к виртуальной машине Linux см. в статье [Использование SSH с Linux и Mac в Azure](virtual-machines-linux-ssh-from-linux.md).


## Дальнейшие действия

Чтобы узнать, как использовать интерфейс командной строки Azure для управления новой виртуальной машиной, см. статью [Команды Azure CLI в режиме Azure Resource Manager (ARM)](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0511_2016-->
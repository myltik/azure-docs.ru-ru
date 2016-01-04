<properties 
	pageTitle="Управление учетными записями служб мультимедиа Azure с помощью PowerShell" 
	description="Узнайте, как управлять учетными записями служб мультимедиа Azure с помощью командлетов PowerShell." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="juliako"/>


#Управление учетными записями служб мультимедиа Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE]Для создания учетной записи служб мультимедиа Azure необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Бесплатная пробная версия Azure</a>.

##Обзор 

В этой статье показано, как использовать командлеты PowerShell для управления учетными записями служб мультимедиа Azure.

>[AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Бесплатная пробная версия Azure</a>.

##Установка командлетов Microsoft Azure PowerShell

Сведения об установке новейших командлетов Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md)

##Выбор подписки Azure

После установки и настройки командлетов PowerShell следует указать, в какой подписке вы будете работать.

Чтобы получить список доступных подписок, выполните следующий командлет:

	PS C:\> Get-AzureSubscription

Выберите подписку, выполнив командлет:

	PS C:\> Select-AzureSubscription "TestSubscription"

 
##Получение имени учетной записи хранения

Службы мультимедиа Azure используют хранилище Azure для хранения мультимедиа. При создании новой учетной записи служб мультимедиа необходимо связать ее с учетной записью хранения. Учетная запись хранения должна принадлежать к подписке, которую планируется использовать для учетной записи служб мультимедиа.

В этом примере применяется существующая учетная запись хранения. Командлет [Get-AzureStorageAccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) получает учетные записи хранения в текущей подписке. Получите имя учетной записи хранения (StorageAccountName), с которой требуется связать учетную запись служб мультимедиа.

	StorageAccountDescription : 
	AffinityGroup             :
	Location                  : East US
	GeoReplicationEnabled     : True
	GeoPrimaryLocation        : East US
	GeoSecondaryLocation      : West US
	Label                     : storagetest001
	StorageAccountStatus      : Created
	StatusOfPrimary           : Available
	StatusOfSecondary         : Available
	Endpoints                 : {https://storagetest001.blob.core.windows.net/,
	                            https://storagetest001.queue.core.windows.net/,
	                            https://storagetest001.table.core.windows.net/}
	AccountType               : Standard_GRS
	StorageAccountName        : storatetest001
	OperationDescription      : Get-AzureStorageAccount
	OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
	OperationStatus           : Succeeded

##Создание учетной записи служб мультимедиа

Чтобы создать учетную запись служб мультимедиа, используйте командлет [New-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx). При этом укажите имя учетной записи служб мультимедиа, местоположение центра обработки данных, где она будет создана, и имя учетной записи хранения.


	PS C:\> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

##Получение учетных записей служб мультимедиа

После создания одной или нескольких учетных записей служб мультимедиа можно отобразить сведения с помощью командлета [Get-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx)

	
	PS C:\> Get-AzureMediaServicesAccount
	
	AccountId		Name				State
	---------       ----       			 -----
	xxxxxxxxxx      amstestaccount001   Active

Передав параметр Name, вы получите более подробные сведения, включая ключи учетной записи.

	PS C:\> Get-AzureMediaServicesAccount -Name amstestaccount001

##Повторное создание ключей доступа служб мультимедиа

Чтобы обновить первичный или вторичный ключ доступа служб мультимедиа, используйте командлет [New-AzureMediaServicesKey](https://msdn.microsoft.com/library/azure/dn495215.aspx). Необходимо указать имя учетной записи и ключ, который следует создать повторно (первичный или вторичный).

Добавьте параметр -Force, чтобы не отображать запросы подтверждения PowerShell.

	PS C:\> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

##Удаление учетной записи служб мультимедиа

Когда все готово для удаления учетной записи служб мультимедиа Azure, выполните командлет [Remove-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495220.aspx).

	PS C:\> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 

<!---HONumber=AcomDC_1210_2015-->

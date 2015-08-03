<properties
   pageTitle="Просмотр и выбор образов виртуальных машин Azure с помощью командной оболочки PowerShell и инфраструктуры Azure CLI"
   description="Узнайте, как определить издателя, предложение и SKU для образов при создании виртуальной машины Azure с помощью диспетчера ресурсов."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="05/29/2015"
   ms.author="rasquill"/>

# Просмотр и выбор образов виртуальных машин Azure с помощью командной оболочки PowerShell и инфраструктуры Azure CLI

> [AZURE.NOTE]При поиске образов виртуальных машин в этом разделе используется [режим управления ресурсами Azure](../resource-group-overview.md) с помощью интерфейса командной строки Azure для Mac, Linux и Windows или Windows PowerShell последней версии. В случае использования интерфейса командной строки Azure запустите этот режим, введя `azure config mode arm`. При использовании PowerShell введите `Switch-AzureMode AzureResourceManager`. Полную информацию об обновлении и настройке см. в статье [Использование интерфейса командной строки Azure в режиме управления ресурсами](xplat-cli-azure-resource-manager.md) и [Использование PowerShell в режиме управления ресурсами Azure](../powershell-azure-resource-manager.md).

## Таблица часто используемых образов


| PublisherName | ПРЕДЛОЖЕНИЕ | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7.1. |
| CoreOS | CoreOS | Beta |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Стандартная |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014 WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014 WS2012R2 | Enterprise-Optimized-for-OLTP |
| Canonical | UbuntuServer | 12.04.5-LTS |
| Canonical | UbuntuServer | 14.04.2-LTS |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


## Инфраструктура CLI Azure

Для поиска образа, который можно использовать с `azure vm quick-create` или для создания файла шаблона группы ресурсов, проще и быстрее всего воспользоваться командой `azure vm image list`, которой передается расположение, имя издателя (без учета регистра) и предложение (если оно вам известно). Например, вот короткий пример (многие списки достаточно длинные), в котором вам известно, что издателем образа является Canonical, а предложение называется UbuntuServer.

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240
    
Столбец **Urn** — это форма, которая передается `azure vm quick-create`.
    
Однако часто нам неизвестно, какие именно образы доступны. В этом случае можно просмотреть их список, сначала получив список издателей с помощью `azure vm image list-publishers` и указав в качестве расположения центр обработка данных, в котором вы собираетесь использовать группу ресурсов. Например, в приведенном ниже списке содержатся все издатели образов из западной части США (при передаче аргумента расположения необходимо привести стандартное название расположения в нижний регистр и удалить из него все пробелы).

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")                
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  

    
Эти списки часто бывают довольно длинными, поэтому в примере приведен лишь фрагмент. Предложим, мы выяснили, что Canonical — издатель из западной части США. Теперь мы можем найти его предложения, вызвав команду azure vm image list-offers и указав расположение и издателя, как в следующем примере:

    azure vm image list-offers           
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK
    
Теперь мы знаем, что издатель Canonical из западной части США предлагает **UbuntuServer** для Azure. Однако нам также нужны номера SKU. Для этого мы вызываем команду `azure vm image list-skus` и указываем расположение, издателя и предложение.

    azure vm image list-skus           
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK
    
Обладая этой информацией, мы можем найти нужный нам образ с помощью исходной команды, которая приведена вверху.

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422 
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK
    
Теперь мы можем выбрать именно тот образ, который нам нужен. Инструкции, с помощью которых можно быстро создать виртуальную машину на основе полученных нами данных URN или воспользоваться шаблоном с этими данными, см. в статье [Использование инфраструктуры Azure CLI для Mac, Linux и Windows со средствами управления ресурсами Azure](xplat-cli-azure-resource-manager.md).

## PowerShell

При создании виртуальной машины с помощью диспетчера ресурсов Azure иногда бывает нужно указать образ по сочетанию следующих свойств:

- ИЗДАТЕЛЬ
- ПРЕДЛОЖЕНИЕ
- SKU

Например, эти значения нужны при вызове командлета PowerShell **Set-AzureVMSourceImage** либо при использовании файла шаблона группы ресурсов, в котором необходимо указать тип создаваемой виртуальной машины.

Чтобы определить эти значения для нужного образа, необходимо выполнить указанные ниже действия.

1. Получить список издателей образов.
2. Получить список предложений нужного издателя.
3. Получить список номеров SKU для требуемого предложения.

Чтобы сделать это в PowerShell, сначала перейдите в режим диспетчера ресурсов Azure PowerShell.

	Switch-AzureMode AzureResourceManager 

Чтобы выполнить первое действие из приведенного выше списка, вызовите список издателей с помощью следующих команд:

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

Укажите название издателя и выполните следующие команды:

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Укажите название предложения и выполните следующие команды:

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Команда **Get-AzureVMImageSku** возвращает информацию, необходимую для выбора образа для новой виртуальной машины.

Ниже приведен пример.

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName
	
	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

Для издателя MicrosoftWindowsServer:

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer
	
	Offer
	-----
	WindowsServer

Для предложения WindowsServer:

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
	
	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

Скопировав наименование SKU из этого списка, вы получаете всю необходимую информацию для командлета PowerShell **Set-AzureVMSourceImage** или файла шаблона группы ресурсов, для которых нужно указать издателя, предложение и номер SKU образа.


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/
 

<!---HONumber=July15_HO4-->
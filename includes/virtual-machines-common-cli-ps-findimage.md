

## <a name="azure-cli-20"></a>Azure CLI 2.0

Если у вас [установлен Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), используйте команду `az vm image list`, чтобы увидеть кэшированный список популярных образов виртуальных машин. Например, в следующем примере команда `az vm image list -o table` отображает приведенный ниже результат.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Поиск всех текущих образов

Чтобы получить текущий список всех образов, используйте `az vm image list` с параметром `--all`. В отличие от команд Azure CLI 1.0, команда `az vm image list --all` возвращает все образы в регионе **westus** по умолчанию (если не указан определенный аргумент `--location`), поэтому выполнение команды `--all` занимает некоторое время. Если вы собираетесь исследовать данные в интерактивном режиме, используйте команду `az vm image list --all > allImages.json`, которая возвращает список всех образов, доступных в настоящее время в Azure, и сохраняет его как файл для локального использования. 

Можно указать один из нескольких параметров, чтобы ограничить область поиска до определенного расположения, предложения, издателя или номера SKU, если вам известны соответствующие сведения. Если расположение не указано, то возвращаются значения для региона **westus**.

### <a name="find-specific-images"></a>Поиск определенных образов

Используйте `az vm image list` с [фильтром запроса JMESPATH](https://docs.microsoft.com/cli/azure/query-az-cli2) для поиска определенной информации. Например, приведенная ниже команда отображает номера **SKU**, которые доступны для **Debian** (помните, что без параметра `--all` поиск выполняется только в локальном кэше общих образов).

```azurecli
az vm image list --query '[?contains(offer,`Debian`)]' -o table --all
```

Результат будет выглядеть следующим образом: 
```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
  Sku  Publisher    Offer    Urn                       Version    UrnAlias
-----  -----------  -------  ------------------------  ---------  ----------
    8  credativ     Debian   credativ:Debian:8:latest  latest     Debian

<list shortened for the example>
```

Если вы знаете, куда выполняется развертывание, то можете использовать результаты общего поиска образов с помощью команд `az vm image list-skus`, `az vm image list-offers` и `az vm image list-publishers`, чтобы найти именно тот образ, который нужен, и где он может быть развернут. Например, если из предыдущего примера известно, что у издателя `credativ` есть предложение Debian, то можно использовать `--location` и другие параметры, чтобы найти именно то, что нужно. Следующий пример выполняет поиск образа Debian 8 в регионе **westeurope**.

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

Ниже приведен результат.

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0 

> [!NOTE]
> В этой статье описывается, как найти и выбрать образы виртуальных машин, используя установленную версию Azure CLI 1.0 или Azure PowerShell, поддерживающую модель развертывания с помощью Azure Resource Manager. Предварительно необходимо переключиться в режим Resource Manager. В случае использования интерфейса командной строки Azure запустите этот режим, введя `azure config mode arm`. 
> 

Быстрее всего найти образ можно, выполнив команду `azure vm image list` и передав в нее расположение, имя издателя (без учета регистра) и предложение (если оно вам известно). Например, вот короткий пример (многие списки достаточно длинные), в котором вам известно, что издателем образа является Canonical, а предложение называется UbuntuServer.

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

Столбец **Urn** — это форма, которая передается `azure vm quick-create`.

Однако часто нам неизвестно, какие именно образы доступны. В этом случае можно перейти к образам, выполнив команду `azure vm image list-publishers` и указав в командной строке расположение центра обработки данных. Например, в приведенном ниже списке содержатся все издатели образов из западной части США (при передаче аргумента расположения необходимо привести стандартное название расположения в нижний регистр и удалить из него все пробелы).

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Эти списки часто бывают довольно длинными, поэтому в предыдущем примере приведен лишь фрагмент. Предложим, мы выяснили, что Canonical — издатель из западной части США. Теперь мы можем найти его предложения, вызвав `azure vm image list-offers` и указав расположение и издателя, как в следующем примере.

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Теперь мы знаем, что издатель Canonical из западной части США предлагает **UbuntuServer** для Azure. Однако нам также нужны номера SKU. Чтобы получить их значения, следует выполнить команду `azure vm image list-skus` и указать обнаруженные расположение, издателя и предложение.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Обладая этой информацией, мы можем найти нужный нам образ с помощью исходной команды, которая приведена вверху.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

Теперь мы можем выбрать именно тот образ, который нам нужен. Инструкции, с помощью которых можно быстро создать виртуальную машину на основе полученных нами данных URN или воспользоваться шаблоном с этими данными, см. в статье [Использование инфраструктуры Azure CLI для Mac, Linux и Windows со средствами управления ресурсами Azure](../articles/xplat-cli-azure-resource-manager.md).

## <a name="powershell"></a>PowerShell
> [!NOTE]
> Установите и настройте [последнюю версию Azure PowerShell](/powershell/azureps-cmdlets-docs). Если вы используете модули Azure PowerShell версии ниже 1.0, то можете использовать приведенные ниже команды. Но сначала необходимо выполнить команду `Switch-AzureMode AzureResourceManager`. 
> 
> 

При создании виртуальной машины с помощью диспетчера ресурсов Azure иногда бывает нужно указать образ по сочетанию следующих свойств:

* ИЗДАТЕЛЬ
* ПРЕДЛОЖЕНИЕ
* SKU

Например, эти значения нужны при вызове командлета PowerShell `Set-AzureRMVMSourceImage` либо при использовании файла шаблона группы ресурсов, в котором необходимо указать тип создаваемой виртуальной машины.

Чтобы определить эти значения для нужного образа, необходимо выполнить указанные ниже действия.

1. Получить список издателей образов.
2. Получить список предложений нужного издателя.
3. Получить список номеров SKU для требуемого предложения.

Во-первых, выведите список издателей с помощью следующих команд.

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Укажите название выбранного издателя и выполните следующие команды.

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Укажите название выбранного предложения и выполните следующие команды.

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Команда `Get-AzureRMVMImageSku` возвращает всю информацию, необходимую для указания образа для новой виртуальной машины.

Ниже представлен полный пример.

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

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
```

Для издателя MicrosoftWindowsServer:

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Для предложения WindowsServer:

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

Скопируйте выбранное наименование SKU из этого списка. Теперь у вас есть все необходимые сведения, чтобы использовать командлет PowerShell `Set-AzureRMVMSourceImage` или шаблон группы ресурсов.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/
## Настройка PowerShell для шаблонов диспетчера ресурсов

Чтобы использовать Azure PowerShell с диспетчером ресурсов, необходимы правильные версии Windows PowerShell и Azure PowerShell.

### Проверка версий PowerShell

Убедитесь, что Windows PowerShell имеет версию 3.0 или 4.0. Чтобы узнать версию Windows PowerShell, введите в командной строке Windows PowerShell такую команду:

	$PSVersionTable

Появятся такие сведения:

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2


Убедитесь, что в строке **PSVersion** указано значение 3.0 или 4.0. Если указано другое значение, см. статью [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### Настройка учетной записи и подписки Azure

Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться в бесплатной пробной версии](http://azure.microsoft.com/pricing/free-trial/).

Откройте окно командной строки Azure PowerShell и войдите в систему Azure с помощью такой команды:

	Login-AzureRmAccount

Если у вас есть несколько подписок Azure, их список можно получить с помощью такой команды:

	Get-AzureRmSubscription

Появятся такие сведения:

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName :
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Укажите текущую подписку Azure, выполнив в командной строке Azure PowerShell такие команды: Замените все содержимое внутри кавычек, включая символы < and >, правильным именем.

	$subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
	Select-AzureRmSubscription -SubscriptionName $subscr -Current

Дополнительные сведения о подписках и учетных записях Azure см. в разделе [Подключение к подписке](powershell-install-configure.md#Connect).

<!---HONumber=Oct15_HO4-->
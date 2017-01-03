## <a name="set-up-azure-powershell-for-azure-dns"></a>Настройка Azure PowerShell для Azure DNS

### <a name="before-you-begin"></a>Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующие компоненты.

* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* Вам потребуется установить последнюю версию командлетов PowerShell Azure Resource Manager. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure

Откройте консоль PowerShell и подключитесь к своей учетной записи. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>Выбор подписки
 
Просмотрите подписки учетной записи.

```powershell
Get-AzureRmSubscription
```

Выберите подписку Azure.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.

Если используется существующая группа ресурсов, можно пропустить этот шаг.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Регистрация поставщика ресурсов

Служба Azure DNS управляется поставщиком ресурсов Microsoft.Network. Вашу подписку Azure необходимо зарегистрировать, чтобы использовать этот поставщик ресурсов, прежде чем работать с Azure DNS. Эта операция выполняется один раз для каждой подписки.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

<!--HONumber=Dec16_HO3-->



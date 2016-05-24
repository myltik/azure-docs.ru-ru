
## Запуск сеанса PowerShell

Сначала установите и запустите [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) версии 1.0 или выше. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../articles/powershell-install-configure.md).


>[AZURE.NOTE] Многие новые функции базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager (ARM)](../articles/resource-group-overview.md), поэтому в примерах используются соответствующие [командлеты Azure PowerShell для базы данных SQL](https://msdn.microsoft.com/library/azure/mt574084.aspx). Существующие [классические командлеты для базы данных SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) в классическом развертывании поддерживаются для обеспечения обратной совместимости. Мы рекомендуем использовать командлеты для Resource Manager.


Выполните командлет [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx). Откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте для входа те же учетные данные, что и для входа на портал Azure.

	Add-AzureRmAccount

Если у вас несколько подписок, используйте командлет [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx), чтобы выбрать подписку, которую будет использовать сеанс PowerShell. Чтобы узнать, какие подписки использует текущий сеанс PowerShell, запустите командлет [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx). Чтобы просмотреть все подписки, запустите командлет [**Get AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx).

	Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


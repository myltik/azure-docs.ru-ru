
## Запуск сеанса PowerShell

Сначала установите и запустите [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) версии 1.0 или выше. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../articles/powershell-install-configure.md).


>[AZURE.NOTE] Многие новые функции базы данных SQL поддерживаются только при использовании [модели развертывания Azure Resource Manager (ARM)](../articles/resource-group-overview.md), когда в примерах используются [командлеты Azure SQL Database PowerShell](https://msdn.microsoft.com/library/azure/mt574084.aspx) на основе ARM. Существующие [командлеты базы данных SQL Azure (классические)](https://msdn.microsoft.com/library/azure/dn546723.aspx) классической модели развертывания поддерживаются для обратной совместимости, поэтому рекомендуется использовать командлеты на основе ARM.


Выполните командлет [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx). Откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте для входа те же учетные данные, что и для входа на портал Azure.

	Add-AzureRmAccount

Если у вас несколько подписок, используйте командлет [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx), чтобы выбрать подписку, которую будет использовать сеанс PowerShell. Чтобы узнать, какие подписки использует текущий сеанс PowerShell, запустите командлет [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx). Чтобы просмотреть все подписки, запустите командлет [**Get AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx).

	Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


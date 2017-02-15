
### <a name="start-your-powershell-session"></a>Запуск сеанса PowerShell
Сначала установите и запустите последнюю версию [Azure PowerShell](https://msdn.microsoft.com/library/mt619274\(v=azure.300\).aspx). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Многие новые функции базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md), поэтому в примерах используются соответствующие [командлеты Azure PowerShell для базы данных SQL](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx). Командлеты [управления службой базы данных SQL Azure](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) в классической модели развертывания для управления службой поддерживаются для обеспечения обратной совместимости, но мы рекомендуем использовать командлеты для Resource Manager.
> 
> 

Выполните командлет [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx). Откроется окно входа, в котором нужно ввести свои учетные данные. Используйте для входа те же учетные данные, что и для входа на портал Azure.

    Add-AzureRmAccount

Если у вас несколько подписок, используйте командлет [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx), чтобы выбрать подписку, которую будет использовать сеанс PowerShell. Чтобы узнать, какую подписку использует текущий сеанс PowerShell, выполните командлет [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx). Чтобы просмотреть все подписки, выполните командлет [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Dec16_HO2-->



## <a name="delete-a-recovery-services-vault-powershell"></a>Удаление хранилища служб восстановления (PowerShell)

1. Получите хранилище служб восстановления.

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. Удалите хранилище.

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> Используйте указанные выше команды с предельной осторожностью. Если вы по ошибке удалите какое-либо хранилище, будут потеряны все хранящиеся в нем данные. Это необратимое действие, которое никак не удастся отменить.  



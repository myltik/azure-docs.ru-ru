Если вы используете файл параметров для передачи значений параметров во время развертывания, то необходимо создать JSON-файл с форматом, как в примере ниже.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

Размер файла параметров не может быть более 64 КБ.

Если требуется предоставить конфиденциальное значение для параметра (например, пароль), добавьте это значение в хранилище ключей. Получите хранилище ключей во время развертывания, как показано в предыдущем примере. Дополнительные сведения см. в статье [Передача безопасных значений в процессе развертывания](../articles/azure-resource-manager/resource-manager-keyvault-parameter.md). 



<!--HONumber=Nov16_HO3-->



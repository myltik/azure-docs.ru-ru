Для добавления тега к ресурсу во время развертывания добавьте элемент `tags` к развертываемому ресурсу и укажите имя и значение тега.

### <a name="apply-literal-value-to-tag-name"></a>Применение литерального значения к имени тега
В следующем примере показана учетная запись хранения с двумя тегами (`Dept` и `Environment`), которым присвоены литеральные значения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

### <a name="apply-object-to-tag-element"></a>Применение объекта к элементу тега
Можно определить параметр объекта, который хранит несколько тегов, и применить этот объект к элементу тега. Каждое свойство в объекте становится отдельным тегом ресурса. В следующем примере содержится параметр с именем `tagValues`, который применяется к элементу тега.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": "[parameters('tagValues')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }
  ]
}
```

### <a name="apply-json-string-to-tag-name"></a>Применение строки JSON к имени тега

Для хранения большого количества значений в одном теге примените строку JSON, представляющую значения. Вся строка JSON сохраняется как один тег, значение которого не может превышать 256 символов. В следующем примере приведен один тег с именем `CostCenter`, содержащий несколько значений из строки JSON.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```
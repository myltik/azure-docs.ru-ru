Чтобы добавить тег к группе ресурсов, используйте **набор групп Azure**. Если у группы ресурсов нет тегов, передайте один.

```azurecli
azure group set -n tag-demo-group -t Dept=Finance
```

При этом обновляются все теги. Если вы хотите добавить тег к группе ресурсов, в которой уже есть теги, передайте их все. 

```azurecli
azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
```

Ресурсы не унаследуют теги в группе ресурсов. Чтобы добавить тег к ресурсу, используйте **набор ресурсов Azure**. Передайте номер версии API для типа ресурса, к которому добавляется тег. Если нужно получить версию API, используйте следующую команду, указав поставщик для настраиваемого типа ресурсов:

```azurecli
azure provider show -n Microsoft.Storage --json
```

В результатах найдите нужный тип ресурса.

```azurecli
"resourceTypes": [
{
  "resourceType": "storageAccounts",
  ...
  "apiVersions": [
    "2016-01-01",
    "2015-06-15",
    "2015-05-01-preview"
  ]
}
...
```

Теперь укажите эту версию API, имя группы ресурсов, имя ресурса, тип ресурса и значение тега в качестве параметров.

```azurecli
azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01
```

Существующие теги находятся непосредственно в ресурсах и группах ресурсов. Чтобы просмотреть существующие теги, запросите сведения о группе ресурсов и ее ресурсах с помощью команды **azure group show**.

```azurecli
azure group show -n tag-demo-group --json
```

Отобразятся метаданные о группе ресурсов, включая все примененные к ней теги.

```azurecli
{
  "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
  "name": "tag-demo-group",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "location": "southcentralus",
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Project": "Upgrade"
  },
  ...
}
```

Теги для конкретного ресурса можно просмотреть с помощью команды **azure resource show**.

```azurecli
azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json
```

Чтобы получить все ресурсы со значением тега, используйте следующую команду:

```azurecli
azure resource list -t Dept=Finance --json
```

Чтобы получить все группы ресурсов со значением тега, используйте следующую команду:

```azurecli
azure group list -t Dept=Finance
```

Чтобы просмотреть существующие теги в подписке, используйте следующую команду:

```azurecli
azure tag list
```

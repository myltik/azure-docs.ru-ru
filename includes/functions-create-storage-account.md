## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

В службе "Функции" используется учетная запись хранения Azure общего назначения для сохранения состояния и других сведений о функциях. Создайте учетную запись хранения общего назначения в созданной вами группе ресурсов с помощью команды [az storage account create](/cli/azure/storage/account#create).

В следующей команде замените `<storage_name>` глобально уникальным именем своей учетной записи хранения везде, где встречается этот заполнитель. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

После создания учетной записи хранения в Azure CLI отобразятся следующие сведения:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```
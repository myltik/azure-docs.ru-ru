Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Чтобы просмотреть доступные расположения, выполните команду `az appservice list-locations`. Обычно ресурсы создаются в ближайших регионах.

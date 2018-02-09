В Cloud Shell создайте группу ресурсов с помощью команды [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *Западная Европа*. Чтобы просмотреть все поддерживаемые расположения для службы приложений, выполните команду `az appservice list-locations`.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Обычно группа ресурсов и ресурсы создаются в ближайших регионах. 

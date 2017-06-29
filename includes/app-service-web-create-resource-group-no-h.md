Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Обычно группа ресурсов и ресурсы создаются в ближайших регионах. Чтобы просмотреть все поддерживаемые расположения для веб-приложений Azure, выполните команду `az appservice list-locations`. 
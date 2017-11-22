## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (приложений-функций, баз данных и учетных записей хранения) и управление ими.

В следующем примере создается группа ресурсов `myResourceGroup`.  
Если вы не используете Cloud Shell, сначала выполните вход с помощью `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Обычно группа ресурсов и ресурсы создаются в ближайших регионах. Чтобы просмотреть все поддерживаемые расположения для планов службы приложений, выполните команду [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations).
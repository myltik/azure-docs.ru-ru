## <a name="create-a-resource-group"></a>Создание группы ресурсов

 [Группы ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) — это логический контейнер для управления ресурсами, такими как веб-приложения и базы данных. Развертывание, обновление и удаление ресурсов выполняется в группе ресурсов.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create).

```azurecli
az group create --name myResourceGroup --location westeurope
```

Чтобы просмотреть доступные расположения, выполните команду `az appservice list-locations`. Обычно ресурсы создаются в ближайших регионах.

## <a name="create-an-azure-app-service-plan"></a>Создание плана службы приложений Azure

Создайте [план службы приложений](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) уровня "Бесплатный", выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan.md)]

Следующая команда создает план службы приложений с именем `quickStartPlan` и ценовой категорией **Бесплатный**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

После создания плана службы приложений в Azure CLI отображается информация следующего вида:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Создание веб-приложения
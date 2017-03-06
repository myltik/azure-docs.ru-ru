## <a name="prerequisites"></a>Предварительные требования

Получите [бесплатную пробную подписку Azure](https://azure.microsoft.com/pricing/free-trial/) (если вы еще этого не сделали) и установите [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="create-the-scale-set"></a>Создание масштабируемого набора

Сначала создайте группу ресурсов, в которую будет развертываться масштабируемый набор:

```azurecli
az group create --location westus --name myResourceGroup
```

Теперь создайте масштабируемый набор с помощью команды `az vmss create`. В следующем примере создается масштабируемый набор Linux с именем `myvmss` в группе ресурсов с именем `myrg`:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

В следующем примере создается масштабируемый набор Windows с такой же конфигурацией:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Если вам нужно выбрать другой образ операционной системы, то вы можете просмотреть доступные образы с помощью команды `az vm image list` или `az vm image list --all`. Чтобы отобразить сведения о подключении для виртуальных машин в масштабируемом наборе, используйте команду `az vmss list_instance_connection_info`:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```
---
title: Создание шлюза приложений с перенаправлением на основе URL-пути при помощи Azure CLI
description: Узнайте, как создать шлюз приложений с перенаправлением трафика на основе URL-пути с помощью Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 4/27/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: c50a97add735138c295aeb7bf5759f7d5ec54600
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355990"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Руководство по созданию шлюза приложений с перенаправлением на основе URL-пути при помощи Azure CLI

Для настройки [правил маршрутизации на основе URL-пути](application-gateway-url-route-overview.md) при создании [шлюза приложений](application-gateway-introduction.md) можно использовать Azure CLI. В этом руководстве мы создадим внутренние пулы с использованием [масштабируемых наборов виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Затем вы создадите правила маршрутизации на основе URL-адресов, которые обеспечивают перенаправление веб-трафика в соответствующий внутренний пул.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка сети
> * Создание шлюза приложений
> * добавление прослушивателей и правил маршрутизации;
> * создание масштабируемых наборов виртуальных машин для внутренних пулов.

Ниже приведен пример трафика сайта. Трафик поступает из портов 8080 и 8081 и перенаправляется в те же внутренние пулы.

![Пример маршрутизации для URL-адресов](./media/tutorial-url-redirect-cli/scenario.png)

При необходимости инструкции из этого руководства можно выполнить с помощью [Azure PowerShell](tutorial-url-redirect-powershell.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#create).

В следующем примере создается группа ресурсов с именем *myResourceGroupAG* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов 

Создайте виртуальную сеть с именем *myVNet* и подсеть *myAGSubnet* с помощью команды [az network vnet create](/cli/azure/network/vnet#az_net). Затем добавьте подсеть с именем *myBackendSubnet*, необходимую для внутренних серверов, используя команду [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Создайте общедоступный IP-адрес с именем *myAGPublicIPAddress*, используя команду [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Выполните команду [az network application-gateway create](/cli/azure/application-gateway#create), чтобы создать шлюз приложений myAppGateway. При создании шлюза приложений с помощью Azure CLI укажите такие сведения о конфигурации, как емкость, номер SKU и параметры HTTP. Шлюз приложений назначается подсети *myAGSubnet* и адресу *myPublicIPSddress*, созданным ранее.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Создание шлюза приложений может занять несколько минут. Когда шлюз приложений будет создан, вы увидите такие новые функции:

- *appGatewayBackendPool* — шлюз приложений должен иметь по крайней мере один внутренний пул адресов.
- *appGatewayBackendHttpSettings* — указывает, что для обмена данными используются порт 80 и протокол HTTP.
- *appGatewayHttpListener* — прослушиватель по умолчанию, связанный с *appGatewayBackendPool*.
- *appGatewayFrontendIP* — назначает адрес *myAGPublicIPAddress* для прослушивателя *appGatewayHttpListener*.
- *rule1* — правило маршрутизации по умолчанию, связанное с прослушивателем *appGatewayHttpListener*.


### <a name="add-backend-pools-and-ports"></a>Добавление внутренних пулов и портов

Вы можете добавить в шлюз приложений внутренние пулы адресов с именами *imagesBackendPool* и *videoBackendPool* с помощью команды [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Интерфейсные порты для пулов можно добавить при помощи команды [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Добавление прослушивателей и правил

### <a name="add-listeners"></a>Добавление прослушивателей

Добавьте серверные прослушиватели с именами *backendListener* и *redirectedListener*, необходимые для маршрутизации трафика, при помощи команды [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Добавление сопоставления URL-путей по умолчанию

Сопоставления URL-путей гарантируют, что определенные URL-адрес маршрутизируются в определенные внутренние пулы. Вы можете создать сопоставления URL-путей с именами *imagePathRule* и *videoPathRule* при помощи команд [az network application-gateway url-path-map create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) и [az network application-gateway url-path-map rule create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create).

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Добавление конфигурации перенаправления

Можно настроить перенаправление для прослушивателя с помощью команды [az network application-gateway redirect-config create](/cli/azure/application-gateway#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Добавление сопоставления URL-путей для перенаправления

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Добавление правил маршрутизации

Правила маршрутизации связывают сопоставления URL-путей с созданными прослушивателями. Вы можете добавить правила *defaultRule* и *redirectedRule* с помощью команды [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Создание масштабируемых наборов виртуальных машин

В этом примере вы создадите три масштабируемых набора виртуальных машин, которые поддерживают три созданных внутренних пула. Имена создаваемых масштабируемых наборов — *myvmss1*, *myvmss2* и *myvmss3*. Каждый масштабируемый набор содержит два экземпляра виртуальной машины, на которых устанавливаются службы NGINX.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Установка nginx

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Чтобы получить общедоступный IP-адрес шлюза приложений, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера. Например *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, *http://40.121.222.19:8080/video/test.htm* или *http://40.121.222.19:8081/images/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Тестирование базового URL-адреса в шлюзе приложений](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Измените URL-адрес на http://&lt;ip-address&gt;:8080/images/test.html, указав вместо заполнителя &lt;ip-address&gt; фактический IP-адрес. Результат должен быть примерно таким:

![Тестирование URL-адреса изображений в шлюзе приложений](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Измените URL-адрес на http://&lt;ip-address&gt;:8080/video/test.html. Подставьте вместо &lt;ip-address&gt; свой IP-адрес. Результат должен быть примерно таким:

![Тестирование URL-адреса видео в шлюзе приложений](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Теперь измените URL-адрес на http://&lt;ip-address&gt;:8081/images/test.html, заменив &lt;ip-address&gt; своим IP-адресом. Вы увидите, что трафик перенаправляется обратно во внутренний пул образов по адресу http://&lt;ip-address&gt;:8080/images.

## <a name="clean-up-resources"></a>Очистка ресурсов

При необходимости вы можете удалить группу ресурсов, шлюз приложений и все связанные ресурсы.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```
## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Настройка сети
> * Создание шлюза приложений
> * добавление прослушивателей и правил маршрутизации;
> * создание масштабируемых наборов виртуальных машин для внутренних пулов.

> [!div class="nextstepaction"]
> [Дополнительные сведения о возможностях шлюза приложений](application-gateway-introduction.md)
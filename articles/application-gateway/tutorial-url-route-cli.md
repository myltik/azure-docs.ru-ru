---
title: Руководство. Маршрутизация веб-трафика на основе URL-адресов с помощью Azure CLI
description: Узнайте, как маршрутизировать веб-трафик на основе URL-адреса в определенные масштабируемые пулы серверов с помощью Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 4/27/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8c87206f75114cb3947d57180f570f8defaf41ea
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Руководство. Маршрутизация веб-трафика на основе URL-адресов с помощью Azure CLI

С помощью Azure CLI можно настраивать маршрутизацию веб-трафика в определенные серверные пулы на основе URL-адреса, который используется для доступа к приложению. В рамках этого руководства вы создадите [шлюз приложений Azure](application-gateway-introduction.md) с тремя серверными пулами, в которых используется служба [Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) У каждого серверного пула свое назначение. Например, они могут использоваться для данных, изображений и видео.  Если направлять трафик в отдельные пулы, пользователи будут получать нужную информацию в нужное время.

Чтобы включить маршрутизацию трафика, создайте [правила маршрутизации](application-gateway-url-route-overview.md), назначенные прослушивателям, которые ожидают передачи данных через определенные порты. После этого веб-трафик будет поступать на надлежащие серверы в пуле.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка сети
> * создание прослушивателей, сопоставления URL-путей и правил;
> * создание масштабируемых серверных пулов.


![Пример маршрутизации для URL-адресов](./media/tutorial-url-route-cli/scenario.png)

При необходимости инструкции из этого руководства можно выполнить с помощью [Azure PowerShell](tutorial-url-route-powershell.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором выполняется развертывание ресурсов Azure и управление ими. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#create).

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

## <a name="create-the-application-gateway-with-url-map"></a>Создание шлюза приложений с сопоставлением URL-адресов

Выполните команду [az network application-gateway create](/cli/azure/application-gateway#create), чтобы создать шлюз приложений myAppGateway. При создании шлюза приложений с помощью Azure CLI укажите такие сведения о конфигурации, как емкость, номер SKU и параметры HTTP. Шлюз приложений назначается подсети *myAGSubnet* и адресу *myAGPublicIPAddress*, созданным ранее. 

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


### <a name="add-image-and-video-backend-pools-and-port"></a>Добавление внутреннего порта и внутренних пулов для изображений и видео

Добавьте внутренние пулы с именами *imagesBackendPool* и *videoBackendPool* в шлюз приложений с помощью [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Интерфейсный порт для пулов можно добавить при помощи [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

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
  --name port8080
```

### <a name="add-backend-listener"></a>Добавление серверного прослушивателя

Добавьте серверный прослушиватель с именем *backendListener*, необходимый для маршрутизации трафика, при помощи [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Добавление сопоставления URL-путей

Сопоставления URL-путей гарантируют, что трафик с определенных URL-адресов маршрутизируется в определенные внутренние пулы. Создайте сопоставления URL-путей с именами *imagePathRule* и *videoPathRule* с помощью команд [az network application-gateway url-path-map create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) и [az network application-gateway url-path-map rule create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create).

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
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
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Добавление правила маршрутизации

Правило маршрутизации связывает сопоставления URL-адресов с созданным прослушивателем. Добавьте правило с именем *rule2* с помощью команды [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Создание масштабируемых наборов виртуальных машин

В рамках этого руководства вы создадите три масштабируемых набора виртуальных машин, которые поддерживают три созданных внутренних пула. Вы создадите масштабируемые наборы с именами *myvmss1*, *myvmss2* и *myvmss3*. Каждый масштабируемый набор содержит два экземпляра виртуальной машины, на которых устанавливаются службы NGINX.

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
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Чтобы получить общедоступный IP-адрес шлюза приложений, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера. Например *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* или *http://40.121.222.19:8080/video/test.htm*.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Тестирование базового URL-адреса в шлюзе приложений](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Измените URL-адрес на http://&lt;ip-address&gt;:8080/video/test.html. Подставьте вместо &lt;ip-address&gt; свой IP-адрес. Результат должен быть примерно таким:

![Тестирование URL-адреса изображений в шлюзе приложений](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Измените URL-адрес на http://&lt;ip-address&gt;:8080/video/test.html. Подставьте вместо &lt;ip-address&gt; свой IP-адрес. Результат должен быть примерно таким:

![Тестирование URL-адреса видео в шлюзе приложений](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Можно удалить группу ресурсов, шлюз приложений и все связанные с ними ресурсы, если они больше не требуются.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Настройка сети
> * создание прослушивателей, сопоставления URL-путей и правил;
> * создание масштабируемых серверных пулов.

> [!div class="nextstepaction"]
> [Создание шлюза приложений с перенаправлением на основе URL-пути](./tutorial-url-redirect-cli.md)

---
title: Создание шлюза приложений с внутренним перенаправлением при помощи Azure CLI | Документация Майкрософт
description: Узнайте, как создать шлюз приложений, который перенаправляет внутренний веб-трафик в соответствующий пул с помощью Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: 42b45d07c8ea326f0daa8f0e6efd7cf567dbfd1b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355820"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Создание шлюза приложений с перенаправлением внутреннего трафика при помощи Azure CLI

С помощью Azure CLI можно настроить [перенаправление веб-трафика](application-gateway-multi-site-overview.md) при создании [шлюза приложений](application-gateway-introduction.md). В этом руководстве вы создадите внутренний пул с использованием масштабируемого набора виртуальных машин. Затем вы настроите прослушиватели и правила на основе принадлежащих вам доменов, чтобы обеспечить передачу веб-трафика в соответствующий пул. В этом руководстве предполагается, что вам принадлежат несколько доменов. Для примера используются *www.contoso.com* и *www.contoso.org*.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Настройка сети
> * Создание шлюза приложений
> * добавление прослушивателей и правила перенаправления;
> * создание масштабируемого набора виртуальных машин с внутренним пулом;
> * создание записи CNAME в домене.

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

Создайте виртуальную сеть с именем *myVNet* и подсеть *myAGSubnet* с помощью команды [az network vnet create](/cli/azure/network/vnet#az_net). Затем добавьте подсеть с именем *myBackendSubnet*, необходимую для внутреннего пула серверов, используя команду [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Создайте общедоступный IP-адрес с именем *myAGPublicIPAddress*, используя команду [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

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

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Выполните команду [az network application-gateway create](/cli/azure/application-gateway#create), чтобы создать шлюз приложений *myAppGateway*. При создании шлюза приложений с помощью Azure CLI укажите такие сведения о конфигурации, как емкость, номер SKU и параметры HTTP. Шлюз приложений назначается подсети *myAGSubnet* и адресу *myAGPublicIPAddress*, созданным ранее. 

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

Создание шлюза приложений может занять несколько минут. Когда шлюз приложений будет создан, вы увидите такие новые функции шлюза:

- *appGatewayBackendPool* — шлюз приложений должен иметь по крайней мере один внутренний пул адресов.
- *appGatewayBackendHttpSettings* — указывает, что для обмена данными используются порт 80 и протокол HTTP.
- *appGatewayHttpListener* — прослушиватель по умолчанию, связанный с *appGatewayBackendPool*.
- *appGatewayFrontendIP* — назначает адрес *myAGPublicIPAddress* для прослушивателя *appGatewayHttpListener*.
- *rule1* — правило маршрутизации по умолчанию, связанное с прослушивателем *appGatewayHttpListener*.


## <a name="add-listeners-and-rules"></a>Добавление прослушивателей и правил 

Прослушиватель требуется для того, чтобы шлюз приложений правильно маршрутизировал трафик на внутренние пулы. В этом руководстве создаются два прослушивателя для двух ваших доменов. В этом примере создаются прослушиватели для доменов *www.contoso.com* и *www.contoso.org*.

Добавьте серверные прослушиватели, необходимые для маршрутизации трафика, при помощи команды [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Добавление конфигурации перенаправления

Добавьте конфигурацию перенаправления, которая позволяет отправлять трафик с *www.consoto.org* на прослушиватель для *www.consoto.org* в шлюзе приложения с помощью команды [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoComListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Добавление правил маршрутизации

Правила обрабатываются в порядке их создания. Трафик направляется при помощи первого правила, которое соответствует URL-адресу, отправленному в шлюз приложений. Базовое правило, которое было создано по умолчанию, в этом руководстве не требуется. В этом примере создаются два правила с именами *contosoComRule* и *contosoOrgRule* и удаляется правило, которое было создано по умолчанию.  Вы можете добавить правила с помощью команды [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Создание масштабируемых наборов виртуальных машин

В этом примере вы создадите масштабируемый набор виртуальных машин, который поддерживает созданный вами внутренний пул по умолчанию. Созданный масштабируемый набор называется *myvmss* и содержит два экземпляра виртуальной машины, на которых устанавливается NGINX.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Установка nginx

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Создание записи CNAME в домене

После создания шлюза приложений с общедоступным IP-адресом можно получить DNS-адрес и использовать его для создания записи CNAME в своем домене. С помощью команды [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) можно получить DNS-адрес шлюза приложений. Скопируйте значение *fqdn* для DNSSettings и используйте его в качестве значения создаваемой записи CNAME. Использовать записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска шлюза приложений.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

В адресной строке браузера введите имя домена. Например, http://www.contoso.com.

![Проверка сайта contoso в шлюзе приложений](./media/tutorial-internal-site-redirect-cli/application-gateway-nginxtest.png)

Измените адрес на другой домен, например http://www.contoso.org. Вы должны увидеть, что трафик перенаправляется в прослушиватель для www.contoso.com.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Настройка сети
> * Создание шлюза приложений
> * добавление прослушивателей и правила перенаправления;
> * создание масштабируемого набора виртуальных машин с внутренним пулом;
> * создание записи CNAME в домене.

> [!div class="nextstepaction"]
> [Дополнительные сведения о возможностях шлюза приложений](./application-gateway-introduction.md)
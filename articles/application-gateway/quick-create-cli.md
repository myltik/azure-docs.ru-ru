---
title: Краткое руководство. Направление веб-трафика с помощью шлюза приложений Azure и Azure CLI | Документация Майкрософт
description: Узнайте, как с помощью Azure CLI создать шлюз приложений Azure, который направляет веб-трафик к виртуальным машинам в серверном пуле.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99c3975c6ab2c7a20dfbab519dae575a2a61465f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32160364"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Краткое руководство. Направление веб-трафика с помощью шлюза приложений Azure и Azure CLI

Шлюз приложений Azure позволяет направлять веб-трафик приложения к определенным ресурсам. Для этого портам назначаются прослушиватели, создаются определенные правила, и в серверный пул добавляются соответствующие ресурсы.

Из этого краткого руководства вы узнаете, как с помощью Azure CLI быстро создать шлюз приложений с двумя виртуальными машинами в серверном пуле. Затем вы протестируете шлюз, чтобы убедиться, что он работает правильно.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Для ресурсов обязательно нужно создать группу ресурсов. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#az_group_create). 

В следующем примере создается группа ресурсов с именем *myResourceGroupAG* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов 

Для шлюза приложений нужно создать виртуальную сеть, чтобы он мог обмениваться данными с другими ресурсами. Вы можете создать виртуальную сеть во время создания шлюза приложений. В этом примере создаются две подсети: одна предназначена для шлюза приложений, а другая — для виртуальных машин. 

Создайте виртуальную сеть и подсеть, выполнив команду [az network vnet create](/cli/azure/vnet#az_vnet_create). Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/public-ip#az_public_ip_create).

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом примере вы создадите две виртуальные машины, которые будут использоваться как внутренние серверы для шлюза приложений. 

### <a name="create-two-virtual-machines"></a>Создание двух виртуальных машин

Вы также установите службы NGINX на виртуальных машинах, чтобы убедиться, что шлюз приложений успешно создан. Тот же самый файл конфигурации cloud-init можно использовать и для установки NGINX, а также для запуска простого приложения Node.js "Hello World" на виртуальной машине Linux. 

В текущей оболочке создайте файл cloud-init.txt и вставьте в него следующую конфигурацию. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Создайте сетевые интерфейсы с помощью команды [az network nic create](/cli/azure/network/nic#az_network_nic_create). Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm#az_vm_create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений с помощью команды [az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create). При создании шлюза приложений с помощью Azure CLI укажите такие сведения о конфигурации, как емкость, номер SKU и параметры HTTP. Частные IP-адреса сетевых интерфейсов добавляются как внутренний пул шлюза приложений.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Создание шлюза приложений может занять до 30 минут. Когда шлюз приложений будет создан, вы увидите такие функции шлюза:

- *appGatewayBackendPool* — шлюз приложений должен иметь по крайней мере один внутренний пул адресов.
- *appGatewayBackendHttpSettings* — указывает, что для обмена данными используются порт 80 и протокол HTTP.
- *appGatewayHttpListener* — прослушиватель по умолчанию, связанный с *appGatewayBackendPool*.
- *appGatewayFrontendIP* — назначает адрес *myAGPublicIPAddress* для прослушивателя *appGatewayHttpListener*.
- *rule1* — правило маршрутизации по умолчанию, связанное с прослушивателем *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Для создания шлюза приложений не требуется устанавливать сервер NGINX. Но в рамках этого руководства он устанавливается для того, чтобы проверить, создан ли шлюз приложений. Чтобы получить общедоступный IP-адрес шлюза приложений, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Тестирование шлюза приложений](./media/quick-create-cli/application-gateway-nginxtest.png)

Когда вы обновляете браузер, должно отобразиться имя другой виртуальной машины.

## <a name="clean-up-resources"></a>Очистка ресурсов

Сначала просмотрите ресурсы, созданные вместе со шлюзом приложений. Затем вы можете удалить группу ресурсов, шлюз приложений и все связанные с ними ресурсы, если они больше не требуются. Для этого выполните команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по управлению веб-трафиком с помощью шлюза приложений и Azure CLI](./tutorial-manage-web-traffic-cli.md)


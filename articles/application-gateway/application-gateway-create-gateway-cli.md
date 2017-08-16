---
title: "Создание шлюза приложений Azure с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как создать шлюз приложений с помощью Azure CLI 2.0 в Resource Manager"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 052410db8c7619c7990dc319951a55663f2c2ba1
ms.contentlocale: ru-ru
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Создание шлюза приложений с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-gateway-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-create-gateway.md)
> * [Шаблон Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Шлюз приложений — это выделенный виртуальный модуль, предоставляющий контроллер доставки приложений (ADC) как услугу, который обеспечивает для приложения множество функций балансировки нагрузки уровня 7.

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md) — это интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager.

## <a name="prerequisite-install-the-azure-cli-20"></a>Предварительные требования. Установка Azure CLI 2.0

Для выполнения действий, описанных в этой статье, требуется [установить интерфейс командной строки Azure для Mac, Linux и Windows (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Если у вас нет учетной записи Azure, то вам потребуется получить ее. Зарегистрируйтесь, чтобы получить [бесплатную пробную версию](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Сценарий

В этом сценарии вы узнаете, как создать шлюз приложений с помощью портала Azure.

Вы узнаете:

* как создать средний шлюз приложений с двумя экземплярами;
* как создать виртуальную сеть AdatumAppGatewayVNET с зарезервированным блоком CIDR (10.0.0.0/16);
* как создать подсеть с именем Appgatewaysubnet и блоком CIDR (10.0.0.0/28);

> [!NOTE]
> Дополнительная настройка шлюза приложений, включая пользовательские пробы работоспособности, серверный пул адресов и дополнительные правила, осуществляется после настройки шлюза приложений, а не во время первоначального развертывания.

## <a name="before-you-begin"></a>Перед началом работы

Шлюзу приложений Azure требуется собственная подсеть. При создании виртуальной сети обязательно оставьте достаточно адресного пространства для нескольких подсетей. После развертывания шлюза приложений в подсети в нее можно добавлять только дополнительные шлюзы приложений.

## <a name="log-in-to-azure"></a>Вход в Azure

Откройте **командную строку Microsoft Azure**, и выполните вход. 

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Команду `az login` также можно использовать без параметра для входа на устройство, при котором требуется ввести код на странице aka.ms/devicelogin.

Введя предыдущий пример, вы получите код. В браузере перейдите по адресу https://aka.ms/devicelogin, чтобы продолжить процедуру входа.

![Команда, выводящая имя пользователя устройства][1]

В браузере введите полученный код. Вы будете перенаправлены на страницу входа.

![Ввод кода в браузере][2]

Когда вы выполните вход, введя код, закройте браузер, чтобы продолжить работу.

![Вход выполнен][3]

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием шлюза приложений создается группа ресурсов, которая будет содержать шлюз приложений. Ниже показана команда для создания группы ресурсов.

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

IP-адреса серверной части — это IP-адреса внутреннего сервера. Эти значения могут быть частными IP-адресами в виртуальной сети, общедоступными IP-адресами или полными доменными именами для внутренних серверов. В следующем примере создается шлюз приложений с дополнительными параметрами конфигурации, задающими настройки HTTP, порты и правила.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

В предыдущем примере показано множество свойств, которые не являются обязательными во время создания шлюза приложения. Следующий пример кода создает шлюз приложений с использованием требуемой информации.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> Чтобы вывести список параметров, которые можно указать во время создания, выполните команду `az network application-gateway create --help`.

В этом примере создается базовый шлюз приложений с параметрами по умолчанию для прослушивателя, серверного пула, протокола HTTP серверной части и правил. Вы сможете изменить эти параметры в соответствии с развертыванием после успешного завершения подготовки.
Если на предыдущем шаге вы уже определили для веб-приложения внутренний пул, то после создания шлюза запускается балансировка нагрузки.

## <a name="get-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы гарантировать попадание пользователей на шлюз приложений, можно использовать запись CNAME, чтобы указать общедоступную конечную точку шлюза приложений. [Настройка пользовательского имени домена в Azure](../dns/dns-custom-domain.md). Чтобы настроить псевдоним, извлеките подробные сведения о шлюзе приложений и соответствующий IP-адрес или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. DNS-имя шлюза приложений должно использоваться для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Чтобы удалить все ресурсы, созданные в этой статье, выполните следующие действия:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать пользовательские пробы работоспособности, посетив страницу [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Узнайте, как настроить разгрузку SSL и убрать дорогостоящее шифрование SSL с веб-серверов, посетив страницу [Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png


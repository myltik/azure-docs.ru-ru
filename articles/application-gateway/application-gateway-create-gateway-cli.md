---
title: "Создание шлюза приложений с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как создать шлюз приложений с помощью Azure CLI 2.0 в Resource Manager."
services: application-gateway
documentationcenter: na
author: davidmu1
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
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 9429fafa73392c3ab5d6af844453ba97f7d4f65b
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Создание шлюза приложений с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-gateway-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-create-gateway.md)
> * [Шаблон Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Шлюз приложений Azure — это выделенный виртуальный модуль, предоставляющий контроллер доставки приложений (ADC) как услугу, который обеспечивает для приложения множество функций балансировки нагрузки уровня 7.

## <a name="cli-versions"></a>Версии интерфейса командной строки

Вы можете создать шлюз приложений, используя одну из следующих версий интерфейса командной строки:

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md) — это интерфейс командной строки для классической модели развертывания и модели развертывания с помощью Resource Manager;
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md) — это интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="prerequisite-install-the-azure-cli-20"></a>Предварительные требования. Установка Azure CLI 2.0

Для выполнения действий, описанных в этой статье, требуется [установить Azure CLI для Mac, Linux и Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Для создания шлюза приложений требуется учетная запись Azure. Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Сценарий

В этом сценарии вы узнаете, как создать шлюз приложений с помощью портала Azure.

Вы узнаете:

* как создать средний шлюз приложений с двумя экземплярами;
* как создать виртуальную сеть AdatumAppGatewayVNET с зарезервированным блоком CIDR (10.0.0.0/16);
* как создать подсеть с именем Appgatewaysubnet и блоком CIDR (10.0.0.0/28);

> [!NOTE]
> Дальнейшая настройка шлюза приложений, включая пользовательские пробы работоспособности, серверный пул адресов и дополнительные правила, осуществляется после создания шлюза приложений, а не во время первоначального развертывания.

## <a name="before-you-begin"></a>Перед началом работы

Шлюзу приложений требуется собственная подсеть. При создании виртуальной сети обязательно обеспечьте достаточное адресное пространство для нескольких подсетей. После развертывания шлюза приложений в подсети в нее можно будет добавлять только дополнительные шлюзы приложений.

## <a name="sign-in-to-azure"></a>Вход в Azure

Откройте **командную строку Microsoft Azure** и выполните вход.

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Команду `az login` также можно использовать без параметра для входа на устройство, при котором требуется ввести код на странице aka.ms/devicelogin.

После выполнения предыдущей команды вы получите код. В браузере перейдите по адресу https://aka.ms/devicelogin, чтобы продолжить процедуру входа.

![Команда, выводящая имя пользователя устройства][1]

В браузере введите полученный код. Вы будете перенаправлены на страницу входа.

![Ввод кода в браузере][2]

Введите код, чтобы выполнить вход, а затем закройте браузер, чтобы продолжить.

![Вход выполнен][3]

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Прежде чем создать шлюз приложений, создайте для него группу ресурсов. Используйте следующую команду:

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Используйте внутренние IP-адреса для внутренних серверов. Эти значения могут быть частными IP-адресами в виртуальной сети, общедоступными IP-адресами или полными доменными именами для внутренних серверов. В следующем примере создается шлюз приложений с дополнительными параметрами конфигурации, задающими настройки HTTP, порты и правила.

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

В предыдущем примере показано несколько свойств, которые не являются обязательными во время создания шлюза приложений. Следующий пример кода создает шлюз приложений с использованием требуемой информации.

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
> Чтобы вывести список параметров, указываемых во время создания, выполните команду `az network application-gateway create --help`.

В этом примере создается базовый шлюз приложений с параметрами по умолчанию для прослушивателя, внутреннего пула, внутренними параметрами HTTP и параметрами правил. Вы сможете изменить эти параметры в соответствии с развертыванием после успешного завершения подготовки.

Если веб-приложение было определено с использованием внутреннего пула на предыдущих шагах, то сейчас начнется балансировка нагрузки.

## <a name="get-the-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений
После создания шлюза настраивается внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, то шлюзу приложений требуется динамически назначаемое DNS-имя, не являющееся понятным именем. Чтобы пользователи могли попасть в шлюз приложений, используйте запись CNAME, чтобы указать общедоступную конечную точку шлюза приложений. Дополнительные сведения см. в статье [Использование Azure DNS для указания параметров личного домена для службы Azure](../dns/dns-custom-domain.md).

Чтобы настроить псевдоним, извлеките подробные сведения о шлюзе приложений и соответствующий IP-адрес или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. Используйте DNS-имя шлюза приложений для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Не следует использовать записи A, так как виртуальный IP-адрес может измениться после перезапуска шлюза приложений


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

Чтобы удалить все ресурсы, созданные при работе с этой статьей, выполните приведенную ниже команду.

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как создать пользовательские пробы работоспособности, ознакомьтесь с разделом [Создание пользовательской пробы для шлюза приложений с помощью портала](application-gateway-create-probe-portal.md).

Чтобы узнать, как настроить разгрузку SSL и отказаться от использования дорогостоящей расшифровки SSL на веб-серверах, ознакомьтесь с разделом [Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png


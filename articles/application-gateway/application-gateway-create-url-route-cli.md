---
title: "Создание шлюза приложений с правилами маршрутизации URL-адресов с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "На этой странице приводятся инструкции по созданию и настройке шлюза приложений с правилами маршрутизации URL-адресов."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Создание шлюза приложений с маршрутизацией на основе пути с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [портал Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Маршрутизация на основе URL-пути позволяет связывать маршруты на основе URL-пути HTTP-запросов. При такой маршрутизации проверяется, настроен ли для URL-адресов в шлюзе приложений маршрут к внутреннему пулу, после чего сетевой трафик передается в определенный пул. Как правило, маршрутизация на основе URL-пути используется для распределения запросов содержимого разных типов между разными внутренними пулами серверов.

Шлюз приложений Azure использует два типа правил: правила простой маршрутизации и правила маршрутизации на основе URL-пути. Правила простой маршрутизации предоставляют службу с циклическим перебором для внутренних пулов. Правила маршрутизации на основе пути, помимо циклического распределения, для выбора внутреннего пула также используют шаблон пути URL-адреса запроса.

## <a name="scenario"></a>Сценарий

В следующем примере шлюз приложений обслуживает трафик сайта contoso.com с помощью двух внутренних пулов: пула серверов по умолчанию и пула серверов обработки изображений.

Запросы к http://contoso.com/image* направляются в пул серверов обработки изображений (**imagesBackendPool**). Если шаблон пути не соответствует, шлюз приложений выбирает пул серверов по умолчанию (**appGatewayBackendPool**).

![Маршрут URL-адреса](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Вход в Azure

Откройте **командную строку Microsoft Azure** и выполните вход.

```azurecli
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

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Добавление правила на основе пути для имеющегося шлюза приложений

Ниже приведены инструкции по добавлению правила на основе пути для имеющегося шлюза приложений.
### <a name="create-a-new-back-end-pool"></a>Создание внутреннего пула

Настройте параметр **imagesBackendPool** шлюза приложений для балансировки нагрузки сетевого трафика во внутреннем пуле. В этом примере вы настроите различные параметры новых пулов тыловых серверов. Параметры каждого внутреннего пула можно настроить отдельно. Внутренние параметры HTTP используются правилами на основе пути для перенаправления трафика соответствующим участникам внутреннего пула. Они определяют протокол и порт, используемые для отправки трафика участникам внутреннего пула. Сеансы на основе файлов cookie также определяются внутренними параметрами HTTP.  Если эта функция включена, служба сходства сеансов на основе файлов cookie отправляет для каждого пакета трафик в ту же серверную часть, что и для предыдущих запросов.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Создание интерфейсного порта для шлюза приложений

Объект конфигурации интерфейсного порта используется прослушивателем для определения порта, на котором шлюз приложений ожидает передачи трафика в прослушиватель.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Создание прослушивателя

Этот шаг позволяет настроить прослушиватель для общедоступного IP-адреса и порта, используемых для получения входящего сетевого трафика. В следующем примере используются ранее настроенная внешняя IP-конфигурация, конфигурация интерфейсных портов и протокол (http или https с учетом регистра) и настраивается прослушиватель. В этом примере прослушиватель ожидает передачи трафика HTTP через порт 82 для общедоступного IP-адреса, который был создан ранее.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Создание сопоставления URL-пути

На этом шаге настраивается относительный URL-путь, который шлюз приложений использует для сопоставления пути с внутренним пулом, который назначается для обработки входящего трафика.

> [!IMPORTANT]
> Каждый путь должен начинаться с "/". Звездочка может находиться только в конце пути. Примеры допустимых значений: /xyz, /xyz* или /xyz/*. Строка, передаваемая для сопоставления пути, не должна содержать никакого текста после первого символа "?" или "#", и сами эти символы не допускаются. 

В следующем примере создается одно правило для пути /images/\* , которое перенаправляет трафик во внутренний пулу серверов **imagesBackendPool**. Это правило гарантирует, что трафик для каждого набора URL-адресов направляется к серверной части. Например, файл http://adatum.com/images/figure1.jpg передается в **imagesBackendPool**. При настройке сопоставления для пути правил также настраивается пул серверной части по умолчанию, который будет использоваться, если путь не соответствует ни одному из предустановленных правил. Например, файл http://adatum.com/shoppingcart/test.html передается в **pool1** так как этот пул определен как пул по умолчанию для несоответствующего трафика.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Дальнейшие действия

Информацию о разгрузке SSL см. в статье о [настройке шлюза приложений для разгрузки SSL](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

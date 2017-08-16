---
title: "Создание шлюза приложений с использованием правил маршрутизации URL-адресов с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "На этой странице приводятся инструкции по созданию и настройке шлюза приложений Azure с помощью правил маршрутизации URL-адресов."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 958049830d6753ec26635f18f8f8b2fabdec0733
ms.contentlocale: ru-ru
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-using-path-based-routing-with-azure-cli-20"></a>Создание шлюза приложений с помощью маршрутизации на основе пути с использованием Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Маршрутизация на основе URL-адресов позволяет связывать маршруты на основе URL-пути HTTP-запроса. Она проверяет, настроен ли для URL-адресов в шлюзе приложений маршрут к пулу тыловых серверов, и отправляет сетевой трафик в указанный пул. Как правило, маршрутизация на основе URL-адресов используется для распределения запросов содержимого разных типов между разными пулами тыловых серверов.

При маршрутизации на основе URL-адресов к шлюзу приложений добавляется новый тип правил. Шлюз приложений имеет два типа правил: базовые правила и правила на основе пути. Основной тип правил обеспечивает циклическое обслуживание пулов тыловых серверов, а PathBasedRouting наряду с циклическим распространением при выборе пула тыловых серверов учитывает также шаблон URL-адреса запроса.

## <a name="scenario"></a>Сценарий

В следующем примере шлюз приложений обслуживает трафик веб-сайта contoso.com с помощью двух пулов тыловых серверов: пула серверов по умолчанию и пула серверов для изображений.

Запросы http://contoso.com/image* направляются в пул серверов для изображений (imagesBackendPool). Если шаблон пути не совпадает, выбирается пул серверов по умолчанию (appGatewayBackendPool).

![Маршрут URL-адреса](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="log-in-to-azure"></a>Вход в Azure

Откройте **командную строку Microsoft Azure**, и выполните вход. 

```azurecli
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

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Добавление правила на основе пути для имеющегося шлюза приложений

Создание шлюза приложений с определенным правилом на основе пути

### <a name="create-a-new-back-end-pool"></a>Создание внутреннего пула

Настройте параметр **imagesBackendPool** шлюза приложений для балансировки нагрузки сетевого трафика во внутреннем пуле. В этом примере вы настроите различные параметры новых пулов тыловых серверов. Для каждого пула тыловых серверов параметры можно настроить отдельно.  Серверные параметры HTTP используются правилами для перенаправления трафика соответствующим участникам серверного пула. Они определяют протокол и порт, используемый для отправки трафика участникам серверного пула. Сеансы на основе файлов cookie также определяются с помощью серверных параметров HTTP.  Если этот параметр включен, соответствие сеансу на основе файлов cookie отправляет для каждого пакета трафик в ту же серверную часть, что и для предыдущих запросов.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port"></a>Создание внешнего порта

Настройте внешний порт для шлюза приложений. Объект конфигурации интерфейсного порта используется прослушивателем для определения порта, на котором шлюз приложений прослушивает трафик, поступающий в прослушиватель.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Создание прослушивателя

Создайте прослушиватель. Этот шаг позволяет настроить прослушиватель для общедоступного IP-адреса и порта, используемых для получения входящего сетевого трафика. В следующем примере используются ранее настроенная внешняя IP-конфигурация, конфигурация интерфейсных портов и протокол (HTTP или HTTPS) и настраивается прослушиватель. В этом примере прослушиватель прослушивает трафик HTTP через порт 82 для общедоступного IP-адреса, который был создан ранее.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Создание сопоставления URL-пути

Настройте пути URL-правил для пулов тыловых серверов. На этом этапе настраивается относительный путь, который шлюз приложений использует для сопоставления URL-пути с пулом тыловых серверов, который назначается для обработки входящего трафика.

> [!IMPORTANT]
> Каждый путь должен начинаться с косой черты (/). Знак "\*" может находиться только в конце. Примеры допустимых значений: /xyz, /xyz* или /xyz/*. Строка, передаваемая для сопоставления пути, не должна содержать никакого текста после первого символа "?" или "#", и сами эти символы не допускаются. 

В следующем примере создается одно правило для маршрутизации трафика от пути /images/* к пулу тыловых серверов imagesBackendPool. Это правило гарантирует, что трафик для каждого набора URL-адресов направляется к серверной части. Например, файл http://adatum.com/images/figure1.jpg переходит в imagesBackendPool. При настройке сопоставления для пути правил также настраивается пул серверной части по умолчанию, который будет использоваться, если путь не соответствует ни одному из предустановленных правил. Например, файл http://adatum.com/shoppingcart/test.html переходит в pool1, так как этот пул определен как пул по умолчанию для несоответствующего трафика.

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


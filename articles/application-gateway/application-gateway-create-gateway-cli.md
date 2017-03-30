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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 68d3e3ee9b35f2d6d88cde68365cef91d9683462
ms.lasthandoff: 03/01/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Создание шлюза приложений с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-gateway-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-create-gateway.md)
> * [Шаблон Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений выполняет такие функции доставки приложений: балансировка нагрузки HTTP, определение сходства сеансов на основе файлов cookie, разгрузка SSL, применение пользовательских проб работоспособности и поддержка нескольких сайтов.

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
* как настроить сертификат для разгрузки SSL.

![Пример сценария][scenario]

> [!NOTE]
> Дополнительная настройка шлюза приложений, включая пользовательские пробы работоспособности, серверный пул адресов и дополнительные правила, осуществляется после настройки шлюза приложений, а не во время первоначального развертывания.

## <a name="before-you-begin"></a>Перед началом работы

Шлюзу приложений Azure требуется собственная подсеть. При создании виртуальной сети обязательно оставьте достаточно адресного пространства для нескольких подсетей. После развертывания шлюза приложений в подсети в нее можно добавлять только дополнительные шлюзы приложений.

## <a name="log-in-to-azure"></a>Вход в Azure

Откройте **командную строку Microsoft Azure**, и выполните вход. 

```azurecli
az login -u "username"
```

>[ПРИМЕЧАНИЕ] Команду `az login` также можно использовать без параметра для входа на устройство, при котором требуется ввести код на странице aka.ms/devicelogin.

Введя предыдущий пример, вы получите код. В браузере перейдите по адресу https://aka.ms/devicelogin, чтобы продолжить процедуру входа.

![Команда, выводящая имя пользователя устройства][1]

В браузере введите полученный код. Вы будете перенаправлены на страницу входа.

![Ввод кода в браузере][2]

Когда вы выполните вход, введя код, закройте браузер, чтобы продолжить работу.

![Вход выполнен][3]

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием шлюза приложений создается группа ресурсов, которая будет содержать шлюз приложений. Ниже показана команда для создания группы ресурсов.

```azurecli
az resource group create --name myresourcegroup --location "West US"
```

## <a name="create-a-virtual-network-and-subnet"></a>Создание виртуальной сети и подсети

Сразу после создания группы ресурсов создается виртуальная сеть для шлюза приложений.  Как видно из предыдущих заметок о сценарии, в примере для виртуальной сети определено адресное пространство 10.0.0.0/16, а для подсети — 10.0.0.0/28.

```azurecli
az network vnet create \
--name AdatumAppGatewayVNET \
--address-prefix 10.0.0.0/16 \
--subnet-name Appgatewaysubnet \
--subnet-prefix 10.0.0.0/28 \
--resource-group AdatumAppGateway \
--location eastus
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

После создания виртуальной сети и подсети можно считать, что все предварительные требования для шлюза приложений соблюдены. Кроме того, далее вам понадобятся ранее экспортированный PFX-сертификат и пароль для сертификата. IP-адреса, используемые для серверной части, — это IP-адреса для внутреннего сервера. Эти значения могут быть частными IP-адресами в виртуальной сети, общедоступными IP-адресами или полными доменными именами для внутренних серверов.

```azurecli
az network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group AdatumAppGatewayRG \
--vnet-name AdatumAppGatewayVNET \
--vnet-address-prefix 10.0.0.0/16 \
--subnet Appgatewaysubnet \
--subnet-address-prefix 10.0.0.0/28 \
--servers 10.0.0.4 10.0.0.5 \
--cert-file /mnt/c/Users/username/Desktop/application-gateway/fabrikam.pfx \
--cert-password P@ssw0rd \
--capacity 2 \
--sku-tier Standard \
--sku-name Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 443 \
--routing-rule-type Basic \
--http-settings-port 80

```

> [!NOTE]
> Чтобы вывести список параметров, которые можно указать во время создания, выполните следующую команду: **az network application-gateway create --help**.

В этом примере создается базовый шлюз приложений с параметрами по умолчанию для прослушивателя, серверного пула, протокола HTTP серверной части и правил. Он также настраивает разгрузку SSL. Вы сможете изменить эти параметры в соответствии с развертыванием после успешного завершения подготовки.
Если на предыдущем шаге вы уже определили для веб-приложения внутренний пул, то после создания шлюза запускается балансировка нагрузки.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать пользовательские пробы работоспособности, посетив страницу [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Узнайте, как настроить разгрузку SSL и убрать дорогостоящее шифрование SSL с веб-серверов, посетив страницу [Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png


---
title: 'Загрузка сценариев конфигурации VPN-устройств для VPN-подключений типа "сеть — сеть": Azure Resource Manager | Документация Майкрософт'
description: В этой статье описывается, как загрузить сценарии конфигурации VPN-устройств для VPN-подключений типа "сеть — сеть" с VPN-шлюзами Azure с помощью Azure Resource Manager.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: yushwang
ms.openlocfilehash: 254f5012bfbf827aebc20d90405636dcb204193c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30317778"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Загрузка сценариев конфигурации VPN-устройств для VPN-подключений типа "сеть — сеть"

В этой статье описывается, как загрузить сценарии конфигурации VPN-устройств для VPN-подключений типа "сеть — сеть" с VPN-шлюзами Azure с помощью Azure Resource Manager. Детализированный рабочий процесс показан на схеме ниже.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Для следующих устройств доступны следующие сценарии:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>О сценариях конфигурации VPN-устройств

VPN-подключение между локальными сетями состоит из VPN-шлюза Azure, локального VPN-устройства и VPN-туннеля IPsec S2S, соединяющего шлюз и устройство. Стандартный рабочий процесс включает следующие этапы.

1. Создание и настройка VPN-шлюза Azure (шлюза виртуальной сети).
2. Создание и настройка локального сетевого шлюза Azure, представляющего вашу локальную сеть и VPN-устройство.
3. Создание и настройка VPN-подключения Azure между VPN-шлюзом Azure и локальным сетевым шлюзом.
4. Настройка локального VPN-устройства, представленного локальным сетевым шлюзом, для установки фактического VPN-туннеля S2S с VPN-шлюзом Azure.

Шаги 1–3 можно выполнить с помощью [портала](vpn-gateway-howto-site-to-site-resource-manager-portal.md) Azure, [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) или [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Последний шаг включает настройку локальных VPN-устройств за пределами Azure. Эта функция позволяет загружать сценарий конфигурации для VPN-устройства с уже заполненными соответствующими значениями VPN-шлюза Azure, виртуальной сети и префиксов локальных сетевых адресов, а также свойств VPN-подключения и т. д. Вы можете использовать сценарий в качестве отправной точки или применить сценарий непосредственно к своим локальным VPN-устройствам через консоль конфигурации.

> [!IMPORTANT]
> * Синтаксис для каждого сценария конфигурации VPN-устройства отличается и преимущественно зависит от моделей и версий встроенного ПО. Учитывайте модель своего устройства и сведения о версии, когда рассматриваете доступные шаблоны.
> * Некоторые значения параметров в устройстве должны быть уникальными, и их нельзя определить без доступа к устройству. Сценарии конфигурации, созданные Azure, предварительно заполняют эти значения, но вам необходимо убедиться, что эти значения допустимы для вашего устройства. Примеры приведены ниже.
>    * Номера интерфейсов.
>    * Номера списков контроля доступа.
>    * Имена или номера политик и т. д.
> * Перед применением сценария найдите встроенное в него ключевое слово "**REPLACE**", чтобы найти параметры, которые нужно проверить.
> * Некоторые шаблоны включают раздел "**CLEANUP**", который вы можете применить для удаления конфигураций. Разделы очистки закомментированы по умолчанию.

## <a name="download-the-configuration-script-from-azure-portal"></a>Загрузка сценария конфигурации с портала Azure

Создайте VPN-шлюз Azure, локальный сетевой шлюз и ресурс подключения для их соединения. В статье ниже описываются шаги, которые нужно выполнить.

* [Создание подключения типа "сеть — сеть" на портале Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md);

После создания ресурса подключения следуйте приведенным ниже инструкциям, чтобы загрузить сценарии конфигурации VPN-устройства:

1. В браузере откройте [портал Azure](http://portal.azure.com) и при необходимости войдите с помощью учетной записи Azure.
2. Перейдите в созданный ресурс подключения. Список всех ресурсов подключения можно найти, щелкнув "Все службы", затем "Сети" и "Подключения".

    ![connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Щелкните подключение, которое нужно настроить.

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Щелкните ссылку "Скачать конфигурацию", выделенную красным цветом на странице обзора подключения. Откроется страница скачивания конфигурации.

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Выберите семейство моделей и версию встроенного ПО для своего VPN-устройства, а затем нажмите кнопку "Скачать конфигурацию".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Вам будет предложено сохранить загруженный сценарий (текстовый файл) из браузера.
7. После того как вы загрузите сценарий конфигурации, откройте его с помощью текстового редактора и найдите ключевое слово "REPLACE", чтобы идентифицировать и проанализировать параметры, которые может потребоваться заменить.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Загрузка сценария конфигурации с помощью Azure PowerShell

Вы также можете загрузить сценарий конфигурации с помощью Azure PowerShell, как показано в следующем примере:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Применение сценария конфигурации к VPN-устройству

После того как вы загрузили и подтвердили сценарий конфигурации, следующим шагом будет применение сценария к VPN-устройству. Фактическая процедура зависит от типов и моделей VPN-устройств. Ознакомьтесь с руководствами по эксплуатации или инструкциями к VPN-устройствам.

## <a name="next-steps"></a>Дополнительная информация

Продолжите настройку [подключения типа "сеть — сеть"](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
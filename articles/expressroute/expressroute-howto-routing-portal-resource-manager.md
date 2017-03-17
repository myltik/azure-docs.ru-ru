---
title: "Как настроить маршрутизацию для канала ExpressRoute с помощью портала Azure | Документация Майкрософт"
description: "В этой статье описана процедура создания и подготовки частного пиринга, общедоступного пиринга и пиринга Microsoft для канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить пиринги для канала."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 65eef5df67adc089f52d8478fd1bb37a481d7d2a
ms.openlocfilehash: 8d0b9f142ef274667692009bf8dafeb1f2f0f9c7
ms.lasthandoff: 02/22/2017


---
# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Создание и изменение маршрутизации для канала ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-routing-arm.md)
> * [Классическая модель — PowerShell](expressroute-howto-routing-classic.md)
> * [Видео — частный пиринг](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Видео — общедоступный пиринг](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Видео — пиринг Майкрософт](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

В этой статье описано, как создать конфигурацию маршрутизации ExpressRoute и управлять ею, используя портал Azure и модель развертывания с помощью Azure Resource Manager.

**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Предварительные требования для настройки
* Прежде чем приступать к настройке, обязательно изучите [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md).
* Вам потребуется активный канал ExpressRoute. Приступая к работе, [создайте канал ExpressRoute](expressroute-howto-circuit-arm.md) ; он должен быть затем включен на стороне поставщика услуг подключения. Для выполнения описанных ниже командлетов канал ExpressRoute должен быть подготовлен и включен.
* Если вы планируете использовать общий ключ или хэш MD5, используйте его на обеих сторонах туннеля и ограничьте максимальное число символов до 25.

Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно IPVPN, типа MPLS), ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно. 

> [!IMPORTANT]
> Мы пока не предлагаем использовать пиринги, которые настроены поставщиками услуг на портале управления службами. Такая возможность будет предоставлена позже. Перед настройкой пирингов BGP уточните информацию у поставщика услуг.
> 
> 

Для каждого канала ExpressRoute можно настроить один, два или три пиринга (частный пиринг Azure, общедоступный пиринг Azure и пиринг Microsoft). Пиринги можно настраивать в любом порядке, главное, выполнять их конфигурацию по очереди. 

## <a name="azure-private-peering"></a>Частный пиринг Azure
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию частного пиринга Azure для канала ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Создание частного пиринга Azure
1. Настройте канал ExpressRoute. Прежде чем продолжить, убедитесь, что канал полностью подготовлен поставщиком услуг подключения.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Настройте для канала частный пиринг Azure. Прежде чем продолжить, убедитесь в наличии следующих элементов:
   
   * Подсеть /30 для основной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
   * Подсеть /30 для дополнительной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS. Для этого пиринга можно использовать частный номер AS. Не используйте номер 65515.
   * Хэш MD5, если вы решите его использовать. **Это необязательно**.
3. Выберите в таблице пирингов нужный частный пиринг Azure, как показано ниже.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Настройте частный пиринг. В качестве примера можно использовать следующее изображение.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Указав все параметры, сохраните конфигурацию. Когда изменения настройки будут подтверждены, вы увидите примерно такой результат.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>Просмотр сведений о частном пиринге Azure
Чтобы посмотреть свойства частного пиринга Azure, выберите нужный пиринг.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>Обновление конфигурации частного пиринга Azure
Щелкнув строку пиринга, вы сможете изменить свойства пиринга. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>Удаление частного пиринга Azure
Настройку пиринга можно удалить, щелкнув значок "Удалить", как показано ниже.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Общедоступный пиринг Azure
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию общедоступного пиринга Azure для канала ExpressRoute. 

### <a name="to-create-azure-public-peering"></a>Создание общедоступного пиринга Azure
1. Настройте канал ExpressRoute. Прежде чем продолжить, убедитесь, что канал полностью подготовлен поставщиком услуг подключения.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Настройте для канала общедоступный пиринг Azure. Прежде чем продолжить, убедитесь в наличии следующих элементов:
   
   * Подсеть /30 для основной ссылки. 
   * Подсеть /30 для дополнительной ссылки. 
   * Все IP-адреса, которые используются для настройки этого пиринга, должны быть допустимыми IPv4-адресами.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * Хэш MD5, если вы решите его использовать. **Это необязательно**.
3. Выберите в таблице пирингов нужный общедоступный пиринг Azure, как показано ниже.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Настройте общедоступный пиринг. В качестве примера можно использовать следующее изображение.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Указав все параметры, сохраните конфигурацию. Когда изменения настройки будут подтверждены, вы увидите примерно такой результат.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>Просмотр сведений об общедоступном пиринге Azure
Чтобы посмотреть свойства общедоступного пиринга Azure, выберите нужный пиринг.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>Обновление конфигурации общедоступного пиринга Azure
Щелкнув строку пиринга, вы сможете изменить свойства пиринга. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>Удаление общедоступного пиринга Azure
Настройку пиринга можно удалить, щелкнув значок "Удалить", как показано ниже.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Пиринг Майкрософт
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию пиринга Майкрософт для канала ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Создание пиринга Майкрософт
1. Настройте канал ExpressRoute. Прежде чем продолжить, убедитесь, что канал полностью подготовлен поставщиком услуг подключения.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Настройте пиринг Майкрософт для этого канала. Перед началом работы убедитесь, что у вас есть следующие сведения.
   
   * Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
   * Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * **Объявленные префиксы:** необходимо предоставить список всех префиксов, которые вы планируете объявить во время сеанса BGP. Допускаются только общедоступные префиксы IP-адресов. Если планируется отправить набор префиксов, можно отправить список значений, разделенных запятыми. Эти префиксы должны быть зарегистрированы в RIR/IRR на ваше имя.
   * **Клиент ASN:** чтобы объявить префиксы, не связанные с номером AS для пиринга, вы можете указать номер AS, с которым они связаны при регистрации. **Это необязательно**.
   * **Имя реестра маршрутизации:** можно указать RIR/IRR, в котором зарегистрированы номер AS и префиксы. **Это необязательно.**
   * Хэш MD5, если вы решите его использовать. **Это необязательно.**
3. Вы можете выбрать пиринг, который хотите настроить, как показано ниже. Выберите строку пиринга Майкрософт.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Настройте пиринг Майкрософт. В качестве примера можно использовать следующее изображение.
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Указав все параметры, сохраните конфигурацию. 
   
    Если для канала будет установлено состояние "Требуется проверка" (как показано ниже), следует создать запрос в службу поддержки и предоставить сотрудникам поддержки доказательство того, что вы владеете префиксами.    
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    Запрос в службу поддержки можно создать непосредственно на портале, как показано ниже.     

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Когда изменения настройки будут подтверждены, вы увидите примерно такой результат.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>Просмотр сведений о пиринге Майкрософт
Чтобы посмотреть свойства общедоступного пиринга Azure, выберите нужный пиринг.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>Обновление конфигурации пиринга Майкрософт
Щелкнув строку пиринга, вы сможете изменить свойства пиринга. 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>Удаление пиринга Майкрософт
Настройку пиринга можно удалить, щелкнув значок "Удалить", как показано ниже.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>Дальнейшие действия
Следующий шаг — [связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Дополнительную информацию о рабочих процессах ExpressRoute см. в статье [Процедуры ExpressRoute для подготовки каналов и состояний каналов](expressroute-workflows.md).
* Дополнительную информацию о пиринге канала см. в статье [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).
* Подробнее о работе с виртуальными сетями см. в статье [Обзор виртуальных сетей](../virtual-network/virtual-networks-overview.md).



---
title: Связывание виртуальной сети с каналом ExpressRoute с помощью портала Azure | Документация Майкрософт
description: Подключение виртуальной сети к каналу Azure ExpressRoute. Пошаговое руководство.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: cherylmc
ms.openlocfilehash: c2bef1d79d3133ea6306928a8c917e1bc3000a58
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874558"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Подключение виртуальной сети к каналу ExpressRoute с помощью портала
> [!div class="op_single_selector"]
> * [портал Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [интерфейс командной строки Azure](howto-linkvnet-cli.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (классическая модель)](expressroute-howto-linkvnet-classic.md)
> 

Эта статья поможет вам создать подключение для связи виртуальной сети с каналом Azure ExpressRoute, используя портал Azure. Виртуальные сети, подключаемые к каналу Azure ExpressRoute, могут быть в той же или другой подписке.

## <a name="before-you-begin"></a>Перед началом работы

* Прежде чем приступить к настройке, изучите [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md).

* Вам потребуется активный канал ExpressRoute.
  * Следуйте инструкциям, чтобы [создать канал ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) и включить его на стороне поставщика услуг подключения.
  * Убедитесь, что для вашего канала настроен частный пиринг Azure. Инструкции по маршрутизации см. в статье [Настройка маршрутизации](expressroute-howto-routing-portal-resource-manager.md).
  * Для создания сквозного подключения обязательно настройте частный пиринг Azure, а также пиринг BGP между вашей сетью и сетью Майкрософт.
  * Вам необходимо создать и полностью подготовить виртуальную сеть и шлюз виртуальной сети. Следуйте инструкциям по [созданию шлюза виртуальной сети для ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Для ExpressRoute используется шлюз виртуальной сети типа ExpressRoute, а не VPN.

* К стандартному каналу ExpressRoute можно подключить не более 10 виртуальных сетей. Если используется стандартный канал ExpressRoute, все виртуальные сети должны находиться в одном геополитическом регионе.

* Отдельную виртуальную сеть можно связать не более чем с четырьмя каналами ExpressRoute. Для создания объекта подключения для каждого канала ExpressRoute, к которому вы подключаетесь, используйте процесс, описанный ниже. Каналы ExpressRoute могут быть размещены в той же подписке, в других подписках или и там, и там.

* Если включить надстройку ExpressRoute Premium, вы сможете подключить к каналу ExpressRoute больше виртуальных сетей, включая сети из других геополитических регионов. Дополнительную информацию о надстройке Premium см. в разделе [Вопросы и ответы](expressroute-faqs.md).

* Вы можете [просмотреть видео](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit), прежде чем начать, чтобы лучше понять выполняемые действия.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Подключение виртуальной сети к каналу в одной и той же подписке

> [!NOTE]
> Если пиринги были настроены поставщиком уровня 3, сведения о конфигурации BGP отображаться не будут. Если канал находится в подготовленном состоянии, то вы сможете создавать подключения.
>

### <a name="to-create-a-connection"></a>Создание подключения

1. Убедитесь, что канал ExpressRoute и частный пиринг Azure настроены. Следуйте инструкциям в разделах [Создание и изменение канала ExpressRoute](expressroute-howto-circuit-arm.md) и [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md). Ваш канал ExpressRoute должен выглядеть, как показано на рисунке ниже.

  ![Снимок экрана канала ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Теперь можно приступить к подготовке подключения для связи шлюза виртуальной сети с каналом ExpressRoute. Последовательно выберите **Подключение** > **Добавить**, чтобы открыть страницу **Добавление подключения**, и укажите необходимые параметры.

  ![Снимок экрана добавления подключения](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
3. После успешной настройки подключения в вашем объекте подключения будут показаны сведения о подключении.

  ![Снимок экрана объекта подключения](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Подключение виртуальной сети к каналу в разных подписках

Канал ExpressRoute может совместно использоваться несколькими подписками. На рисунке ниже схематично показан способ совместного использования каналов ExpressRoute несколькими подписками.

![Подключение между подписками](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Каждое маленькое облако внутри большого облака представляет подписки, принадлежащие различным подразделениям одной организации.
- Любое подразделение в организации может использовать свою собственную подписку для развертывания служб. Кроме того, оно может совместно использовать один выделенный канал ExpressRoute для подключения к корпоративной сети.
- Владельцем канала ExpressRoute может выступать одно подразделение (в данном примере — ИТ-подразделение). Другие подписки в организации могут использовать канал ExpressRoute и авторизации, связанные с каналом, в том числе подписки, связанные с другими клиентами Azure Active Directory и регистрациями соглашения Enterprise.

  > [!NOTE]
  > Плата за подключение выделенного канала ExpressRoute и использование полосы пропускания будет взиматься с владельца выделенного канала. Полоса пропускания распределяется между всеми виртуальными сетями.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Администрирование: о владельцах и пользователях канала

Владельцем канала является уполномоченный опытный пользователь ресурса канала ExpressRoute. Владелец канала может создавать разрешения, которые могут быть активированы пользователями канала. Пользователи канала являются владельцами шлюзов виртуальных сетей (которые находятся в разных подписках с каналом ExpressRoute). Пользователи канала могут активировать разрешения (по одному разрешению для каждой виртуальной сети).

Владелец канала имеет право изменить или отменить авторизацию в любое время. Отмена разрешения приводит к удалению всех связывающих подключений из подписки, доступ к которой был отменен.

### <a name="circuit-owner-operations"></a>Действия владельца канала

**Создание разрешения на подключение**

Владелец канала создает разрешение. Это приводит к созданию ключа разрешения, который может использоваться пользователем канала для подключения шлюзов виртуальной сети к каналу ExpressRoute. Разрешение действительно только для одного подключения.

1. На странице ExpressRoute щелкните **Разрешения**, введите **имя** разрешения и щелкните **Сохранить**.

  ![Authorizations](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. После сохранения конфигурации скопируйте **идентификатор ресурса** и **ключ авторизации**.

  ![Ключ авторизации](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Удаление разрешения на подключение**

Подключение можно удалить, щелкнув значок **Удалить** на странице подключения.

### <a name="circuit-user-operations"></a>Действия пользователя канала

Пользователь канала должен получить от владельца канала идентификатор ресурса и ключ авторизации.

**Активация разрешения на подключение**

1. Нажмите кнопку **+Создать**.

  ![Нажмите кнопку "Создать"](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Найдите элемент **Подключение** на сайте Marketplace, выберите его и щелкните **Создать**.

  ![Поиск подключений](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Убедитесь, что выбран **тип подключения** ExpressRoute.
4. Введите необходимые сведения и нажмите кнопку **ОК** на странице "Основные".

  ![Страница "Основные"](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. На странице **Параметры** выберите **Шлюз виртуальной сети** и установите флажок **Активировать авторизацию**.
6. Введите значения **Ключ авторизации** и **Peer circuit URI** (URI однорангового канала) и присвойте подключению имя. Последовательно выберите **ОК**.

  ![Страница «Параметры»](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Просмотрите сведения на странице **Сводка** и нажмите кнопку **ОК**.

**Освобождение разрешения на подключение**

Разрешение можно освободить, удалив подключение, связывающее канал ExpressRoute и виртуальную сеть.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Удаление подключения для разрыва связи с виртуальной сетью

Можно удалить подключение и разорвать связь виртуальной сети с каналом ExpressRoute, выбрав значок **Удалить** на странице соответствующего подключения.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
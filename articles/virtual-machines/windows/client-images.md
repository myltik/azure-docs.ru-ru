---
title: Использование образов клиента Windows в Azure | Документация Майкрософт
description: Узнайте, как использовать преимущества подписки Visual Studio для развертывания Windows 7, Windows 8 или Windows 10 в Azure в сценариях разработки и тестирования.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: aaab69f452db9d4f11af2b5cfd2cd9ff6ac79954
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2018
ms.locfileid: "28103682"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Использование клиента Windows в Azure для сценариев разработки и тестирования
В сценариях разработки и тестирования Azure можно использовать Windows 7, Windows 8 или Windows 10 Корпоративная (x64) при условии, что у вас есть соответствующая подписка Visual Studio (прежнее название — MSDN). В этой статье описываются требования к доступности при запуске Windows 7, Windows 8.1 и Windows 10 Корпоративная в Azure и использовании образов из коллекции Azure.

![Сведения об образах на портале Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Сведения об образах Windows 10 Pro и Windows 10 Pro N из коллекции Azure см. в статье [Как развернуть Windows 10 в Azure с правами на мультитенантное размещение](windows-desktop-multitenant-hosting-deployment.md)
>![Сведения об образах Windows Pro на портале Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Доступность в зависимости от подписки
Активные подписчики Visual Studio (пользователи, которые приобрели лицензию на подписку Visual Studio) могут использовать клиент Windows в целях разработки и тестирования. Вы можете использовать клиент Windows на собственном оборудовании и виртуальных машинах Azure, работающих в любом типе подписки Azure. Клиент Windows не может быть развернут или использоваться в Azure в обычной рабочей среде, а также недоступен для пользователей, не являющихся активными подписчиками Visual Studio.

Для вашего удобства доступны несколько образов Windows 10 из коллекции Azure, которые можно использовать для разработки и тестирования. См. раздел [Доступные предложения](#eligible-offers). Подписчики Visual Studio с предложением любого типа также смогут [правильно подготавливать и создавать](prepare-for-upload-vhd-image.md) 64-разрядные образы Windows 7, Windows 8 или Windows 10, а затем [отправлять их в Azure](upload-generalized-managed.md). Они также могут использоваться только активными подписчиками Visual Studio и только в целях разработки и тестирования.

## <a name="eligible-offers"></a>Доступные предложения
В следующей таблице перечислены идентификаторы предложений, которые доступны для развертывания Windows 10 с помощью коллекции Azure. Образы Windows 10 отображаются только для указанных ниже предложений. Подписчики Visual Studio, которым необходимо запустить клиент Windows с помощью другого типа предложения, должны [правильно подготовить и создать](prepare-for-upload-vhd-image.md) 64-разрядный образ Windows 7, Windows 8 или Windows 10, а затем [передать его в Azure](upload-generalized-managed.md).

| Название предложения | Номер предложения | Доступные образы клиента |
|:--- |:---:|:---:|
| [Разработка и тестирование с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Подписчики Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Подписчики Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Подписчики Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium с подпиской MSDN (преимущество)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Подписчики Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Подписчики Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise — разработка и тестирование](https://azure.microsoft.com/ofers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Проверка подписки Azure
Если вы не знаете идентификатор своего предложения, его можно найти на портале Azure одним из двух способов:  

- В окне *Подписки*:

  ![Сведения об идентификаторе предложения на портале Azure](./media/client-images/offer-id-azure-portal.png) 

- Или щелкните **Выставление счетов** и выберите свой идентификатор подписки. Идентификатор предложения отобразится в окне *Выставление счетов*.

На портале учетных записей Azure просмотреть идентификатор предложения можно на [вкладке "Подписки"](http://account.windowsazure.com/Subscriptions) :

![Сведения об идентификаторе предложения на портале учетных записей Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Дополнительная информация
Теперь вы можете развернуть виртуальные машины с помощью [PowerShell](quick-create-powershell.md), [шаблонов Resource Manager](ps-template.md) или [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).


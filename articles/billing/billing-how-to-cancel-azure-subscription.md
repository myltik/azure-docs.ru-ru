---
title: Отмена подписки Azure | Документация Майкрософт
description: В этой статьей объясняется, как отменить подписку Azure, например бесплатную пробную версию подписки.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 604e2ee6d2ac7e30813f8b4cd02253407c2b1e3f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067441"
---
# <a name="cancel-your-subscription-for-azure"></a>Отмена подписки в Azure

[Администратор учетной записи](billing-subscription-transfer.md#whoisaa) может отменить подписку Azure. После отмены подписки вы больше не сможете получать доступ к службам и ресурсам Azure.

Прежде чем отменить подписку, сделайте следующее:

* Создайте резервную копию данных. Например, если вы храните данные в хранилище Azure или SQL, скачайте копию. При наличии виртуальной машины сохраните ее образ локально.
* Завершите работу служб. Перейдите на [страницу ресурсов на портале управления](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) и выберите команду **Остановить**, чтобы остановить работу всех запущенных виртуальных машин, приложений или других служб.
* Перенесите данные. Ознакомьтесь со статьей [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Перемещение ресурсов в новую группу ресурсов или подписку).

Если вы отмените подписку на платный [план поддержки Azure](https://azure.microsoft.com/support/plans/), вам по-прежнему ежемесячно будет выставляться счет за оставшуюся часть 6-месячного срока действия.

## <a name="cancel-subscription-using-the-azure-portal"></a>Отмена подписки с помощью портала Azure

1. Выберите свою подписку на [странице подписок](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) на портале Azure.
1. Выберите подписку, которую нужно отменить, и щелкните **Отменить подписку**.

    ![Снимок экрана, на котором показана кнопка "Отмена"](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Следуйте инструкциям и завершите отмену.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Что происходит после отмены подписки?

После отмены подписки немедленно прекратится выставление счетов. Однако, отображение отмены на портале может занять до 10 минут.

После этого службы будут отключены. Это означает, что виртуальные машины и временные IP-адреса освобождаются, а хранилище становится доступным только для чтения.

Если вы отменяете подписку в середине расчетного периода, мы отправим вам итоговый счет в день расчета после истечения периода тарификации. 

Мы окончательно удаляем данные только через 90 дней, обеспечивая возможность доступа к ним, если вы измените свое решение. Мы не взимаем плату за хранение данных. Дополнительные сведения см. в разделе [What happens to your data if you leave the service](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Что происходит с данными, когда вы прекращаете использовать службу).

## <a name="reactivate-subscription"></a>Повторная активация подписки

Если вы случайно отменили подписку с оплатой по мере использования, можно [повторно активировать ее в Центре управления учетной записью](billing-subscription-become-disable.md).

Если у вас подписка не по мере использования, обратитесь в службу поддержки в течение 90 дней после отмены подписки для повторной активации.

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.

---
title: "Отмена подписки Azure | Документация Майкрософт"
description: "В этой статьей объясняется, как отменить подписку Azure, например бесплатную пробную версию подписки."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c415fada30aa0b0bd9b9d1e416bc37ef30653f68
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---
# <a name="cancel-your-subscription-for-azure"></a>Отмена подписки в Azure

[Администратор учетной записи](billing-subscription-transfer.md#whoisaa) может отменить подписку Azure. После отмены подписки вы больше не сможете получать доступ к службам и ресурсам Azure.

Прежде чем отменить подписку, сделайте следующее:

* Создайте резервную копию данных. Например, если вы храните данные в хранилище Azure или SQL, скачайте копию. При наличии виртуальной машины сохраните ее образ локально.
* Завершите работу служб. Перейдите на [страницу ресурсов на портале управления](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) и выберите команду **Остановить**, чтобы остановить работу всех запущенных виртуальных машин, приложений или других служб.
* Перенесите данные. Ознакомьтесь со статьей [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Перемещение ресурсов в новую группу ресурсов или подписку).

Если вы отмените подписку на платный [план поддержки Azure](https://azure.microsoft.com/support/plans/), вам по-прежнему ежемесячно будет выставляться счет за оставшуюся часть 6-месячного срока действия.

## <a name="cancel-subscription-using-the-azure-portal"></a>Отмена подписки с помощью портала Azure

1. Выберите свою подписку на [странице подписок](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Выберите подписку, которую нужно отменить, и щелкните **Отменить подписку**.

    ![Снимок экрана, на котором показана кнопка "Отмена"](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)

1. Следуйте инструкциям и завершите отмену.

## <a name="cancel-subscription-using-the-azure-account-center"></a>Отмена подписки с помощью Центра управления учетной записью Azure

1. Войдите в [Центр управления учетной записью Azure](https://account.windowsazure.com/subscriptions) в качестве администратора учетной записи.

1. В разделе **Щелкните подписку для просмотра информации и сведений об использовании**выберите подписку, которую нужно отменить.

    ![Снимок экрана, на котором показан выбранный пример подписки](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)

1. В правой части страницы выберите **Отменить подписку**.

    ![Снимок экрана, на котором показана кнопка "Отменить подписку"](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)

1. Выберите **Да, отменить мою подписку**.

    ![Снимок экрана, на котором показано диалоговое окно "Отмена"](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)

1. Щелкните  ![Кнопка со знаком проверки](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) , чтобы закрыть диалоговое окно и вернуться к странице подписки.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Что происходит после отмены подписки?

После отмены подписки немедленно прекратится выставление счетов. Однако, отображение отмены на портале может занять до 10 минут.

После этого службы будут отключены. Это означает, что виртуальные машины и временные IP-адреса освобождаются, а хранилище становится доступным только для чтения.

Если у вас нет бесплатной пробной версии или доступного кредита, вам будет выставлен счет за любые непогашенные задолженности за использование, которые появились за время с начала цикла выставления счетов до даты отмены подписки. Свой последний счет вы получите в конце цикла выставления счетов.

Мы окончательно удаляем данные через 90 дней после отмены подписки на случай, если они вам понадобятся или вы измените свое решение. Мы не взимаем плату за хранение данных. Дополнительные сведения см. в разделе [What happens to your data if you leave the service](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Что происходит с данными, когда вы прекращаете использовать службу).

## <a name="reactivate-subscription"></a>Повторная активация подписки

Если вы случайно отменили подписку с оплатой по мере использования, можно [повторно активировать ее в Центре управления учетной записью](billing-subscription-become-disable.md).

Если у вас подписка не по мере использования, обратитесь в службу поддержки в течение 90 дней после отмены подписки для повторной активации.

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.


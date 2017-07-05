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
ms.date: 03/29/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a8751fa70bd500a29a3e86de57de4fb919405136
ms.contentlocale: ru-ru
ms.lasthandoff: 03/31/2017


---
# <a name="cancel-your-subscription-for-azure"></a>Отмена подписки в Azure
[Администратор учетной записи](billing-subscription-transfer.md#whoisaa) может отменить подписку Azure. После отмены подписки вы больше не сможете получать доступ к службам и ресурсам Azure.

Прежде чем отменить подписку, сделайте следующее:

* Создайте резервную копию данных. Например, если вы храните данные в хранилище Azure или SQL, скачайте копию. При наличии виртуальной машины сохраните ее образ локально.
* Завершите работу служб. Перейдите на [страницу ресурсов на портале управления](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) и выберите команду **Остановить**, чтобы остановить работу всех запущенных виртуальных машин, приложений или других служб.
* Перенесите данные. Ознакомьтесь со статьей [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Перемещение ресурсов в новую группу ресурсов или подписку).

Если вы отмените подписку на платный [план поддержки Azure](https://azure.microsoft.com/support/plans/), вам по-прежнему ежемесячно будет выставляться счет за оставшуюся часть 6-месячного срока действия.

## <a name="cancel-subscription-via-the-azure-portal"></a>Отмена подписки на портале Azure
1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.

2. В области служб Azure слева выберите **Подписки**.

    ![Снимок экрана, на котором показана кнопка "Подписки"](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. Выберите подписку, которую нужно отменить, и щелкните **Cancel Sub** (Отменить подписку).

    ![Снимок экрана, на котором показана кнопка "Отмена"](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. Введите имя подписки и выберите причину отмены. 
5. Нажмите кнопку **Cancel Sub** (Отменить подписку) внизу.

## <a name="cancel-subscription-by-using-the-azure-account-center"></a>Отмена подписки в Центре управления учетной записью Azure
1. Войдите в [Центр управления учетной записью Azure](https://account.windowsazure.com/subscriptions) в качестве администратора учетной записи.
2. В разделе **Щелкните подписку для просмотра информации и сведений об использовании**выберите подписку, которую нужно отменить.

    ![Снимок экрана, на котором показан выбранный пример подписки](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. В правой части страницы выберите **Отменить подписку**.

    ![Снимок экрана, на котором показана кнопка "Отменить подписку"](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. Выберите **Да, отменить мою подписку**.

    ![Снимок экрана, на котором показано диалоговое окно "Отмена"](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. Щелкните  ![Кнопка со знаком проверки](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) , чтобы закрыть диалоговое окно и вернуться к странице подписки.

## <a name="what-happens-after-you-cancel-your-subscription"></a>Что происходит после отмены подписки?
Отмена отобразится на портале через 10 минут. Выставление счетов прекратится немедленно.

Мы окончательно удаляем данные через 90 дней после отмены подписки на случай, если они вам понадобятся или вы измените свое решение. Дополнительные сведения см. в разделе [What happens to your data if you leave the service](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Что происходит с данными, когда вы прекращаете использовать службу).

## <a name="reactivate-subscription"></a>Повторная активация подписки
Если вы случайно отменили подписку с оплатой по мере использования, можно [повторно активировать ее в Центре управления учетной записью](billing-subscription-become-disable.md).

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.


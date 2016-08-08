<properties
	pageTitle="Скачивание счета на оплату и данных о ежедневном использовании в Azure | Microsoft Azure"
	description="Инструкции по скачиванию счета на оплату и данных о ежедневном использовании в Azure"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="mbaldwin"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="genli"/>

# Скачивание счета на оплату и данных о ежедневном использовании в Azure

Администратор учетной записи Azure может просматривать выставленные счета в центре учетных записей Azure, а также скачивать выписки за шесть предыдущих расчетных периодов. Этот процесс описывается далее.

1. Выполните вход в [центр учетных записей Azure](https://account.windowsazure.com/subscriptions) в качестве администратора учетной записи. На [портале Azure](https://portal.azure.com) в центр учетных записей можно перейти, выбрав **Подписка** > *имя подписки* > **Управление**.

	> [AZURE.NOTE] Доступ к центру учетных записей предоставляется только администратору учетной записи. Дополнительные сведения о том, как узнать, кто является администратором учетной записи для подписки, см. в разделе [Часто задаваемые вопросы](billing-subscription-transfer.md#faq).
	
2. Выберите подписку, по которой необходимо получить информацию о счетах и статистику использования.
3. Щелкните **ЖУРНАЛ ВЫСТАВЛЕННЫХ СЧЕТОВ**. </br>![billinghistory](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)
4. Вы увидите выписки за шесть последних расчетных периодов, а также информацию за текущий период, счет за который еще не выставлен. </br>![billingsum](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)</br>
5. Щелкните **Просмотреть текущую выписку**, чтобы просмотреть сумму к оплате по состоянию на момент создания выписки. Эта информация обновляется только один раз в день, поэтому в ней могут отображаться не все данные об использовании. Сумма в счете за месяц может отличаться от этой предварительной суммы.</br>![billingsum2](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)</br>![billingsum3](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)</br>
6. Выберите **Загрузить накладную**, чтобы просмотреть последний выставленный счет. </br>![DLInvoice](./media/billing-download-azure-invoice-daily-usage-date/DLInvoice1.png)
7. Выберите **Загрузить сведения об использовании** и сохраните CSV-файл с данными о ежедневном использовании.</br>![DLusage](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

<!---HONumber=AcomDC_0727_2016-->
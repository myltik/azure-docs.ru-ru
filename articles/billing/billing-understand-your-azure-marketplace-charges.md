---
title: "Основная информация о расходах на внешние службы в Azure | Документация Майкрософт"
description: "Узнайте больше о выставлении счетов за использование внешних служб, ранее известных как Marketplace, в Azure."
services: 
documentationcenter: 
author: adpick
manager: ruchic
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: adpick
translationtype: Human Translation
ms.sourcegitcommit: 2f6f560b1a0994e99d7a029541ba73e1c8186ff2
ms.openlocfilehash: 68df4e35c4f1ff744053d87273c7d07daca89b41


---
# <a name="understand-your-azure-external-service-charges"></a>Основная информация о расходах на внешние службы в Azure
В этой статье объясняется выставление счетов за использование внешних служб в Azure. Ранее внешние службы назывались заказами Marketplace. Внешние службы предоставляются независимыми поставщиками служб, но полностью интегрируются в экосистему Azure. Ниже перечислено, что вы можете узнать.

* Определение внешних служб.
* Чем выставление счетов за внешние службы отличается от выставления счетов за другие ресурсы Azure.
* Просмотр и отслеживание всех затрат, начисляемых за использование внешних служб.
* Управление заказами внешних служб и способом их оплаты.

## <a name="what-are-azure-external-services"></a>Что такое внешние службы в Azure?
Ранее внешние службы назывались заказами Azure Marketplace. Обычно это службы, опубликованные сторонними производителями для использования в Azure. Например, ClearDB и SendGrid — это внешние службы, которые можно приобрести в Azure, но которые не опубликованы корпорацией Майкрософт.

### <a name="identify-external-services"></a>Определение внешних служб
При подготовке новой внешней службы или ресурса отображается предупреждение.

![Предупреждение о покупке Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Внешние службы публикуются компаниями, не принадлежащими корпорации Майкрософт, но иногда продукты Майкрософт также относят к внешним службам.
> 
> 

### <a name="external-services-are-billed-separately"></a>Счета за внешние службы выставляются отдельно
Внешние службы обрабатываются как отдельные заказы в подписке Azure. Расчетный период для каждой службы задается при ее покупке. Его не следует путать с расчетным периодом подписки, в которой вы приобрели службу. Вы будете получать отдельные счета, плата за которые будет отдельно взиматься с кредитной карты.

### <a name="each-external-service-has-a-different-billing-model"></a>У каждой внешней службы своя модель выставления счетов
Одни службы используют модель с оплатой по мере использования, другие — модель с ежемесячными платежами. Для оплаты внешних служб Azure нужна кредитная карта, их нельзя приобрести посредством оплаты счета.

### <a name="you-cant-use-monthly-free-credits-for-external-services"></a>Для оплаты внешних служб невозможно использовать бесплатные ежемесячные деньги на счете
Если вы используете подписку Azure, которая включает в себя [бесплатные деньги на счете](https://azure.microsoft.com/pricing/spending-limits/), то ими невозможно оплачивать счета за внешние службы. Для покупки внешних служб используйте кредитную карту.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Просмотр расходов на внешние службы и журнала на портале Azure
Можно просмотреть список внешних служб для каждой подписки на [портале Azure](https://portal.azure.com/). 

1. Войдите на [портал Azure](https://portal.azure.com/) в качестве администратора учетной записи.
2. В главном меню выберите **Подписки**.
   
    ![Выбор пункта "Подписки" в главном меню](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. В колонке **Подписки** выберите подписку, которую нужно просмотреть, а затем щелкните **Внешние службы**.
   
    ![Выберите подписку в колонке "Выставление счетов".](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Должны отобразиться все заказы внешних служб, включая имя издателя, приобретенный уровень служб, имя, присвоенное ресурсу, и текущее состояние заказа. Выберите внешнюю службу, чтобы просмотреть предыдущие счета.
   
    ![Выберите внешнюю службу.](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Здесь можно просмотреть прошлые суммы счетов, включая разделение налога.
   
    ![Просмотрите журнал выставленных счетов за внешние службы.](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Просмотр расходов на внешние службы для клиентов с соглашением Enterprise (EA)
Клиенты с соглашением Enterprise могут просматривать данные о расходах на внешние службы и скачивать отчеты на портале EA. Чтобы приступить к работе, см. статью [Azure Marketplace for EA Customers](https://ea.azure.com/helpdocs/azureMarketplace) (Azure Marketplace для клиентов с соглашением Enterprise).

## <a name="manage-payment-methods-for-external-service-orders"></a>Управление способами оплаты заказов внешних служб
Измените метод оплаты заказов внешних служб в [Центре управления учетной записью](https://account.windowsazure.com/).

> [!NOTE]
> Если вы приобрели подписку с рабочей или учебной учетной записью, то вам следует [обратиться в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы изменить метод оплаты.
> 
> 

1. Войдите в [Центр управления учетной записью](https://account.windowsazure.com/) и [перейдите на вкладку **Marketplace**](https://account.windowsazure.com/Store).
   
    ![Выберите вкладку "Marketplace" в Центре управления учетной записью.](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Выберите внешнюю службу для управления.
   
    ![Выберите внешнюю службу для управления.](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. В правой части страницы щелкните **Изменить метод оплаты**. Эта ссылка позволяет открыть другой портал для управления методом оплаты.
   
    ![Сводка заказов](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Щелкните **Изменить информацию** и следуйте инструкциям, чтобы обновить платежную информацию.
   
    ![Выберите "Изменить информацию".](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Отмена заказа внешней службы
Если вы хотите отменить заказ внешней службы, то необходимо удалить соответствующий ресурс на [портале Azure](https://portal.azure.com).

![Удалите ресурс.](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , которая поможет быстро устранить проблему.




<!--HONumber=Feb17_HO2-->



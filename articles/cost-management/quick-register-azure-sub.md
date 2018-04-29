---
title: Регистрация подписки Azure в Azure Cost Management | Документация Майкрософт
description: Используйте подписку Azure для регистрации в службе "Управление затратами Azure".
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: abf169b84170ff44f7a2035acbb1243821f6e596
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Регистрация индивидуальной подписки Azure и просмотр данных о затратах

Для регистрации в службе "Управление затратами Azure" используется подписка Azure. Такая регистрация предоставляет доступ к порталу Cloudyn. В этом кратком руководстве описывается процесс регистрации, который необходимо пройти для создания пробной подписки Cloudyn и входа на портал Cloudyn. Здесь также показано, как начать просматривать данные о затратах.

## <a name="log-in-to-azure"></a>Вход в Azure

- Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="register-with-azure-cost-management"></a>Регистрация в службе "Управление затратами Azure"

1. На портале Azure в списке служб выберите **Cost Management + Billing** (Управление затратами + Выставление счетов).
2. В разделе **Обзор**, щелкните **Управление затратами**.  
    ![Страница "Управление затратами"](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. На странице **Управление затратами** нажмите кнопку **Go to Cost Management** (Перейти к управлению затратами), чтобы открыть страницу регистрации Cloudyn в новом окне.
4. На странице пробной регистрации на портале Cloudyn введите название вашей компании, а затем выберите **Azure Individual Subscription Owner** (Владелец индивидуальной подписки Azure) и нажмите кнопку **Next** (Далее). Ваши имя учетной записи и идентификатор клиента автоматически добавляются в форму.  
    ![пробная регистрация](./media/quick-register-azure-sub/trial-reg-ind.png)
5. В поле **Offer ID - Name** (Идентификатор предложения — имя) выберите значение, связанное с вашей подпиской. Если вы не уверены, какой у вашей подписки идентификатор скорости, то можно просмотреть счет Azure и найти значение **Идентификатор предложения**.
6. Примите условия использования, а затем проверьте свои данные и нажмите кнопку **Next** (Далее).
7. На странице **Gather additional data** (Сбор дополнительных данных) нажмите кнопку **Next** (Далее), чтобы авторизовать Cloudyn для сбора данных ресурсов Azure. Собранные данные включают в себя сведения об использовании, производительности, выставлении счетов, а также данные тегов из подписок.  
    ![сбор дополнительных данных](./media/quick-register-azure-sub/gather-additional.png)
8. Браузер выполнит переход на страницу входа для Cloudyn. Войдите, используя учетные данные своей подписки Azure.
9. Щелкните **Go to Cloudyn** (Перейти к Cloudyn), чтобы открыть портал Cloudyn. Затем на странице **Accounts Management** (Управление учетными записями) должны отобразиться сведения вашей учетной записи подписки Azure.  
    ![Accounts Management](./media/quick-register-azure-sub/accounts-mgt.png) (Управление учетными записями)

Руководство по регистрации подписки Azure см. в видео [Finding your Directory GUID and Rate ID for use in Azure Cost Management](https://youtu.be/PaRjnyaNGMI) (Поиск каталога GUID и идентификатора тарифа для использования в службе "Управление затратами Azure").

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы использовали данные подписки Azure для регистрации в решении Cost Management. Вы также вошли на портал Cloudyn и начали просматривать данные о затратах. Дополнительные сведения о службе "Управление затратами Azure" см. в руководстве по решению "Управление затратами".

> [!div class="nextstepaction"]
> [Просмотр сведений об использовании и затратах](./tutorial-review-usage.md)

---
title: "Регистрация подписки Azure в Azure Cost Management | Документация Майкрософт"
description: "Используйте подписку Azure для регистрации в Azure Cost Management от Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 666919e37ffa3bb039fc9387737b8e6c3789311a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Регистрация индивидуальной подписки Azure и просмотр данных о затратах

Вы можете использовать подписку Azure для регистрации в Azure Cost Management от Cloudyn. Такая регистрация предоставляет доступ к порталу Cloudyn. В этом кратком руководстве описывается процесс регистрации, который необходимо пройти для создания пробной подписки Cloudyn и входа на портал Cloudyn. Здесь также показано, как начать просматривать данные о затратах.

## <a name="log-in-to-azure"></a>Вход в Azure

- Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Создание пробной регистрации

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

Руководство по регистрации подписки Azure см. в видео [Finding your Directory GUID and Rate ID for use in Azure Cost Management by Cloudyn](https://youtu.be/PaRjnyaNGMI) (Поиск каталога GUID и идентификатора тарифа для использования в службе управления затратами Azure от Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы использовали данные подписки Azure для регистрации в решении Cost Management. Вы также вошли на портал Cloudyn и начали просматривать данные о затратах. Дополнительные сведения об Azure Cost Management от Cloudyn см. в руководстве по решению Cost Management.

> [!div class="nextstepaction"]
> [Просмотр сведений об использовании и затратах](./tutorial-review-usage.md)

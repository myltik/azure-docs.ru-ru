---
title: "Регистрация соглашения Azure Enterprise в Azure Cost Management | Документация Майкрософт"
description: "Используйте соглашение Enterprise для регистрации в Azure Cost Management от Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 0f157b465a9da266481be8d208fc18307cd3bb16
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Регистрация соглашения Azure Enterprise и просмотр данных о затратах

Вы можете использовать соглашение Azure Enterprise для регистрации в Azure Cost Management от Cloudyn. Такая регистрация предоставляет доступ к порталу Cloudyn. В этом кратком руководстве описывается процесс регистрации, который необходимо пройти для создания пробной подписки Cloudyn и входа на портал Cloudyn. Здесь также показано, как начать просматривать данные о затратах.

## <a name="log-in-to-azure"></a>Вход в Azure

- Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Создание пробной регистрации

1. На портале Azure в списке служб выберите **Cost Management + Billing** (Управление затратами + Выставление счетов).
2. В разделе **Обзор**, щелкните **Управление затратами**.  
    ![Страница "Управление затратами"](./media/quick-register-ea/cost-mgt-billing-service.png)
3. На странице **Управление затратами** нажмите кнопку **Go to Cost Management** (Перейти к управлению затратами), чтобы открыть страницу регистрации Cloudyn в новом окне.
4. На странице пробной регистрации на портале Cloudyn введите название вашей компании, а затем выберите **Azure Enterprise Enrollment Administrator** (Администратор регистрации Azure Enterprise).  
    ![пробная регистрация](./media/quick-register-ea/trial-reg.png)
5. Введите свой ключ API для регистрации на портале Enterprise Portal. Если у вас нет под рукой ключа, то щелкните ссылку [Enterprise Portal](https://ea.azure.com) и выполните описанные ниже действия.
  1. Войдите на веб-сайт Azure Enterprise и щелкните **Отчеты**. Затем выберите **API Access Key** (Ключ доступа к API) и скопируйте первичный ключ.  
    ![Ключ API EA](./media/quick-register-ea/ea-key.png)
  3. Вернитесь на страницу регистрации и вставьте эти ключ API.
6. Примите условия использования, а затем проверьте свой ключ. Нажмите кнопку **Next** (Далее), чтобы авторизовать Cloudyn для сбора данных ресурсов Azure. Собранные данные включают в себя сведения об использовании, производительности, выставлении счетов, а также данные тегов из подписок.  
    ![проверка ключа](./media/quick-register-ea/ea-key-validated.png)
7. В разделе **Invite other stakeholders** (Пригласить других заинтересованных лиц) можно добавить пользователей, введя их адреса электронной почты. По завершении нажмите кнопку **Next** (Далее). Добавление всех данных о выставлении счетов в Cloudyn занимает около двух часов.
8. Щелкните **Go to Cloudyn** (Перейти к Cloudyn), чтобы открыть портал Cloudyn. Затем на странице **Cloud Accounts Management** (Управление облачными учетными записями) должны отобразиться сведения вашей зарегистрированной учетной записи EA.

Руководство по регистрации соглашения Enterprise см. в видео [How to Find Your EA Enrollment ID and API Key for use in Azure Cost Management by Cloudyn](https://youtu.be/u_phLs_udig) (Как найти свой идентификатор регистрации EA и ключ API для использования в службе управления затратами Azure от Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы использовали данные соглашения Azure Enterprise для регистрации в решении Cost Management. Вы также вошли на портал Cloudyn и начали просматривать данные о затратах. Дополнительные сведения об Azure Cost Management от Cloudyn см. в руководстве по решению Cost Management.

> [!div class="nextstepaction"]
> [Просмотр сведений об использовании и затратах](./tutorial-review-usage.md)

---
title: "Создание учетной записи API-интерфейсов Cognitive Services на портале Azure | Документация Майкрософт"
description: "Описывается, как создать учетную запись API-интерфейсов Microsoft Cognitive Services на портале Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Создание учетной записи API-интерфейсов Cognitive Services на портале Azure

Чтобы использовать API-интерфейсы Microsoft Cognitive Services, необходимо сначала создать учетную запись на портале Azure.

1. Войдите на [портал Azure](http://portal.azure.com).

2. Щелкните **+ Создать**.

3. Выберите **Аналитика** > **Cognitive Services APIs (preview)** (API-интерфейсы Cognitive Services (предварительная версия)).

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. На странице **Создание** введите приведенные ниже сведения.

    -   **Имя учетной записи**: имя используемой учетной записи. Рекомендуется использовать описательное имя, например *AFaceAPIAccount*.

    -   **Подписка**: выберите одну из доступных подписок Azure, в которых имеются разрешения участника.

    -   **Тип API**: выберите API Cognitive Services, который вы хотите использовать. Дополнительные сведения о различных доступных API-интерфейсах Cognitive Services можно получить на сайте [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

    ![Выбор типа API](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **Ценовая категория**: затраты на учетную запись Cognitive Services зависят от фактического использования и выбранных параметров. Дополнительную информацию о ценах на каждый API можно узнать на этих [страницах цен](https://azure.microsoft.com/pricing/details/cognitive-services/).

    -   **Группа ресурсов**: группа ресурсов — это коллекция ресурсов с одинаковым жизненным циклом, разрешениями и политиками. Дополнительные сведения о группах ресурсов см. в статье [Управление ресурсами Azure через портал](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Расположение группы ресурсов**: этот параметр необходимо указать только в том случае, если выбранный API является глобальным (не привязанным к точке). Если API является глобальным и не привязан к расположению, необходимо указать расположение группы ресурсов, в которой будут размещаться метаданные, связанные с учетной записью API-интерфейсов Cognitive Services. Это расположение никак не повлияет на доступность учетной записи во время выполнения. Дополнительные сведения о группе ресурсов см. в статье [Управление ресурсами Azure через портал](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Параметр API**: по умолчанию создание учетных записей отключено, пока [администратор учетных записей Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles) не включит его явным образом.

        Изменение этого параметра будет применяться только к типу API и расположению или расположению группы ресурсов, выбранному на панели слева.

        ![Создание учетной записи API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > Если появилось уведомление о том, что параметр не удалось обновить, значит, вы не выполнили вход как [администратор учетной записи](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts). Администратору учетной записи необходимо выполнить предыдущие шаги, чтобы включить создание учетных записей.
        >
        > ![Сообщение о сбое при обновлении](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        В некоторых случаях администратор учетной записи может не иметь доступа к подписке. Если это так, попросите администратора служб выполнить действия, описанные в документе [Добавление или изменение ролей администратора Azure, управляющих подписками и службами](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).
        
        Чтобы найти администратора учетной записи или администратора служб для своей подписки, выберите свою подписку на [портале Azure](https://portal.azure.com), а затем выберите __Свойства__. Сведения об __администраторе учетной записи__ и __администраторе служб__ отображаются в нижней части колонки "Свойства".
        
        ![Свойства подписки](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Корпорация Майкрософт может использовать данные, отправляемые вами в Cognitive Services, для улучшения продуктов и услуг Майкрософт. Дополнительные сведения см. в [разделе Microsoft Cognitive Services](http://www.microsoft.com/Licensing/product-licensing/products.aspx)
    условий служб Online Services.

5. Чтобы закрепить учетную запись на панели мониторинга на портале Azure, щелкните **Закрепить на панели мониторинга**.

6. Щелкните **Создать** , чтобы создать учетную запись.

После успешного развертывания учетной записи Cognitive Services щелкните ее элемент на панели мониторинга, чтобы просмотреть сведения об этой учетной записи.

**URL-адрес конечной точки** из раздела **Обзор** и ключи из раздела **Ключи** можно использовать, чтобы выполнять вызовы API в своих приложениях.

![Отображение сведений об учетной записи](media/cognitive-services-apis-create-account/display-account.png)

![Отображение ключей учетной записи](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о всех доступных службах Microsoft Cognitive Services см. в разделе [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

- Краткие руководства с примерами использования некоторых API-интерфейсов Cognitive Services:
    - [Начало работы с интерфейсами API текстовой аналитики для определения тональности, ключевых фраз, тем и языка](cognitive-services-text-analytics-quick-start.md)
    - [Краткое руководство по API рекомендаций Cognitive Services](cognitive-services-recommendations-quick-start.md)

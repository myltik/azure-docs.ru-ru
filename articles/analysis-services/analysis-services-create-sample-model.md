---
title: Добавление примера табличной модели для сервера Azure Analysis Services | Документация Майкрософт
description: Сведения о том, как добавить пример модели в службах Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>Руководство. Добавление примера модели

В этом руководстве вы добавите пример модели Adventure Works на свой сервер. Образец модели представляет собой готовую версию руководства по моделированию данных "Интернет-продажи Adventure Works" (1200). Образец модели полезен для тестирования управления моделью, соединения со средствами и клиентскими приложениями, а также для выполнения запросов к данным модели.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этим учебником необходимы указанные ниже компоненты.

- Сервер Azure Analysis Services
- Разрешения администратора сервера

## <a name="sign-in-to-the-azure-portal"></a>Выполните вход на портал Azure.

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>Создание образца модели

1. В области **Обзор** сервера выберите **Новая модель**.

    ![Создание образца модели](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. В разделе **Новая модель** > **Choose a datasource** (Выбор источника данных) установите флажок **Пример данных**, а затем нажмите кнопку **Добавить**.

    ![Выбор примеров данных](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Проверьте, создан ли пример `adventureworks` в области **Обзор**.

    ![Выбор примеров данных](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Образец модели использует ресурсы кэш-памяти. Если образец модели не используется для тестирования, следует удалить его с сервера.

> [!NOTE]
> В следующих шагах описано, как удалить модель с сервера с помощью среды SSMS. Также модель можно удалить с помощью предварительной версии компонента "Конструктор веб-страниц".

1. В среде SSMS выберите **Обозреватель объектов** и щелкните **Подключиться** > **Analysis Services**.

2. В поле **Подключение к серверу** вставьте имя сервера, затем в поле **Проверка подлинности** выберите **Active Directory — универсальная с поддержкой многофакторной проверки подлинности**, введите имя пользователя и нажмите кнопку **Подключиться**.

    ![Вход](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. В **обозревателе объектов** щелкните правой кнопкой мыши пример базы данных `adventureworks`, затем выберите **Удалить**.

    ![Удаление примера базы данных](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Дополнительная информация 

[Подключение в Power BI Desktop](analysis-services-connect-pbi.md)   
[Управление ролями и пользователями базы данных](analysis-services-database-users.md)



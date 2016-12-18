---
title: "Azure Active Directory B2C: создание клиента Azure Active Directory B2C | Документация Майкрософт"
description: "Инструкции по созданию клиента Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 36268d0153f4f61d6e6132f4e6a878b11cf7f6c2


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C: создание клиента Azure AD B2C
Чтобы начать работу с Microsoft Azure Active Directory (Azure AD) B2C, выполните три шага, описанные в этой статье.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Шаг 1. Оформление подписки Azure
Если у вас уже есть подписка Azure, этот шаг можно пропустить. Если у вас ее нет, зарегистрируйтесь для оформления [подписки Azure](../active-directory/sign-up-organization.md) и получения доступа к Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Шаг 2. Создание клиента Azure AD B2C
Чтобы создать клиент Azure AD B2C, выполните указанные ниже действия. Сейчас функции B2C нельзя включить в существующих клиентах.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com/) под именем администратора подписки. Это та же рабочая или учебная учетная запись либо учетная запись Майкрософт, которая использовалась для регистрации в Azure.
2. Щелкните **Создать** > **Службы приложений** > **Active Directory** > **Каталог** > **Настраиваемое создание**.
   
    ![Снимок экрана: начало создания клиента](./media/active-directory-b2c-get-started/new-directory.png)
3. Выберите **Имя**, **Доменное имя** и **Страну или регион** для вашего клиента.
4. Установите флажок **Это каталог B2C**.
5. Щелкните флажок, чтобы завершить действие.
   
    ![Снимок экрана: флажок создания каталога B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. Ваш клиент создан и должен отобразиться в расширении Active Directory. Кроме того, теперь вы — глобальный администратор клиента. При необходимости можно добавить других глобальных администраторов.
   
   > [!IMPORTANT]
   > Если вы планируете использовать B2C клиент для рабочего приложения, см. статью, в которой [сравниваются рабочая версия и предварительная версия клиента B2C](active-directory-b2c-reference-tenant-type.md). Обратите внимание, что при удалении существующего клиента B2C и его повторном создании с тем же доменным именем могут возникнуть известные проблемы. Создавайте клиент B2C с другим доменным именем.
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Шаг 3. Переход к колонке функций B2C на портале Azure
1. Перейдите к расширению Active Directory в области навигации слева.
2. Найдите на вкладке **Каталог** свой клиент и щелкните его.
3. Выберите вкладку **Настройка** .
4. В разделе **Администрирование B2C** щелкните ссылку **Управление настройками B2C**.
   
    ![Снимок экрана: настройка каталога для B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. В новом окне или на новой вкладке браузера откроется портал Azure с колонкой функций B2C.
   
   > [!IMPORTANT]
   > На то, чтобы ваш клиент стал доступен на портале Azure, может уйти 2–3 минуты. Если повторить эти шаги через некоторое время, проблема будет устранена. Если это не поможет, обратитесь в службу поддержки.
   > 
   > 
6. Закрепите эту колонку на начальной панели для удобного доступа. (Средство закрепления обозначено красным цветом в правом верхнем углу колонки функции.)
   
    ![Снимок экрана: колонка функций B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > Управлять пользователями, группами, настройками самостоятельного сброса пароля и фирменной символикой для своего клиента можно на [классическом портале Azure](https://manage.windowsazure.com/).
   > 
   > 

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Быстрый доступ к колонке функций B2C на портале Azure
Мы добавили ярлык колонки функций B2C на портале Azure, чтобы вы могли быстро ее найти.

1. Войдите на портал Azure как глобальный администратор клиента B2C. Если вы уже вошли в другой клиент, вы можете переключаться между клиентами в правом верхнем углу.
2. Нажмите кнопку **Обзор** в области навигации слева.
3. Для доступа к колонке с функциями B2C щелкните ярлык **Azure AD B2C** .
   
    ![Снимок экрана: переход к колонке функций B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Дальнейшие действия
Сведения о том, как зарегистрировать приложение в Azure AD B2C и быстро создать простое приложение, см. в статье [Azure Active Directory B2C: регистрация приложения](active-directory-b2c-app-registration.md).




<!--HONumber=Nov16_HO3-->



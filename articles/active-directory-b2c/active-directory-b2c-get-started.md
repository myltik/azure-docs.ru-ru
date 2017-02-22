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
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 351149296a6d7dfa801b295ec21fc04215c7b051
ms.openlocfilehash: 77b8e707b5743ce2d0b3eb52f75828c9ab0337d9


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

## <a name="step-4-link-your-azure-ad-b2c-tenant-to-your-azure-subscription"></a>Шаг 4. Привязка клиента Azure AD B2C к подписке Azure
Если вы планируете использовать клиент B2C для рабочих приложений, то необходимо связать клиент Azure AD B2C с подпиской Azure, чтобы взималась плата за использование. Чтобы узнать, как это сделать, ознакомьтесь с [этой статьей](active-directory-b2c-how-to-enable-billing.md).

   > [!IMPORTANT]
   > Если не связать клиент Azure AD B2C с подпиской Azure, то в колонке функций B2C на портале Azure отобразится предупреждение "No Subscription linked to this B2C tenant or the Subscription needs your attention" (С этим клиентом B2C не связана ни одна подписка, или подписка требует вашего внимания). Важно выполнить этот шаг, прежде чем переводить приложения в рабочую среду.
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




<!--HONumber=Jan17_HO4-->



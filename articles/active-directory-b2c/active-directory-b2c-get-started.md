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
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 02bb7286f6cd42f86e6cc920742a86d57ed93b60
ms.lasthandoff: 03/09/2017


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C: создание клиента Azure AD B2C
Чтобы начать работу с Microsoft Azure Active Directory (Azure AD) B2C, выполните четыре шага, описанные в этой статье.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Шаг 1. Оформление подписки Azure
Если у вас уже есть подписка Azure, этот шаг можно пропустить. Если у вас ее нет, зарегистрируйтесь для оформления [подписки Azure](../active-directory/sign-up-organization.md) и получения доступа к Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Шаг 2. Создание клиента Azure AD B2C
Чтобы создать клиент Azure AD B2C, выполните указанные ниже действия. Сейчас функции B2C нельзя включить в существующих клиентах.

1. Войдите на [портал Azure](https://portal.azure.com/) под именем администратора подписки. Это та же рабочая или учебная учетная запись либо учетная запись Майкрософт, которая использовалась для регистрации в Azure.
2. Нажмите кнопку **Создать** (или кнопку "+") и в поле **Поиск по Marketplace** введите "Azure Active Directory B2C".
   
    ![Снимок экрана: поиск по запросу "Azure AD B2C"](./media/active-directory-b2c-get-started/find-azure-ad-b2c.png)
3. В результатах щелкните **Azure Active Directory B2C**.

    ![Снимок экрана: результат для "Azure Active Directory B2C"](./media/active-directory-b2c-get-started/find-azure-ad-b2c-result.png)
4. На экране появится страница со сведениями о B2C.  Нажмите внизу экрана кнопку **Создать**, чтобы начать настройку нового клиента Azure Active Directory B2C.
5. Щелкните **Создать новый клиент Azure AD B2C**.
6. Выберите **имя организации, имя домена, страну или регион** для клиента.

    ![Снимок экрана: форма для создания нового клиента](./media/active-directory-b2c-get-started/create-new-b2c-tenant.png)
7. Нажмите кнопку **Создать**, чтобы создать клиент.  Это может занять несколько минут. Когда клиент будет создан, вы получите уведомление.

8. Ваш клиент создан и должен отобразиться в расширении Active Directory. Кроме того, теперь вы — глобальный администратор клиента. При необходимости можно добавить других глобальных администраторов.
   
   > [!IMPORTANT]
   > Если вы планируете использовать B2C клиент для рабочего приложения, см. статью, в которой [сравниваются рабочая версия и предварительная версия клиента B2C](active-directory-b2c-reference-tenant-type.md). Обратите внимание, что при удалении существующего клиента B2C и его повторном создании с тем же доменным именем могут возникнуть известные проблемы. Создавайте клиент B2C с другим доменным именем.
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Шаг 3. Переход к колонке функций B2C на портале Azure
1. Разверните раздел **Другие службы** под панелью навигации слева.
2. В поле поиска введите **Azure AD B2C** и щелкните результат (вы можете добавить Azure AD B2C в избранное для быстрого доступа в будущем).

    ![Снимок экрана: поиск Azure AD B2C на панели навигации](./media/active-directory-b2c-get-started/navigate-to-azure-ad-b2c.png)

3. В новом окне или на новой вкладке браузера откроется портал Azure с колонкой функций B2C.
   
4. Закрепите эту колонку на начальной панели для удобного доступа. (Кнопка "Закрепить" находится в правом верхнем углу колонки функций.)
   
    ![Снимок экрана: колонка функций B2C и кнопка "Закрепить"](./media/active-directory-b2c-get-started/b2c-pin-tenant.png)

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



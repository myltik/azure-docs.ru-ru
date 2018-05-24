---
title: Руководство по настройке xMatters OnDemand для автоматической подготовки пользователей с помощью Azure Active Directory | Документы Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отзыва учетных записей пользователей в xMatters OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 0f1cadfb7c78c32071083d5e2efa3299ba08fb3a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32161387"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Руководство по настройке xMatters OnDemand для автоматической подготовки пользователей

В этом руководстве показаны шаги, которые необходимо выполнить в xMatters OnDemand и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отзыва пользователей и групп в xMatters OnDemand.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>предварительным требованиям

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

*   клиент Azure AD;
*   клиент xMatters OnDemand с включенным планом [Starter](https://www.xmatters.com/pricing) или планом более высокого уровня; 
*   учетная запись пользователя в xMatters OnDemand с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки в Azure AD зависит от [API xMatters OnDemand](https://help.xmatters.com/xmAPI), доступного для команд xMatters OnDemand, использующих план Starter или более высокого уровня.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Добавление xMatters OnDemand из коллекции

Перед настройкой xMatters OnDemand для автоматической подготовки пользователей в Azure AD необходимо добавить xMatters OnDemand из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить xMatters OnDemand из коллекции приложений Azure AD, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]
    
3. Чтобы добавить xMatters OnDemand, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **xMatters OnDemand**.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. В области результатов выберите **xMatters OnDemand** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Назначение пользователей в xMatters OnDemand

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к xMatters OnDemand. Когда этот вопрос будет решен, этих пользователи и группы можно будет назначить приложению xMatters OnDemand, следуя приведенным ниже указаниям.

*   [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Важные рекомендации по назначению пользователей в xMatters OnDemand

*   Рекомендуется назначить одного пользователя Azure AD в xMatters OnDemand для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя в xMatters OnDemand в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (если доступно). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Настройка автоматической подготовки пользователей в xMatters OnDemand 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей или групп в xMatters OnDemand на основе назначений в Azure AD.

> [!TIP]
> Для xMatters OnDemand также можно включить единый вход на основе SAML. Для этого следуйте инструкциям, приведенным в [руководстве по единому входу в xMatters OnDemand](active-directory-saas-xmatters-ondemand-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в xMatters OnDemand, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. Выберите xMatters OnDemand из списка приложений SaaS.
 
    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Выберите вкладку **Подготовка**.
    
    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **Учетные данные администратора** введите **учетные данные**, **пароль администратора** и **домен** своей учетной записи xMatters OnDemand.

    *   В поле **Имя администратора** укажите имя учетной записи администратора в клиенте xMatters OnDemand. Пример: admin@contoso.com.

    *   В поле **Пароль администратора** введите пароль, соответствующий имени пользователя администратора.

    *   В поле **Домен** введите поддомен вашего клиента xMatters OnDemand.
    Пример: для учетной записи клиента с URL-адресом https://my-tenant.xmatters.com поддомен будет иметь значение **my-tenant**.

6. После заполнения поля, указанного в шаге 5, нажмите кнопку **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к xMatters OnDemand. Если установить подключение не удалось, убедитесь, что у учетной записи xMatters OnDemand есть разрешения администратора и попробуйте еще раз.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. В поле **Уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Выберите команду **Сохранить**.

9. В разделе **Сопоставления** выберите **Synchronize Azure Active Directory Users to xMatters OnDemand** (Синхронизировать пользователей Azure Active Directory с xMatters OnDemand).

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. В разделе **Сопоставление атрибутов** просмотрите атрибуты пользователей, которые синхронизированы из Azure AD в xMatters OnDemand. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в xMatters OnDemand для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. В разделе **Сопоставления** выберите **Synchronize Azure Active Directory Groups to xMatters OnDemand** (Синхронизировать группы Azure Active Directory с xMatters OnDemand).

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. В разделе **Сопоставление атрибутов** просмотрите атрибуты групп, которые синхронизированы из Azure AD в xMatters OnDemand. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления групп в xMatters OnDemand для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./active-directory-saas-scoping-filters.md).

14. Чтобы включить службу подготовки Azure AD для xMatters OnDemand, в разделе **Параметры** измените значение параметра **Состояние подготовки** на **Включено**.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Определите пользователей или группы, которые вы хотите подготовить в xMatters OnDemand, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые с приложением xMatters OnDemand службой подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png

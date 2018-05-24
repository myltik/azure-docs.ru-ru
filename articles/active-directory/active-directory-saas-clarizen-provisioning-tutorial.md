---
title: Руководство по настройке Clarizen для автоматической подготовки пользователей с помощью Azure Active Directory | Документы Microsoft
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Clarizen.
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
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 779fa09781b5ea6e13450d609703bec0c08e38e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Руководство по настройке Clarizen для автоматической подготовки пользователей

В этом руководстве описаны шаги, которые нужно выполнить в Clarizen и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отзыва пользователей и групп в Clarizen.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>предварительным требованиям

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

*   клиент Azure AD;
*   клиент Clarizen с планом [Enterprise Edition](https://www.clarizen.com/product/pricing/) или выше; 
*   учетная запись пользователя в Clarizen с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [API Clarizen](https://api.clarizen.com/v2.0/services/), доступного для групп Clarizen, использующих план Enterprise Edition или выше.

## <a name="adding-clarizen-from-the-gallery"></a>Добавление Clarizen из коллекции.
Перед настройкой Clarizen для автоматической подготовки пользователей в Azure AD необходимо добавить Clarizen из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Clarizen из коллекции приложений Azure AD, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]
    
3. Чтобы добавить Clarizen, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Clarizen**.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. В области результатов выберите **Clarizen** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Назначение пользователей в Clarizen

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD. 

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к Clarizen. Когда этот вопрос будет решен, этих пользователей и (или) группы можно будет назначить приложению Clarizen, следуя приведенным ниже указаниям:

*   [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Важные рекомендации по назначению пользователей в Clarizen

*   Рекомендуется назначить одного пользователя Azure AD в Clarizen для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя в Clarizen в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (если доступно). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Настройка автоматической подготовки пользователей в Clarizen 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Clarizen на основе их назначений в Azure AD.

> [!TIP]
> Для Clarizen также можно включить единый вход на основе SAML. Для этого следуйте инструкциям, приведенным в [руководстве по единому входу в Clarizen](active-directory-saas-clarizen-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в Clarizen, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. В списке приложений SaaS выберите Clarizen.
 
    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Выберите вкладку **Подготовка**.
    
    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **Учетные данные администратора** введите **домен**, **имя пользователя-администратора** и **пароль администратора** своей учетной записи Clarizen. Примеры этих значений:

    *   В поле **Имя администратора** укажите имя учетной записи администратора в клиенте Clarizen. Пример: admin@contoso.com.

    *   В поле **Пароль администратора** введите пароль учетной записи, соответствующей имени пользователя-администратора.

    *   В поле **Домен** укажите поддомен на основе шага 6.
    
6. Получите значение **serverLocation** для вашей учетной записи Clarizen на основе действий, упомянутых в разделе **Проверка подлинности** [руководства по API Rest в Clarizen](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2). После получения serverLocation укажите поддомен URL-адреса, выделенный ниже, в поле **Домен**.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. После заполнения поля, указанного в шаге 5, нажмите кнопку **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Clarizen. Если установить подключение не удалось, убедитесь, что у учетной записи Clarizen есть разрешения администратора, и повторите попытку.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. Выберите команду **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Clarizen**.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Clarizen. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в Clarizen для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Clarizen**.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. В разделе **Сопоставление атрибутов** просмотрите атрибуты группы, которые синхронизированы из Azure AD в Clarizen. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления групп в Clarizen для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./active-directory-saas-scoping-filters.md).

15. Чтобы включить службу подготовки Azure AD для Clarizen, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Определите пользователей или группы для подготовки в Clarizen, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые с приложением Clarizen службой подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png

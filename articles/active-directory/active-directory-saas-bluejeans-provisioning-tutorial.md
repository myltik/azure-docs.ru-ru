---
title: Руководство по настройке BlueJeans для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отзыва учетных записей пользователей в BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.openlocfilehash: 9b071d166f741df523d6343ba771b7a29cd4a303
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32139667"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Руководство по настройке BlueJeans для автоматической подготовки пользователей

В этом руководстве описаны действия, которые необходимо выполнить в BlueJeans и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отзыва пользователей и групп в BlueJeans.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>предварительным требованиям

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

*   клиент Azure AD;
*   включенный клиент BlueJeans с планом [My Company](https://www.BlueJeans.com/pricing) или более высокого уровня;
*   учетная запись пользователя в BlueJeans с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки в Azure AD зависит от [API BlueJeans](https://BlueJeans.github.io/developer), доступного для команд BlueJeans, использующих план "Стандартный" или более высокого уровня.

## <a name="adding-bluejeans-from-the-gallery"></a>Добавление BlueJeans из коллекции
Перед настройкой BlueJeans для автоматической подготовки пользователей в Azure AD необходимо добавить BlueJeans из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить BlueJeans из коллекции приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]
    
3. Чтобы добавить BlueJeans, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **BlueJeans**.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. В области результатов выберите **BlueJeans** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Назначение пользователей в BlueJeans

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к BlueJeans. Когда этот вопрос будет решен, этих пользователей и (или) группы можно будет назначить приложению BlueJeans, следуя приведенным ниже указаниям:

*   [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Важные рекомендации по назначению пользователей в BlueJeans

*   Рекомендуем назначить одного пользователя Azure AD в BlueJeans для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя в BlueJeans в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (при доступности). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Настройка автоматической подготовки пользователей в BlueJeans

Из этого раздела вы узнаете, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей или групп в BlueJeans на основе назначений в Azure AD.

> [!TIP]
> Для BlueJeans также можно включить единый вход на основе SAML. Для этого следуйте инструкциям в [руководстве по единому входу в BlueJeans](active-directory-saas-bluejeans-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в BlueJeans:

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. В списке приложений SaaS выберите BlueJeans.
 
    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans1.png)

5. В разделе **Учетные данные администратора** введите **учетные данные** и **пароль** администратора своей учетной записи BlueJeans. Примеры этих значений:

    *   В поле **Имя администратора** укажите имя учетной записи администратора в клиенте BlueJeans. Пример: admin@contoso.com.

    *   В поле **Пароль администратора** введите пароль, соответствующий имени пользователя администратора.

6. После заполнения поля, указанного в шаге 5, щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к BlueJeans. Если установить подключение не удалось, убедитесь, что учетной записи BlueJeans назначены разрешения администратора, и повторите попытку.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Выберите команду **Сохранить**.

9. В разделе **Сопоставления** выберите **Synchronize Azure Active Directory Users to BlueJeans** (Синхронизировать пользователей Azure Active Directory с BlueJeans).

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в BlueJeans. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в BlueJeans для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./active-directory-saas-scoping-filters.md).

12. Чтобы включить службу подготовки Azure AD для BlueJeans, в разделе **Параметры** измените значение параметра **Состояние подготовки** на **Включено**.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Определите пользователей или группы для подготовки в BlueJeans, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/SaveProvisioning.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые с приложением BlueJeans службой подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](./active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* В Bluejeans нельзя использовать имена пользователей длиннее 30 знаков.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png

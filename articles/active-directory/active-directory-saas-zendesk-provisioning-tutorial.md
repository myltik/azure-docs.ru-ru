---
title: Руководство по настройке автоматической подготовки пользователей с помощью Azure Active Directory для приложения Zendesk | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отзыва учетных записей пользователей в Zendesk.
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
ms.author: v-ant
ms.openlocfilehash: 87ed987b53b45ef9dacb771d8dd057432712fb16
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354121"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Руководство по настройке Zendesk для автоматической подготовки пользователей

В этом руководстве описаны шаги, которые нужно выполнить в Zendesk и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отзыва пользователей и групп в Zendesk. 

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>предварительным требованиям

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

*   клиент Azure AD;
*   клиент Zendesk с [планом Enterprise](https://www.zendesk.com/product/pricing/) или выше; 
*   учетная запись пользователя в Zendesk с разрешениями администратора. 

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [Rest API Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), доступного для групп Zendesk, использующих план Essential или более высокий.

## <a name="adding-zendesk-from-the-gallery"></a>Добавление Zendesk из коллекции
Перед настройкой Zendesk для автоматической подготовки пользователей в Azure AD необходимо добавить Zendesk из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Zendesk из коллекции приложений Azure AD, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]
    
3. Чтобы добавить Zendesk, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Zendesk**.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk6.png)

5. В области результатов выберите **Zendesk** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Назначение пользователей в Zendesk

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD. 

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к Zendesk. Когда этот вопрос будет решен, этих пользователей и (или) группы можно будет назначить приложению Zendesk, следуя инструкциям:

*   [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Важные советы по назначению пользователей в Zendesk

*   Рекомендуется назначить одного пользователя Azure AD в Zendesk для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя в Zendesk в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (если доступно). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Настройка автоматической подготовки пользователей в Zendesk 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Zendesk на основе их назначений в Azure AD.

> [!TIP]
> Для Zendesk также можно включить единый вход на основе SAML. Для этого следуйте инструкциям, приведенным в [руководстве по единому входу в Zendesk](active-directory-saas-zendesk-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в Zendesk, сделайте следующее.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. В списке приложений SaaS выберите Zendesk.
 
    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk3.png)

3. Выберите вкладку **Подготовка**.
    
    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk16.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. В разделе **Учетные данные администратора** введите **имя администратора**, **маркер секрета** и **домен** своей учетной записи Zendesk. Примеры этих значений:

    *   В поле **Имя администратора** укажите имя учетной записи администратора в клиенте Zendesk. Пример: admin@contoso.com.

    *   В поле **маркер секрета** введите маркер секрета, как описано в шаге 6.

    *   В поле **Домен** введите поддомен вашего клиента Zendesk.
    Пример: для учетной записи клиента с URL-адресом https://my-tenant.zendesk.com поддомен будет иметь значение **my-tenant**.

6. **Маркер секрета** для учетной записи Zendesk можно найти в разделе **Администрирование > API > Параметры**. 

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk4.png) ![Zendesk Provisioning](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

7. После заполнения полей, указанных на шаге 5, щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Zendesk. Если установить подключение не удалось, убедитесь, что у учетной записи ZenDesk есть разрешения администратора, и повторите попытку.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk9.png)

9. Выберите команду **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Zendesk**.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk10.png)

11. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Zendesk. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей Zendesk для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk11.png)

12. В разделе **Сопоставления** выберите **Синхронизировать группы Azure Active Directory с Zendesk**.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk12.png)

13. В разделе **Сопоставление атрибутов** просмотрите атрибуты группы, которые синхронизированы из Azure AD в Zendesk. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления групп в Zendesk для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk13.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./active-directory-saas-scoping-filters.md).

15. Чтобы включить службу подготовки Azure AD для Zendesk, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk14.png)

16. Определите пользователей или группы для подготовки в Zendesk, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk15.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk18.png)


После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые службой подготовки Azure AD с приложением Zendesk.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](./active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Ограничения соединителя
* Zendesk поддерживает использование групп пользователей только с ролями агента. Дополнительные сведения см. в [документации по Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png

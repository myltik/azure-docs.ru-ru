---
title: Руководство по интеграции Azure Active Directory с AwardSpring | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и AwardSpring.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f115be6-4fbe-42aa-9319-7462e7a75736
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: 591232aca57902d8f11bf3422c972021922bf204
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341854"
---
# <a name="tutorial-azure-active-directory-integration-with-awardspring"></a>Руководство по интеграции Azure Active Directory с AwardSpring

В этом руководстве описано, как интегрировать AwardSpring с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением AwardSpring обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к AwardSpring.
- Вы можете включить автоматический вход пользователей в AwardSpring (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с AwardSpring, вам потребуется:

- подписка Azure AD;
- подписка AwardSpring с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление AwardSpring из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-awardspring-from-the-gallery"></a>Добавление AwardSpring из коллекции
Чтобы настроить интеграцию AwardSpring с Azure AD, необходимо добавить AwardSpring из коллекции в список управляемых приложений SaaS.

**Чтобы добавить AwardSpring из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **AwardSpring**, выберите **AwardSpring** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![AwardSpring в списке результатов](./media/active-directory-saas-awardspring-tutorial/tutorial_awardspring_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в AwardSpring с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в AwardSpring соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в AwardSpring.

Чтобы настроить и проверить единый вход Azure AD в AwardSpring, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя AwardSpring](#create-an-awardspring-test-user)** нужно для того, чтобы в AwardSpring существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении AwardSpring.

**Чтобы настроить единый вход Azure AD в AwardSpring, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **AwardSpring** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-awardspring-tutorial/tutorial_awardspring_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения AwardSpring** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения AwardSpring](./media/active-directory-saas-awardspring-tutorial/tutorial_awardspring_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.awardspring.com/SignIn/SamlMetaData`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.awardspring.com/SignIn/SamlAcs`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения AwardSpring](./media/active-directory-saas-awardspring-tutorial/tutorial_awardspring_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.awardspring.com/`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов AwardSpring](mailto:support@awardspring.com). 

5. Приложение AwardSpring ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/active-directory-saas-awardSpring-tutorial/tutorial_awardSpring_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    | Имя атрибута | Значение атрибута |
    | ---------------| --------------- |    
    | Имя | user.givenname |
    | Фамилия | user.surname |
    | Email | user.mail |
    | Имя пользователя | user.userprinicipalname |
    | studentID | < ИД учащегося > |

    > [!NOTE]
    > Атрибут StudentID сопоставляется с фактическим идентификатором учащегося, который следует передать обратно в утверждениях. Чтобы получить это значение, обратитесь в [службу поддержки клиентов AwardSpring](mailto:support@awardspring.com).

    a. Удалите имеющиеся атрибуты и добавьте новые. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-awardspring-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-awardspring-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    d. Нажмите кнопку **ОК**.

7. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-awardspring-tutorial/tutorial_awardspring_certificate.png) 

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-awardspring-tutorial/tutorial_general_400.png)
    
9. Чтобы настроить единый вход на стороне **AwardSpring**, отправьте скачанный **XML-файл метаданных** в [службу поддержки AwardSpring](mailto:support@awardspring.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-awardspring-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-awardspring-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-awardspring-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-awardspring-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-awardspring-test-user"></a>Создание тестового пользователя AwardSpring

Цель этого раздела — создать пользователя с именем Britta Simon в AwardSpring. Приложение AwardSpring поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к AwardSpring (если он еще не создан).

>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки AwardSpring](maito:support@awardspring.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к AwardSpring.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению AwardSpring, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **AwardSpring**.

    ![Ссылка на AwardSpring в списке приложений](./media/active-directory-saas-awardspring-tutorial/tutorial_awardspring_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку AwardSpring на панели доступа, вы автоматически войдете в приложение AwardSpring.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-awardspring-tutorial/tutorial_general_203.png


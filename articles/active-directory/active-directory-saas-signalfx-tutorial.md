---
title: Руководство по интеграции Azure Active Directory с SignalFx | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 9db01b4ea9a4f0d307db8bb9f8b6d6437a06815d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Руководство по интеграции Azure Active Directory с SignalFx

В этом руководстве описано, как интегрировать SignalFx с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SignalFx обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к SignalFx.
- Вы можете включить автоматический вход пользователей в SignalFx (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SignalFx, вам потребуется:

- подписка Azure AD;
- отдельная подписка с поддержкой единого входа SignalFx.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SignalFx из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-signalfx-from-the-gallery"></a>Добавление SignalFx из коллекции.
Чтобы настроить интеграцию SignalFx с Azure AD, необходимо добавить SignalFx из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SignalFx из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SignalFx**, выберите **SignalFx** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SignalFx в списке результатов](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SignalFx с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в SignalFx соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SignalFx.

Чтобы настроить и проверить единый вход Azure AD в SignalFx, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения SignalFx](#create-a-signalfx-test-user)** требуется для того, чтобы в SignalFx существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SignalFx.

**Чтобы настроить единый вход Azure AD в SignalFx, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **SignalFx** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SignalFx** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://api.signalfx.com/v1/saml/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://api.signalfx.com/v1/saml/acs/<integration ID>`.

    > [!NOTE] 
    > Приведенное выше значение используется только для примера. Это значение следует заменить фактическим URL-адресом ответа, который описывается далее в этом руководстве.

4. Приложение SignalFx ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.   

    ![Настройка единого входа](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.
 
6. В разделе **Сертификат подписи SAML** сделайте следующее: 

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложения**, а затем вставьте его в Блокнот.

    Б. Щелкните **Сертификат (Base64)** и сохраните файл сертификата на компьютере.

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. В разделе **Настройка SignalFx** щелкните **Настроить SignalFx**, чтобы открыть окно **Настройка единого входа**. Скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) из раздела **Краткий справочник**.

    ![Конфигурация SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

9. Войдите на сайт компании SignalFx от имени администратора.

10. В верхней части сайта щелкните **Integrations** (Интеграция), чтобы открыть соответствующую страницу.

    ![Интеграция SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

11. Щелкните плитку **Azure Active Directory** в разделе **Login Services** (Службы входа).
 
    ![SAML SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

12. Щелкните **NEW INTEGRATION** (Новая интеграция) и на вкладке **INSTALL** (Установка) выполните следующие действия:
 
    ![Страница samlintgpage SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. В текстовом поле **Name** (Имя) введите имя новой интеграции, например **OurOrgName SAML SSO**.

    Б. Скопируйте значение **Integration ID** (Идентификатор интеграции) и присоедините к нему **Reply URL** (URL-адрес ответа), например `https://api.signalfx.com/v1/saml/acs/<integration ID>`, в текстовом поле **URL-адрес ответа** раздела **Домены и URL-адреса приложения SignalFx** на портале Azure.

    c. Щелкните **Upload File** (Отправить файл), чтобы отправить **сертификат в кодировке Base64**, скачанный с портала Azure, в поле **Certificate** (Сертификат).

    d. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    д. В текстовое поле **URL-адрес метаданных** вставьте **URL-адрес метаданных федерации приложения**, скопированный на портале Azure.

    f. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-signalfx-test-user"></a>Создание тестового пользователя SignalFx

Цель этого раздела — создать пользователя с именем Britta Simon в SignalFx. Приложение SignalFx поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению SignalFx (если он еще не создан).

При первом входе пользователя в SignalFx с использованием единого входа SAML [служба поддержки SignalFx](mailto:kmazzola@signalfx.com) отправит ему по электронной почте ссылку, которую необходимо открыть для проверки подлинности. Это происходит только при первом входе пользователя. При последующих попытках входа проверка по электронной почте не требуется.

>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки SignalFx](mailto:kmazzola@signalfx.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SignalFx.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SignalFx, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SignalFx**.

    ![Ссылка на SignalFx в списке приложений](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SignalFx на панели доступа, вы автоматически войдете в приложение SignalFx.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png


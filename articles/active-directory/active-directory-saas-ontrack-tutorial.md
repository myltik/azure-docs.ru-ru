---
title: Руководство по интеграции Azure Active Directory с приложением OnTrack | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и OnTrack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 76a7bd34c3fddc70e025af52235be619f04fa0d9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Руководство. Интеграция Azure Active Directory с OnTrack

В этом руководстве описано, как интегрировать OnTrack с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением OnTrack обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к OnTrack.
- Вы можете включить автоматический вход пользователей в OnTrack (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с OnTrack, вам потребуется следующее:

- подписка Azure AD;
- подписка OnTrack с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление OnTrack из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ontrack-from-the-gallery"></a>Добавление OnTrack из коллекции
Чтобы настроить интеграцию OnTrack с Azure AD, необходимо добавить OnTrack из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OnTrack из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **OnTrack**, выберите **OnTrack** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![OnTrack в списке результатов](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение OnTrack с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в OnTrack соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OnTrack.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в OnTrack.

Чтобы настроить и проверить единый вход Azure AD в OnTrack, выполните следующие стандартные действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения OnTrack](#create-an-ontrack-test-user)** требуется для создания в OnTrack пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении OnTrack.

**Чтобы настроить единый вход Azure AD в OnTrack, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **OnTrack** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_samlbase.png)

3. В разделе **Домены и URL-адреса приложения OnTrack** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения OnTrack](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_url.png)

    a. В текстовое поле **Идентификатор**:
    
    Для тестовой среды введите URL-адрес: `https://staging.insigniagroup.com/sso`

    Для рабочей среды введите URL-адрес: `https://oeaccessories.com/sso`

    Б. В текстовое поле **URL-адрес ответа**:
    
    Для тестовой среды введите URL-адрес: `https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    Для рабочей среды введите URL-адрес: `https://igaccessories.com/sso/autonation.aspx`

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_certificate.png)

5. Приложение OnTrack ожидает проверочные утверждения SAML в определенном формате, поэтому следует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. 

    ![Настройка единого входа](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | -------------- | ----------------|    
    | User-Role      | 42F432 |
    | Hyperion-Code  | 12345 |

    > [!NOTE]
    > Атрибуты **User-Role** и **Hyperion-Code** сопоставляются с ролью пользователя Autonation и кодом продавца соответственно. Эти значения приводятся в качестве примера, используйте правильный код для своей интеграции. Чтобы получить эти значения, обратитесь в [службу поддержки Autonation](mailto:CustomerService@insigniagroup.com).
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_04.png)   

    ![Настройка единого входа](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-ontrack-tutorial/tutorial_general_400.png)

8. Чтобы настроить единый вход на стороне **OnTrack**, отправьте в [службу поддержки OnTrack](mailto:CustomerService@insigniagroup.com) скачанный **XML-файл метаданных**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-ontrack-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-ontrack-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-ontrack-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-ontrack-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-ontrack-test-user"></a>Создание тестового пользователя OnTrack

В этом разделе описано, как создать пользователя Britta Simon в приложении OnTrack. Обратитесь в [службу поддержки OnTrack](mailto:CustomerService@insigniagroup.com), чтобы добавить пользователей на платформу OnTrack. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к OnTrack.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в OnTrack, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **OnTrack**.

    ![Ссылка на OnTrack в списке приложений](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку OnTrack на панели доступа, вы автоматически войдете в приложение OnTrack.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_203.png


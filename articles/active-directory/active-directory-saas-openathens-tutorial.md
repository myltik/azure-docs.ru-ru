---
title: "Руководство по интеграции Azure Active Directory с OpenAthens | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и OpenAthens."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: bbf74768542983be2e69ffb948baf35822610839
ms.contentlocale: ru-ru
ms.lasthandoff: 09/13/2017

---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Руководство по интеграции Azure Active Directory с OpenAthens

В этом руководстве описано, как интегрировать OpenAthens с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением OpenAthens обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к OpenAthens.
- Вы можете включить автоматический вход пользователей в OpenAthens (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с OpenAthens, вам потребуется:

- подписка Azure AD;
- подписка OpenAthens с поддержкой единого входа.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление OpenAthens из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-openathens-from-the-gallery"></a>Добавление OpenAthens из коллекции
Чтобы настроить интеграцию OpenAthens с Azure AD, необходимо добавить OpenAthens из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OpenAthens из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **OpenAthens**, выберите **OpenAthens** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![OpenAthens в списке результатов](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в OpenAthens с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в OpenAthens соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OpenAthens.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в OpenAthens.

Чтобы настроить и проверить единый вход Azure AD в OpenAthens, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя OpenAthens](#create-a-openathens-test-user)** требуется для того, чтобы в OpenAthens существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении OpenAthens.

**Чтобы настроить единый вход Azure AD в OpenAthens, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **OpenAthens** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. В разделе **Домены и URL-адреса приложения OpenAthens** введите значение `https://login.openathens.net/saml/2/metadata-sp` в текстовом поле **Идентификатор**.

    ![Сведения о домене и URL-адресах единого входа для приложения OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на свой корпоративный сайт OpenAthens в качестве администратора.

7. Выберите **Connections** (Подключения) на вкладке **Management** (Управление). 

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Выберите **SAML 1.1/2.0** (SAML 1.1 и 2.0) и нажмите кнопку **Configure** (Настроить).

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9.  Передайте XML-файл метаданных, скачанный с портала Azure, нажав кнопку **Browse** (Обзор). Нажмите кнопку **Add** (Добавить), чтобы добавить конфигурацию.

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Выполните следующие действия на вкладке **Details** (Сведения).

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    а. Из списка **Display name mapping** (Сопоставление отображаемого имени) выберите **Use Attribute** (Использовать атрибут).

    b. В текстовом поле **Display name attribute** (Атрибут отображаемого имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Из списка **Unique user mapping** (Сопоставление уникального пользователя) выберите **Use Attribute** (Использовать атрибут).

    d. В текстовом поле **Unique user attribute** (Атрибут уникального пользователя имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    д. В разделе **Status** (Состояние) установите все три флажка.

    f. Из списка **Create local accounts** (Создание локальных учетных записей) выберите **automatically** (Автоматически).

    ж. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    а. В поле **Имя** введите **BrittaSimon**.

    b. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    г) Щелкните **Создать**.
  
### <a name="create-a-openathens-test-user"></a>Создание тестового пользователя OpenAthens

OpenAthens поддерживает JIT-подготовку, и пользователи создаются автоматически после успешной аутентификации. Не нужно выполнять никаких действий в этом разделе.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к OpenAthens.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в OpenAthens, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **OpenAthens**.

    ![Ссылка на OpenAthens в списке приложений](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "OpenAthens" на панели доступа, вы автоматически войдете в приложение OpenAthens.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png



---
title: Руководство по интеграции Azure Active Directory с LearnUpon | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 2796f280b2f95504be5f9691098bff07fdd93393
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Руководство. Интеграция Azure Active Directory с LearnUpon

В этом руководстве описано, как интегрировать LearnUpon с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением LearnUpon обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LearnUpon.
- Вы можете включить автоматический вход пользователей в LearnUpon (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с LearnUpon, вам потребуется:

- подписка Azure AD;
- подписка LearnUpon с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление LearnUpon из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-learnupon-from-the-gallery"></a>Добавление LearnUpon из коллекции
Чтобы настроить интеграцию LearnUpon с Azure AD, необходимо добавить LearnUpon из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LearnUpon из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **LearnUpon**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. На панели результатов выберите **LearnUpon** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в LearnUpon для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в LearnUpon соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LearnUpon.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LearnUpon.

Чтобы настроить и проверить единый вход Azure AD в LearnUpon, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LearnUpon](#creating-a-learnupon-test-user)** требуется для создания в LearnUpon пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LearnUpon.

**Чтобы настроить единый вход Azure AD в LearnUpon, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **LearnUpon** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. В разделе **Домены и URL-адреса LearnUpon** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.learnupon.com/saml/consumer`.

    > [!NOTE] 
    > Обратите внимание, что это значение используется только в качестве примера. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь в [службу поддержки LearnUpon](https://www.learnupon.com/features/support/).



4. В разделе **Сертификат подписи SAML** найдите параметр **Отпечаток**. Он будет добавлен в параметры SAML LearnUpon.

    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. В разделе **Настройка LearnUpon** щелкните **Настроить LearnUpon**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Откройте другую страницу браузера и войдите в LearnUpon с учетной записью администратора. 

8. Перейдите на вкладку **Settings** (Параметры).
   
    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. Щелкните **Single Sign On - SAML** (Единый вход — SAML), а затем нажмите кнопку **General Settings** (Общие параметры) для настройки SAML.
   
    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. В разделе **General Settings** (Общие параметры) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Щелкните **Включено**.

    Б. Для параметра **Версия** установите значение **2.0**.

    c. Для параметра **Пропустить условия** установите значение **Нет**.

    d. В текстовое поле **SAML Token Post param name** (Имя параметра POST для токена SAML) введите имя параметра POST, передаваемого в запросе на указанный выше URL-адрес клиента SAML (в нем содержится требующее проверки и аутентификации утверждение SAML, например **SAMLResponse**).

    д. В текстовое поле **Name Identifier Format** (Формат идентификатора имени) введите значение, которое определяет позицию идентификатора пользователя (адрес электронной почты) в утверждении SAML (например, **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**).
  
    f. В текстовом поле **Расположение поставщика удостоверений** введите значение, которое определяет адрес, на который перенаправляются пользователи при щелчке по значку отправки на экране входа портала Azure.
  
    ж. В текстовом поле **URL-адрес выхода** вставьте значение **URL-адреса выхода**, скопированное на портале Azure.
    
    h. Щелкните **Manage finger prints**(Управление отпечатками) и отправьте отпечаток загруженного сертификата.

11. Перейдите в раздел **User Settings**(Параметры пользователя) и выполните следующее.
   
     ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. В текстовом поле **First Name Identifier Format** (Формат идентификатора имени) введите значение, которое обозначает расположение имени пользователя в утверждении SAML, например: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    Б. В текстовом поле **Last Name Identifier Format** (Формат идентификатора фамилии) введите значение, которое обозначает расположение фамилии пользователя в утверждении SAML, например: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-learnupon-test-user"></a>Создание тестового пользователя LearnUpon

Цель этого раздела — создать пользователя с именем Britta Simon в LearnUpon. Приложение LearnUpon поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению LearnUpon (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Если вам нужно создать пользователя вручную, обратитесь в [службу поддержки LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход для пользователя Britta Simon, предоставив этому пользователю доступ к LearnUpon.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LearnUpon, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **LearnUpon**.

    ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент LearnUpon на панели доступа, вы автоматически войдете в приложение LearnUpon.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png


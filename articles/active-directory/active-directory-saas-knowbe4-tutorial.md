---
title: "Руководство по интеграции Azure Active Directory с KnowBe4 Security Awareness Training | Документация Майкрософт"
description: "Узнайте, как настраивать единый вход между Azure Active Directory и KnowBe4 Security Awareness Training."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 3b18737112a8aef101fab7fac1904f7c2e194d64
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Руководство по интеграции Azure Active Directory с KnowBe4 Security Awareness Training

В этом руководстве вы узнаете, как интегрировать KnowBe4 Security Awareness Training с Azure Active Directory (Azure AD).

Интеграция KnowBe4 Security Awareness Training с Azure AD предоставляет следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к KnowBe4 Security Awareness Training.
- Вы можете позволить пользователям автоматически входить в KnowBe4 Security Awareness Training (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с KnowBe4 Security Awareness Training, вам потребуется:

- подписка Azure AD;
- подписка KnowBe4 Security Awareness Training с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление KnowBe4 Security Awareness Training из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Добавление KnowBe4 Security Awareness Training из коллекции
Для настройки интеграции KnowBe4 Security Awareness Training с Azure AD необходимо добавить KnowBe4 Security Awareness Training из коллекции в список управляемых приложений SaaS.

**Чтобы добавить KnowBe4 Security Awareness Training из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **KnowBe4 Security Awareness Training**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_search.png)

5. На панели результатов выберите **KnowBe4 Security Awareness Training** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в KnowBe4 Security Awareness Training с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь KnowBe4 Security Awareness Training соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в KnowBe4 Security Awareness Training.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в KnowBe4 Security Awareness Training.

Чтобы настроить и проверить единый вход Azure AD в KnowBe4 Security Awareness Training, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя KnowBe4 Security Awareness Training](#creating-a-knowbe4-security-awareness-training-test-user)** требуется для создания пользователя Britta Simon в KnowBe4 Security Awareness Training, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении KnowBe4 Security Awareness Training.

**Чтобы настроить единый вход Azure AD в KnowBe4 Security Awareness Training, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **KnowBe4 Security Awareness Training** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_samlbase.png)

3. В разделе **KnowBe4 Security Awareness Training** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес для входа. Обратитесь в [службу поддержки KnowBe4 Security Awareness Training Client](mailto:support@KnowBe4.com), чтобы получить значение. 
 

4. В разделе **Сертификат подписи SAML** щелкните **Certificate (Raw)** (Сертификат (необработанный)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_400.png)

6. В разделе **Настройка KnowBe4 Security Awareness Training** щелкните **Настроить KnowBe4 Security Awareness Training**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_configure.png) 

7. Чтобы настроить единый вход на стороне **KnowBe4 Security Awareness Training**, необходимо отправить скачанный **необработанный файл сертификата**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа** в [службу поддержки KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-knowbe4-security-awareness-training-test-user"></a>Создание тестового пользователя KnowBe4 Security Awareness Training

В этом разделе описано, как создать пользователя с именем Britta Simon в приложении KnowBe4 Security Awareness Training. Приложение KnowBe4 Security Awareness Training поддерживает JIT-подготовку, включенную по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь будет создан при попытке получить доступ к приложению KnowBe4 Security Awareness Training (если он еще не создан). 

>[!NOTE]
>Если необходимо создать пользователя вручную, свяжитесь со [службой поддержки KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к KnowBe4 Security Awareness Training, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в KnowBe4 Security Awareness Training, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **KnowBe4 Security Awareness Training**.

    ![Настройка единого входа](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
  
Щелкнув панель KnowBe4 Security Awareness Training на панели доступа, вы автоматически войдете в приложение KnowBe4 Security Awareness Training.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_203.png



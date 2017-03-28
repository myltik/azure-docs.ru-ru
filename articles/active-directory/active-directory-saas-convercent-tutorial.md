---
title: "Руководство. Интеграция Azure Active Directory с Convercent | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Convercent."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6bbfc7e22e5cb70ed1466bda14831412aead76a6
ms.openlocfilehash: bacabf7fe51e3c417a93a54f043c14aa5d65b31d
ms.lasthandoff: 01/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>Руководство. Интеграция Azure Active Directory с Convercent

В этом руководстве описано, как интегрировать Convercent с Azure Active Directory (Azure AD).

Интеграция Convercent с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Convercent.
- Вы можете включить автоматический вход пользователей в Convercent (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на новом портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Convercent, вам потребуется следующее:

- подписка Azure AD;
- подписка Convercent с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Convercent из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-convercent-from-the-gallery"></a>Добавление Convercent из коллекции
Чтобы настроить интеграцию Convercent с Azure AD, необходимо добавить Convercent из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Convercent из коллекции, выполните следующее.**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Convercent**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_001.png)

5. На панели результатов выберите **Convercent** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Convercent с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Convercent соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Convercent.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Convercent.

Чтобы настроить и проверить единый вход в Azure AD в Convercent, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Convercent](#creating-a-works-mobile-test-user)** требуется для создания в Convercent пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на новом портале Azure и настроить его в приложении Convercent.

**Чтобы настроить единый вход Azure AD в Convercent, выполните следующее.**

1. На новом портале Azure на странице интеграции с приложением **Convercent** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. На странице диалогового окна **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Convercent** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)

    а. В текстовом поле **Идентификатор** введите `https://sts.convercent.com/`.

    b. Щелкните **Показать дополнительные параметры URL-адресов**.

    c. В текстовом поле **Состояние ретрансляции** введите:`https://app.convercent.com/`
    
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то в разделе **Домены и URL-адреса приложения Convercent** выполните следующие действия:
    
    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png)

    а. В текстовом поле **URL-адрес входа** введите `https://app.convercent.com/`.

    > [!NOTE] 
    > Здесь мы рекомендуем использовать указанный уникальный идентификатор. Чтобы получить это значение, обратитесь в [службу поддержки Convercent](mailTo:support@convercent.com).

5. В разделе **Конфигурация Convercent** щелкните **Настроить Convercent**, чтобы открыть окно **Настройка единого входа**. Щелкните **Метаданные XML-элемента SAML**, а затем сохраните файл метаданных на вашем компьютере.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png)

6. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки Convercent](mailTo:support@convercent.com) и предоставьте скачанный **файл метаданных**.

7. На новом портале Azure щелкните кнопку **Сохранить**.  
  

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-convercent-test-user"></a>Создание тестового пользователя Convercent

В этом разделе описано, как создать пользователя Britta Simon в Convercent. Обратитесь к [группе поддержки](emailto:support@convercent.com) Convercent, чтобы добавить пользователей на платформу Convercent.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Convercent.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Convercent, выполните следующие действия.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Convercent**.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Convercent на панели доступа, вы автоматически войдете в приложение Convercent.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png

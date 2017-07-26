---
title: "Руководство по интеграции Azure Active Directory с Pluralsight | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Pluralsight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 62429643a108665544e42001d264046b5db1ec97
ms.contentlocale: ru-ru
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Учебник. Интеграция Azure Active Directory с Pluralsight

В этом руководстве описано, как интегрировать Pluralsight с Azure Active Directory (Azure AD).

Интеграция Pluralsight с Azure AD обеспечивает приведенные далее преимущества.

- С помощью Azure AD вы можете контролировать доступ к Pluralsight.
- Вы можете включить автоматический вход пользователей в Pluralsight (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Pluralsight, вам потребуется:

- подписка Azure AD;
- подписка Pluralsight с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Pluralsight из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-pluralsight-from-the-gallery"></a>Добавление Pluralsight из коллекции
Чтобы настроить интеграцию Pluralsight с Azure AD, необходимо добавить Pluralsight из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pluralsight из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Pluralsight**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_search.png)

5. На панели результатов выберите **Pluralsight** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Pluralsight с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Pluralsight соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pluralsight.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pluralsight.

Чтобы настроить и проверить единый вход Azure AD в Pluralsight, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pluralsight](#creating-a-pluralsight-test-user)** требуется для того, чтобы в Pluralsight существовал пользователь Britta Simon, связанный с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Pluralsight.

**Чтобы настроить единый вход Azure AD в Pluralsight, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Pluralsight** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Pluralsight** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<instance name>.pluralsight.com/sso/<company name>`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Pluralsight](mailto:support@pluralsight.com). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_certificate.png) 

5. В этом разделе описано, как включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложении Pluralsight.

    Для приложения Pluralsight проверочные утверждения SAML должны иметь определенный формат. Для этого необходимо добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов маркера SAML. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_attribute.png)

    >[!NOTE]
    >Также можно добавить атрибут **Уникальный идентификатор** с соответствующим значением, таким как EmployeeID или другим, которое подходит для вашей организации. Обратите внимание, что это необязательный атрибут, однако его можно добавить для идентификации уникального пользователя. 

6. Чтобы добавить требуемые **атрибуты маркера SAML**, для каждой строки в таблице ниже выполните следующие действия.
   
   | Имя атрибута | Значение атрибута |
   | ---| --- |
   | Имя |user.givenname |
   | Фамилия |user.surname |
   | Email |user.mail |
   
   а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.
    
     ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_addattribute.png)
  
   b. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
  
   c. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
  
   г) Нажмите кнопку **ОК**.    

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_400.png)

8. Чтобы настроить единый вход для своего приложения, свяжитесь с представителем отдела [профессиональных услуг](mailTo:professionalservices@pluralsight.com) Pluralsight и передайте ему скачанный файл метаданных.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-pluralsight-test-user"></a>Создание тестового пользователя Pluralsight

Цель этого раздела — создать пользователя с именем Britta Simon в Pluralsight. Чтобы добавить пользователей в учетную запись Pluralsight, обратитесь в [службу поддержки клиентов Pluralsight](mailto:support@pluralsight.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Pluralsight, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Pluralsight, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Pluralsight**.

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Pluralsight на панели доступа, вы автоматически войдете в приложение Pluralsight. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png



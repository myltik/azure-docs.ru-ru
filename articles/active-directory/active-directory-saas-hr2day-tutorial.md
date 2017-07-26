---
title: "Учебник. Интеграция Azure Active Directory с HR2day by Merces | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и HR2day от Merces."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 456ef3620c1d7bb6ab2bf09b094f977e46cf2ed6
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Руководство. Интеграция Azure Active Directory с HR2day от Merces

В этом руководстве описано, как интегрировать приложение HR2day от Merces с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением HR2day от Merces обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к HR2day от Merces.
- Вы можете включить автоматический вход пользователей в HR2day от Merces (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с HR2day от Merces, вам потребуется:

- подписка Azure AD;
- подписка HR2day от Merces с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление HR2day от Merces из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Добавление HR2day от Merces из коллекции.
Чтобы настроить интеграцию HR2day от Merces с Azure AD, необходимо добавить HR2day от Merces из коллекции в список управляемых приложений SaaS.

**Чтобы добавить HR2day от Merces из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **HR2day от Merces**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. На панели результатов выберите **HR2day от Merces** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в HR2day от Merces с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в HR2day от Merces соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в HR2day от Merces.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в HR2day от Merces.

Чтобы настроить и проверить единый вход Azure AD в HR2day от Merces, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя HR2day от Merces](#creating-an-hr2day-by-merces-test-user)** требуется для создания пользователя Britta Simon в HR2day от Merces, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении HR2day от Merces.

**Чтобы настроить единый вход Azure AD в HR2day от Merces, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **HR2day от Merces** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. В разделе **Домены и URL-адреса приложения HR2day от Merces** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenantname>.force.com/<instancename>`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://hr2day.force.com/<companyname>`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов HR2day от Merces](mailto:servicedesk@merces.nl). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. В этом разделе показано, как разрешить пользователям проходить проверку подлинности в HR2day от Merces со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

    Приложение HR2day от Merces ожидает проверочные утверждения SAML в определенном формате, поэтому следует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. 

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Чтобы настроить утверждение SAML, обратитесь в [службу технической поддержки клиентов HR2day от Merces](mailto:servicedesk@merces.nl) и запросите значение уникального идентификатора для вашего клиента. Он вам потребуется для выполнения действий, описанных в следующем разделе.  

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия:
    
      | Имя атрибута    |   Значение атрибута |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | join([mail],"102938475Z","@" |
    
      а. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b. В текстовом поле **Имя** введите **ATTR_LOGINCLAIM**.

    c. В списке **Значения** выберите **Join()**.

    г) В списке **String1** выберите **user.mail**.

    д. В списке **String2** введите **уникальный идентификатор**, предоставленный службой поддержки HR2day.

    f. В текстовом поле **Разделитель** введите **@**.
    
    ж. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. В разделе **Настройка HR2day от Merces** щелкните **Настроить HR2day от Merces**, чтобы открыть **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки клиентов HR2day от Merces](mailTo:servicedesk@merces.nl) и вложите в электронное сообщение скачанный файл **сертификата в кодировке Base64**. Также предоставьте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**, чтобы настроить их для интеграции единого входа.

    > [!NOTE]
    > Сообщите службе технической поддержки Merces, что в целях интеграции идентификатор сущности должен быть указан в формате **https://hr2day.force.com/INSTANCENAME**.
    > 
    > 

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-an-hr2day-by-merces-test-user"></a>Создание тестового пользователя HR2day от Merces

Цель этого раздела — создать пользователя с именем Britta Simon в HR2day от Merces. Обратитесь в [службу поддержки клиентов HR2day от Merces](mailto:servicedesk@merces.nl), чтобы добавить пользователей в учетную запись HR2day от Merces. 

> [!NOTE]
> Если вам нужно вручную создать пользователя, необходимо обратиться в [службу поддержки клиентов HR2day от Merces](mailto:servicedesk@merces.nl).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к HR2day от Merces, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в HR2day от Merces, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **HR2day от Merces**.

    ![Настройка единого входа](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент HR2day от Merces на панели доступа, вы автоматически войдете в приложение HR2day от Merces.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png



---
title: Учебник. Интеграция Azure Active Directory с CloudPassage | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 412e2c46ba8dae19892b63899abbfc48ca6a82a8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Учебник. Интеграция Azure Active Directory с CloudPassage

В этом учебнике описано, как интегрировать приложение CloudPassage с Azure Active Directory (Azure AD).

Интеграция приложения CloudPassage с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к CloudPassage.
- Вы можете включить автоматический вход пользователей в CloudPassage (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с CloudPassage, вам потребуется следующее:

- подписка Azure AD;
- подписка CloudPassage с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление CloudPassage из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-cloudpassage-from-the-gallery"></a>Добавление CloudPassage из коллекции
Чтобы настроить интеграцию CloudPassage с Azure AD, вам потребуется добавить CloudPassage из коллекции в список управляемых приложений SaaS.

**Чтобы добавить CloudPassage из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **CloudPassage**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_search.png)

5. На панели результатов выберите **CloudPassage** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в CloudPassage с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в CloudPassage соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в CloudPassage.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в CloudPassage.

Чтобы настроить и проверить единый вход Azure AD в CloudPassage, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя CloudPassage](#creating-a-cloudpassage-test-user)** требуется для создания соответствующего пользователя Britta Simon в CloudPassage, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении CloudPassage.

**Чтобы настроить единый вход Azure AD в CloudPassage, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **CloudPassage** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

3. В разделе **Домен и URL-адреса приложения CloudPassage** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://portal.cloudpassage.com/saml/init/accountid`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://portal.cloudpassage.com/saml/consume/accountid`. Значение этого атрибута можно получить, щелкнув **SSO Setup documentation** (Документация по настройке единого входа) в разделе **Single Sign-on Settings** на портале CloudPassage.

    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов CloudPassage](https://www.cloudpassage.com/company/contact/). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

5. Приложение CloudPassage ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример.
   
   ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:

    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_400.png)
    
8. В разделе **Настройка CloudPassage** щелкните **Настроить Clarizen**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

9. В другом окне браузера войдите на сайт CloudPassage компании в качестве администратора.

10. В верхнем меню щелкните **Параметры**, а затем выберите **Администрирование сайта**. 
   
    ![Настройка единого входа][12]

11. Выберите вкладку **Параметры проверки подлинности** . 
   
    ![Настройка единого входа][13]

12. В разделе **Параметры единого входа** сделайте следующее: 
   
    ![Настройка единого входа][14]

    a. Установите флажок **Enable Single sign-on(SSO)(SSO Setup Documentation)** (Включить единый вход (SSO) (Документация по настройке единого входа)).
    
    Б. Вставьте **идентификатор сущности SAML** в текстовое поле **URL-адрес издателя SAML**.
  
    c. Вставьте **URL-адрес службы единого входа SAML** в текстовое поле **URL-адрес конечной точки SAML**.
  
    d. Вставьте **URL-адрес выхода** в текстовое поле **Целевая страница выхода**.
  
    д. Откройте в блокноте сертификат в кодировке Base-64, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Сертификат X.509**.
  
    f. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Создание тестового пользователя CloudPassage

Цель этого раздела — создать пользователя с именем Britta Simon в CloudPassage.

**Чтобы создать пользователя с именем Britta Simon в CloudPassage, сделайте следующее:**

1. Выполните вход на сайт **CloudPassage** компании в качестве администратора. 

2. На панели инструментов в верхней части экрана щелкните **Settings** (Параметры), а затем выберите **Site Administration** (Администрирование сайта). 
   
   ![Создание тестового пользователя CloudPassage][22] 

3. Откройте вкладку **Пользователи**, а затем щелкните **Добавить нового пользователя**. 
   
   ![Создание тестового пользователя CloudPassage][23]

4. В разделе **Добавить нового пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя CloudPassage][24]
    
    a. В текстовом поле **Имя** введите Britta. 
  
    Б. В текстовом поле **Фамилия** введите Simon.
  
    c. В текстовых полях **Username** (Имя пользователя), **Email** (Электронная почта) и **Retype Email** (Введите адрес еще раз) укажите данные, соответствующие пользователю Britta в Azure AD.
  
    d. Для параметра **Access Type** (Тип доступа) выберите **Enable Halo Portal Access** (Включить доступ к порталу Halo).
  
    д. Щелкните **Добавить**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к CloudPassage.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в CloudPassage, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **CloudPassage**.

    ![Настройка единого входа](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент CloudPassage на панели доступа, вы автоматически войдете в приложение CloudPassage.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_203.png


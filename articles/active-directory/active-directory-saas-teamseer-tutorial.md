---
title: Руководство по интеграции Azure Active Directory с TeamSeer | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: b4fd5b65bf0f5ce90aa93f89aad4617976b3a490
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350854"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Учебник. Интеграция Azure Active Directory с TeamSeer

В этом руководстве описано, как интегрировать TeamSeer с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением TeamSeer обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к TeamSeer.
- Вы можете включить автоматический вход пользователей в TeamSeer (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с TeamSeer, вам потребуется:

- подписка Azure AD;
- подписка TeamSeer с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление TeamSeer из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-teamseer-from-the-gallery"></a>Добавление TeamSeer из коллекции
Чтобы настроить интеграцию TeamSeer с Azure AD, нужно добавить TeamSeer из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TeamSeer из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **TeamSeer**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. На панели результатов выберите **TeamSeer** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение TeamSeer с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в TeamSeer соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TeamSeer.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TeamSeer.

Чтобы настроить и проверить единый вход Azure AD в TeamSeer, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя TeamSeer](#creating-a-teamseer-test-user)** требуется для создания пользователя Britta Simon в TeamSeer, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении TeamSeer.

**Чтобы настроить единый вход Azure AD в TeamSeer, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **TeamSeer** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. В разделе **Домены и URL-адреса приложения TeamSeer** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов TeamSeer](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация TeamSeer** щелкните **Настроить TeamSeer**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт TeamSeer в качестве администратора.

8. Перейдите в раздел **Администратор отдела кадров**.
   
    ![Администратор отдела кадров](./media/active-directory-saas-teamseer-tutorial/ic789634.png "Администратор отдела кадров")

9. Щелкните **Настройка**.
   
    ![Настройка](./media/active-directory-saas-teamseer-tutorial/ic789635.png "Настройка")

10. Щелкните **Задать данные о поставщике SAML**.
   
    ![Параметры SAML](./media/active-directory-saas-teamseer-tutorial/ic789636.png "Параметры SAML")

11. В разделе сведений о поставщике SAML выполните следующие действия:
   
    ![Параметры SAML](./media/active-directory-saas-teamseer-tutorial/ic789637.png "Параметры SAML")   

    a. Вставьте значение **URL-адреса службы единого входа** в текстовое поле **URL-адрес**.
          
    Б. Откройте сертификат в кодировке Base-64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Общий сертификат поставщика удостоверений**.

12. Чтобы завершить настройку поставщика SAML, выполните следующие действия:
    
    ![Параметры SAML](./media/active-directory-saas-teamseer-tutorial/ic789638.png "Параметры SAML") 

    a. В разделе **Тестовые адреса электронной почты**введите адрес электронной почты для тестового пользователя. 
  
    Б. В текстовом поле **Издатель** введите URL-адрес издателя поставщика услуг. 
  
    c. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-teamseer-test-user"></a>Создание тестового пользователя TeamSeer

Чтобы пользователи Azure AD могли выполнить вход в TeamSeer, они должны быть подготовлены в TeamSeer. В случае с TeamSeer подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт **TeamSeer** вашей компании в качестве администратора.

2. Выполните следующие действия:
   
    ![Администратор отдела кадров](./media/active-directory-saas-teamseer-tutorial/ic789640.png "Администратор отдела кадров")  
 
    a. Выберите **HR Admin (Администратор отдела кадров) \> Users (Пользователи)**.
  
    Б. Щелкните **Запустить мастер нового пользователя**.

3. В разделе **Сведения о пользователе** сделайте следующее:
   
    ![Сведения о пользователе](./media/active-directory-saas-teamseer-tutorial/ic789641.png "Сведения о пользователе")

    a. Введите в текстовые поля **First Name** (Имя), **Surname** (Фамилия), **User name (Email address)** (Имя пользователя (адрес электронной почты)) соответствующие данные действующей учетной записи AAD, которую нужно подготовить.
  
    Б. Нажмите кнопку **Далее**.

4. Чтобы добавить нового пользователя, следуйте указаниям на экране и нажмите кнопку **Готово**.

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя TeamSeer или API-интерфейсы, предоставляемые TeamSeer, для подготовки учетных записей пользователей Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к TeamSeer.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению TeamSeer, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **TeamSeer**.

    ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png


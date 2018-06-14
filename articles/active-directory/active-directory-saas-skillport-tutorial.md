---
title: Учебник. Интеграция Azure Active Directory с Skillport | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Skillport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4df349b2-a73f-4b88-a077-ec0fbfc26527
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 269c5aedb2e990a83358605445d19d9d61261f3b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344819"
---
# <a name="tutorial-azure-active-directory-integration-with-skillport"></a>Руководство по интеграции Azure Active Directory с Skillport

В этом руководстве описано, как интегрировать Skillport с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Skillport обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Skillport.
- Вы можете включить автоматический вход пользователей в Skillport (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Skillport, вам потребуется:

- подписка Azure AD;
- подписка на Skillport с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Skillport из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-skillport-from-the-gallery"></a>Добавление Skillport из коллекции
Чтобы настроить интеграцию приложения Skillport с Azure AD, вам нужно добавить Skillport из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Skillport из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Skillport**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_search.png)

5. На панели результатов выберите **Skillport** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Skillport с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Skillport соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Skillport.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Skillport.

Чтобы настроить и проверить единый вход Azure AD в Skillport, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Skillport](#creating-a-skillport-test-user)** требуется для создания в Skillport пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на новом портале Azure и настроить его в приложении Skillport.

**Чтобы настроить единый вход Azure AD в Skillport, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Skillport** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Skillport** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес:
      
      Центр обработки данных в ЕС: `https://adfs.skillport.eu`
   
      Центр обработки данных в США: `https://sso.skillport.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес:
      
      Центр обработки данных в ЕС: `http://adfs.skillport.eu/adfs/services/trust`
   
      Центр обработки данных в США: `https://sso.skillport.com`
   
    c. В текстовом поле **URL-адрес ответа** введите URL-адрес:
    
      Центр обработки данных в ЕС: ` https://adfs.skillport.eu/adfs/ls/`
    
      Центр обработки данных в США: `https://sso.skillport.com/sp/ACS.saml2`
 
4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-skillport-tutorial/tutorial_general_400.png)

6. Чтобы настроить единый вход на стороне **Skillport**, отправьте скачанный **XML-файл метаданных** в [службу поддержки Skillport](https://www.skillsoft.com/contact.asp). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах подключения.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-skillport-test-user"></a>Создание тестового пользователя Skillport

Чтобы создать тестового пользователя Skillport, обратитесь в [службу поддержки Skillport](https://www.skillsoft.com/contact.asp), так как они поддерживают различные бизнес-сценарии в соответствии с требованиями пользователей. Специалисты выполнят настройку после обсуждения с пользователями.  

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Skillport.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Skillport, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Skillport**.

    ![Настройка единого входа](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Skillport на панели доступа, вы автоматически войдете в приложение Skillport.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_203.png


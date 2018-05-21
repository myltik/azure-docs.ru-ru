---
title: Руководство. Интеграция Azure Active Directory с приложением Wdesk | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: c55b9c32ccf6712da69c869a6f44c71e1c209627
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Руководство. Интеграция Azure Active Directory с Wdesk

В этом руководстве описано, как интегрировать Wdesk с Azure Active Directory (Azure AD).

Интеграция приложения Wdesk с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать, у кого есть доступ к приложению Wdesk.
- Вы можете включить автоматический вход пользователей в Wdesk (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Wdesk, вам потребуется:

- подписка Azure AD;
- подписка Wdesk с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Wdesk из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-wdesk-from-the-gallery"></a>Добавление Wdesk из коллекции
Чтобы настроить интеграцию приложения Wdesk с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения Wdesk из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Wdesk**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. На панели результатов выберите **Wdesk** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Wdesk с использованием тестового пользователя Britta Simon.

Для работы единого входа службе Azure AD нужно знать, какой пользователь в Wdesk соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Wdesk.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Wdesk.

Чтобы настроить и проверить единый вход Azure AD в Wdesk, выполните следующие действия:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Wdesk](#creating-a-wdesk-test-user)** требуется для создания в Wdesk пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Wdesk.

**Настройка единого входа Azure AD в Wdesk**

1. На портале Azure на странице интеграции с приложением **Wdesk** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Wdesk** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Эти значения вы можете получить на портале Wdesk при настройке единого входа. 
  
5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. В другом окне браузера войдите в приложение Wdesk с правами администратора безопасности.

8. В нижнем левом углу щелкните **Admin** (Администрирование) и выберите **Account Admin** (Администрирование учетных записей):
 
     ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. В разделе администрирования Wdesk откройте **Security** (Безопасность), затем **SAML** > **SAML Settings** (Параметры SAML):

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. В разделе **General Settings** (Общие параметры) установите флажок **Enable SAML Single Sign On** (Включить единый вход SAML):

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. В разделе **Service Provider Details** (Сведения о поставщике SAML) выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Скопируйте значение **Login URL** (URL-адрес входа) и вставьте его в текстовое поле **URL-адрес входа** на портале Azure.
   
      Б. Скопируйте значение **Metadata Url** (URL-адрес метаданных) и вставьте его в текстовое поле **Идентификатор** на портале Azure.
       
      c. Скопируйте значение **Consumer URL** (URL-адрес потребителя) и вставьте его в текстовое поле **URL-адрес ответа** на портале Azure.
   
      d. На портале Azure нажмите кнопку **Сохранить**, чтобы сохранить изменения.      

12. Щелкните **Configure IdP Settings** (Настройка параметров поставщика удостоверений), чтобы открыть диалоговое окно **изменения параметров поставщика удостоверений**. Щелкните **Choose File** (Выбрать файл) и найдите файл **Metadata.xml**, который вы сохранили с портала Azure, а затем отправьте его.
    
    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Нажмите кнопку **Сохранить изменения**.

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-wdesk-test-user"></a>Создание тестового пользователя Wdesk

Чтобы пользователи Azure AD могли выполнять вход в Wdesk, их следует подготовить в Wdesk. В Wdesk подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Wdesk с правами администратора безопасности.
2. Последовательно выберите **Admin** (Администрирование)  > **Account Admin** (Администрирование учетных записей).

     ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Щелкните элемент **Members** (Участники) в разделе **People** (Люди).

4. Теперь щелкните **Add Member** (Добавить участника), чтобы открыть диалоговое окно **Add Member** (Добавление участника). 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. В текстовое поле **User** (Пользователь) введите имя пользователя, например **brittasimon@contoso.com**, и нажмите кнопку **Continue** (Продолжить).

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Введите необходимые данные, как показано ниже:
  
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. В текстовое поле **E-mail** (Адрес электронной почты) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    Б. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

7. Щелкните кнопку **Save Member** (Сохранить участника).  

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Wdesk.

![Назначение пользователя][200] 

**Назначение пользователя Britta Simon приложению Wdesk**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Wdesk**.

    ![Настройка единого входа](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Wdesk на панели доступа, вы автоматически войдете в приложение Wdesk.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png


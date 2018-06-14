---
title: Руководство по интеграции Azure Active Directory с Workplace by Facebook | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 897ee084e0b36f1729260fabb33114652b82a05d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353693"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Руководство по интеграции Azure Active Directory с Workplace by Facebook

В этом руководстве описано, как интегрировать Workplace by Facebook с Azure Active Directory (Azure AD).

Интеграция Workplace by Facebook с Azure AD дает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Workplace by Facebook.
- Вы можете включить автоматический вход пользователей в Workplace by Facebook (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Workplace by Facebook, вам потребуется:

- подписка Azure AD;
- подписка Workplace by Facebook с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> В Facebook имеются два продукта, Workplace Standard (бесплатный) и Workplace Premium (платный). Любой клиент Workplace Premium позволяет настроить SCIM и интеграцию единого входа без какой-либо дополнительной оплаты или лицензий. Единый вход и SCIM недоступны в экземплярах Workplace Standard.

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Workplace by Facebook из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Добавление Workplace by Facebook из коллекции
Чтобы настроить интеграцию Workplace by Facebook с Azure AD, необходимо добавить Workplace by Facebook из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workplace by Facebook из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Workplace by Facebook**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. На панели результатов выберите **Workplace by Facebook** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Workplace by Facebook с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Workplace by Facebook соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workplace by Facebook.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Workplace by Facebook.

Чтобы настроить и проверить единый вход Azure AD в Workplace by Facebook, вам потребуется выполнить действия в указанных ниже стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка частоты повторной проверки подлинности](#configuring-reauthentication-frequency)** необходима для настройки запроса на проверку SAML в Workplace by Facebook.
3. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Workplace by Facebook](#creating-a-workplace-by-facebook-test-user)** требуется для того, чтобы в Workplace by Facebook существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
6. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Workplace by Facebook.

**Чтобы настроить единый вход Azure AD в Workplace by Facebook, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Workplace by Facebook** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Workplace by Facebook** выполните указанные ниже действия.

    ![Настройка единого входа](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<instancename>.facebook.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Правильные значения для своего сообщества Workplace см. на странице аутентификации панели мониторинга компании Workplace. 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Workplace by Facebook** щелкните **Настроить Workplace by Facebook**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-workplacebyfacebook-tutorial/config.png) 

7. В другом окне веб-браузера войдите на свой корпоративный сайт Workplace by Facebook в качестве администратора.
  
   > [!NOTE] 
   > В рамках процесса проверки подлинности SAML приложение Workplace может использовать строки запросов размером до 2,5 КБ для передачи параметров в Azure AD.

8. На **панели мониторинга компании** перейдите на вкладку **Authentication** (Аутентификация).

9. Из раскрывающегося списка **SAML Authentication** (Аутентификация SAML) выберите **SSO Only** (Только единый вход).

10. Введите значения, скопированные из раздела **Конфигурация Workplace by Facebook** на портале Azure, в соответствующие поля.

    *   В текстовое поле **SAML URL** (URL-адрес SAML) вставьте значение **URL-адрес службы единого входа**, скопированное на портале Azure.
    *   В текстовое поле **SAML Issuer URL** (URL-адрес издателя SAML) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.
    *   В текстовое поле **SAML Logout Redirect** (Перенаправление для выхода SAML) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
    *   Откройте в Блокноте **сертификат в кодировке Base64**, скачанный с портала Azure, скопируйте его содержимое в буфер обмена и вставьте в текстовое поле **SAML Certificate** (Сертификат SAML).

11. Может потребоваться ввести URL-адрес аудитории, URL-адрес получателя и URL-адрес службы обработчика утверждений (ACS), указанные в разделе **Конфигурация SAML**.

12. Прокрутите страницу до конца раздела и нажмите кнопку **Test SSO** (Проверить единый вход). Появится всплывающее окно со страницей входа в Azure AD. Введите учетные данные, как при обычной аутентификации. 

    **Устранение неполадок**. Убедитесь в том, что адрес электронной почты, возвращаемый из Azure AD, совпадает с учетной записью Workplace, которая использовалась для входа.

13. После успешного прохождения проверки прокрутите страницу до конца и нажмите кнопку **Save** (Сохранить).

14. Теперь для аутентификации всех пользователей Workplace будет отображаться страница входа в Azure AD.

15. **Перенаправление для выхода SAML (необязательно)** - 

    При необходимости можно настроить URL-адрес выхода SAML, с помощью которого можно указать страницу выхода из Azure AD. После активации и настройки этого параметра пользователи больше не будут направляться на страницу выхода из Workplace. Вместо этого они будут переходить по URL-адресу, указанному в параметре перенаправления для выхода SAML.

### <a name="configuring-reauthentication-frequency"></a>Настройка частоты повторной проверки подлинности

Вы можете настроить в Workplace запрос на проверку SAML каждый день, каждые 3 дня, каждую неделю, каждые 2 недели, каждый месяц или никогда.

> [!NOTE] 
>Минимальная частота проверки SAML в мобильных приложениях составляет одну неделю.

Вы также можете принудительно сбросить SAML для всех пользователей, нажав кнопку "Require SAML authentication for all users now" (Потребовать проверку подлинности SAML для всех пользователей).


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Создание тестового пользователя Workplace by Facebook

В этом разделе вы создадите в Workplace by Facebook пользователя с именем Britta Simon. Workplace by Facebook поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь не существует в Workplace by Facebook, он создается при попытке доступа к Workplace by Facebook.

>[!Note]
>Если вам нужно создать пользователя вручную, обратитесь к [группе поддержки клиентов Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Workplace by Facebook.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Workplace by Facebook, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Workplace by Facebook**.

    ![Настройка единого входа](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Если вы хотите проверить параметры единого входа, откройте панель доступа.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](active-directory-saas-workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_203.png

---
title: "Руководство по интеграции Azure Active Directory с Workplace by Facebook | Документация Майкрософт"
description: "Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 27e62a00832484667117d8718db9f2fc05e2f4e2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Руководство по интеграции Azure Active Directory с Workplace by Facebook

В этом руководстве описано, как интегрировать Workplace by Facebook с Azure Active Directory (Azure AD).

Интеграция Workplace by Facebook с Azure AD дает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Workplace by Facebook.
- Вы можете включить автоматический вход пользователей в Workplace by Facebook (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложения SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Workplace by Facebook, вам потребуется:

- подписка Azure AD;
- подписка Workplace by Facebook с поддержкой единого входа.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Workplace by Facebook из коллекции.
2. Настройка и проверка единого входа в Azure AD.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Добавление Workplace by Facebook из коллекции
Чтобы настроить интеграцию Workplace by Facebook с Azure AD, необходимо добавить Workplace by Facebook из коллекции в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Выберите **Корпоративные приложения** > **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна щелкните **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Workplace by Facebook** и выберите **Workplace by Facebook** среди результатов. Щелкните **Добавить**, чтобы добавить приложение.

    ![Workplace by Facebook в списке результатов](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Workplace by Facebook с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Workplace by Facebook соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workplace by Facebook.

Чтобы установить эту связь, укажите **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Workplace by Facebook.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Workplace by Facebook.

1. На портале Azure на странице интеграции с приложением **Workplace by Facebook** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** из списка **Режим** выберите значение **Вход на основе SAML**, чтобы включить единый вход.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Workplace by Facebook** выполните следующее.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company subdomain>.facebook.com`.

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.facebook.com/company/<scim company id>`.

    > [!NOTE]
    > Эти значения приведены только для примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Workplace by Facebook](https://workplace.fb.com/faq/). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Workplace by Facebook** щелкните **Настроить Workplace by Facebook**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Конфигурация Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. В другом окне веб-браузера войдите на свой корпоративный сайт Workplace by Facebook в качестве администратора.
  
   > [!NOTE] 
   > В рамках процесса аутентификации SAML приложение Workplace может использовать строки запросов размером до 2,5 КБ для передачи параметров в Azure AD.

8. На **панели мониторинга компании** перейдите на вкладку **Authentication** (Аутентификация).

9. Из раскрывающегося списка **SAML Authentication** (Аутентификация SAML) выберите **SSO Only** (Только единый вход).

10. Введите значения, скопированные из раздела **Конфигурация Workplace by Facebook** на портале Azure, в соответствующие поля.

    *   В текстовое поле **SAML URL** (URL-адрес SAML) вставьте значение **URL-адрес службы единого входа**, скопированное на портале Azure.
    *   В текстовое поле **SAML Issuer URL** (URL-адрес издателя SAML) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.
    *   В текстовое поле **SAML Logout Redirect (optional)** (Перенаправление для выхода SAML (необязательно)) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
    *   Откройте в Блокноте **сертификат в кодировке Base-64**, скачанный с портала Azure. Скопируйте его содержимое в буфер обмена и вставьте в текстовое поле **SAML Certificate** (Сертификат SAML).

11. Может потребоваться ввести URL-адрес аудитории, URL-адрес получателя и URL-адрес службы обработчика утверждений (ACS), указанные в разделе **Конфигурация SAML**.

12. Прокрутите страницу до конца раздела и щелкните **Test SSO** (Проверить единый вход). Появится всплывающее окно со страницей входа в Azure AD. Введите учетные данные, как при обычной аутентификации. Убедитесь в том, что адрес электронной почты, возвращаемый из Azure AD, совпадает с учетной записью Workplace, которая использовалась для входа.

13. Если тест выполнен успешно, прокрутите страницу до конца и щелкните **Сохранить**.

14. Теперь для аутентификации всех пользователей Workplace будет отображаться страница входа в Azure AD.

Можно настроить URL-адрес выхода SAML, с помощью которого можно указать страницу выхода из Azure AD. После активации и настройки этого параметра пользователи больше не будут направляться на страницу выхода из Workplace. Вместо этого они будут переходить по URL-адресу, указанному в параметре перенаправления для выхода SAML.


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка в нижней части страницы**. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="configure-reauthentication-frequency"></a>Настройка частоты повторной аутентификации

Вы можете настроить в Workplace запрос на проверку SAML каждый день, каждые 3 дня, каждую неделю, каждые 2 недели, каждый месяц или никогда.

> [!NOTE] 
>Минимальная частота проверки SAML в мобильных приложениях равна одной неделе.

Можно также принудительно применить SAML для всех пользователей. Для этого нажмите кнопку **Require SAML authentication for all users now** (Применить аутентификацию SAML для всех пользователей).


### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

1. На **портале Azure** в области слева щелкните **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. В диалоговом окне **Пользователь** сделайте следующее.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** пользователя BrittaSimon.

    c. Щелкните **Показать пароль** и запишите пароль.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Создание тестового пользователя Workplace by Facebook

В этом разделе вы создадите в Workplace by Facebook пользователя с именем Britta Simon. Workplace by Facebook поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь не существует в Workplace by Facebook, он создается при попытке доступа к Workplace by Facebook.

>[!Note]
>Если вам нужно создать пользователя вручную, обратитесь к [группе поддержки клиентов Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Workplace by Facebook.

![Назначение пользователя][200] 

1. На портале Azure откройте представление "Приложения". Перейдите к представлению "Каталог", перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Workplace by Facebook**.

    ![Ссылка на Workplace by Facebook в списке "Приложения"](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Выберите **Добавить**. Затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** из списка "Пользователи" выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Если вы хотите проверить параметры единого входа, откройте панель доступа.
Дополнительные сведения см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со [списком учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md).
* Прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* Узнайте больше о том, как [настроить подготовку пользователей](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png


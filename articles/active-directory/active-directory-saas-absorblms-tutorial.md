---
title: Руководство. Интеграция Azure Active Directory с Absorb LMS | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: f877d8fee4a94207fc01f4a5e0e7919f1286f2e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340671"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Руководство. Интеграция Azure Active Directory с Absorb LMS

В этом руководстве описано, как интегрировать приложение Absorb LMS с Azure Active Directory (Azure AD).

Интеграция Absorb LMS с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Absorb LMS.
- Вы можете включить автоматический вход пользователей в Absorb LMS (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно, через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Absorb LMS, вам потребуется:

- подписка Azure AD;
- подписка Absorb LMS с поддержкой единого входа.

> [!NOTE]
> Рекомендуется не использовать рабочую среду для этого руководства.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

* Добавление Absorb LMS из коллекции
* настройка и проверка единого входа в Azure AD.

## <a name="add-absorb-lms-from-the-gallery"></a>Добавление Absorb LMS из коллекции
Чтобы настроить интеграцию Absorb LMS с Azure AD, добавьте Absorb LMS из коллекции в список управляемых приложений SaaS.

Чтобы добавить Absorb LMS из коллекции, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Выберите **Корпоративные приложения** > **Все приложения**.

    ![Область "Корпоративные приложения"][2]
    
3. Чтобы добавить приложение, нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Absorb LMS**, выберите **Absorb LMS** на панели результатов и нажмите кнопку **Добавить**.

    ![Absorb LMS в списке результатов](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Absorb LMS с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в Absorb LMS соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем в Azure AD и соответствующим пользователем в Absorb LMS.

Чтобы установить эту связь, следует назначить *имя пользователя* в Azure AD в качестве значения *имени пользователя* в Absorb LMS.

Чтобы настроить и проверить единый вход Azure AD в Absorb LMS, выполните действия в следующих пяти разделах.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Absorb LMS.

Чтобы настроить единый вход Azure AD в Absorb LMS, выполните следующие действия.

1. На портале Azure на странице интеграции с приложением **Absorb LMS** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** из списка **Режим** выберите пункт **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Absorb LMS** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. В поле **Идентификатор** введите URL-адрес, используя следующий синтаксис: `https://<subdomain>.myabsorb.com/Account/SAML`.

    Б. В поле **URL-адрес ответа** введите URL-адрес, используя следующий синтаксис: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > URL-адреса, указанные выше, приведены в качестве примера. Измените их фактическими значениями идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Absorb LMS](https://www.absorblms.com/support). 

4. В разделе **Сертификат подписи SAML** в столбце **Скачать** выберите **XML метаданных** и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата для подписи](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация Absorb LMS** выберите **Настроить Absorb LMS**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода** в разделе **Краткий справочник**.

    ![Область "Конфигурация Absorb LMS"](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. В новом окне веб-браузера войдите на свой корпоративный веб-сайт Absorb LMS в качестве администратора.

8. Нажмите кнопку **Account** (Учетная запись) в правом верхнем углу. 

    ![Кнопка "Account" (Учетная запись)](./media/active-directory-saas-absorblms-tutorial/1.png)

9. В области "Account" (Учетная запись) щелкните **Portal Settings** (Параметры портала).

    ![Ссылка "Portal Settings" (Параметры портала)](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Перейдите на вкладку **Пользователи** .

    ![Вкладка "Пользователи"](./media/active-directory-saas-absorblms-tutorial/3.png)

11. На странице "Single Sign-On Configuration" (Конфигурация единого входа) выполните следующие действия.

    ![Страница "Single Sign-On Configuration" (Конфигурация единого входа)](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. В поле **Mode** (Режим) выберите **Identity Provider Initiated** (Инициируемый поставщиком удостоверений).

    Б. В Блокноте откройте сертификат, скачанный с портала Azure. Удалите теги **---BEGIN CERTIFICATE---** и **---END CERTIFICATE---**. Затем в поле **Key** (Ключ) вставьте оставшееся содержимое.
    
    c. В поле **Id Property** (Свойство идентификатора) выберите атрибут, который вы настроили в качестве идентификатора пользователя в Azure AD. Например, если вы выбрали в Azure AD *userPrincipalName*, то выберите атрибут **Username**.

    d. В поле **Login URL** (URL-адрес входа) вставьте значение **URL-адрес пользовательского доступа** со страницы **Свойства** приложения на портале Azure.

    д. В поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное из окна **Настройка единого входа** на портале Azure.

12. Для параметра **Only Allow SSO Login** (Разрешить только единый вход) установите значение **On** (Вкл.).

    ![Включение параметра "Only Allow SSO Login" (Разрешить только единый вход)](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Щелкните **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций можно также прочесть на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход** и откройте встроенную документацию, воспользовавшись разделом **Настройка** в нижней части страницы. Чтобы узнать больше, ознакомьтесь со [встроенной документацией по Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе вы создадите на портале Azure тестового пользователя Britta Simon.

![Создание тестового пользователя Azure AD][100]

Чтобы создать тестового пользователя в Azure AD, выполните следующее.

1. На портале Azure в области слева щелкните **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, выберите **Пользователи и группы** > **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. В диалоговом окне **Пользователь** сделайте следующее.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите адрес электронной почты учетной записи Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-an-absorb-lms-test-user"></a>Создание тестового пользователя Absorb LMS

Чтобы пользователи Azure AD могли входить в Absorb LMS, их нужно настроить в Absorb LMS.  

Эта настройка для Absorb LMS выполняется вручную.

Чтобы настроить учетную запись пользователя, выполните следующие действия.

1. Войдите на свой корпоративный веб-сайт Absorb LMS в качестве администратора.

2. В левой области щелкните **Users** (Пользователи).

    ![Ссылка на Absorb LMS на странице "Users" (Пользователи)](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. В области **Users** (Пользователи) щелкните **Users** (Пользователи).

    ![Ссылка "Users" (Пользователи)](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. Из раскрывающегося списка **Add New** (Добавить) выберите **User** (Пользователь).

    ![Раскрывающийся список "Add New" (Добавить)](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. На странице **Add User** (Добавление пользователя) сделайте следующее.

    ![Страница "Add User" (Добавление пользователя)](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. В текстовом поле **First Name** (Имя) введите имя, например **Britta**.

    Б. В текстовом поле **Last Name** (Фамилия) введите фамилию, например **Simon**.
    
    c. В текстовом поле **Username** (Имя пользователя) введите полное имя, например **Britta Simon**.

    d. В поле **Password** (Пароль) введите пароль пользователя Britta Simon.

    д. В поле **Confirm Password** (Подтверждение пароля) введите пароль еще раз.
    
    f. Переведите переключатель **Is Active** (Активность) в положение **Active** (Активный).  

6. Щелкните **Сохранить**.
 
### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Absorb LMS.

![Назначение роли пользователя][200]

Чтобы назначить пользователя Britta Simon в Absorb LMS, выполните следующие действия.

1. На портале Azure откройте представление "Приложения", перейдите к представлению "Каталог" и выберите **Корпоративные приложения** > **Все приложения**.

    ![Ссылка "Все приложения"][201] 

2. Из списка **Приложения** выберите **Absorb LMS**.

    ![Ссылка на Absorb LMS в списке "Приложения"](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. В области слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Нажмите кнопку **Добавить**, затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент **Absorb LMS** на панели доступа, вы автоматически войдете в приложение Absorb LMS. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


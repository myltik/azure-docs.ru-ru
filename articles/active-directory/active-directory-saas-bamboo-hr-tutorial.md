---
title: Руководство по интеграции Azure Active Directory с BambooHR | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: c63a625c1eca7008c751d6904f182f76ee5f343b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Руководство по интеграции Azure Active Directory с BambooHR

В этом руководстве описано, как интегрировать BambooHR с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением BambooHR обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к BambooHR.
- Вы можете включить автоматический вход пользователей в BambooHR (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно, через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с BambooHR, вам потребуется:

- подписка Azure AD;
- подписка на BambooHR с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для тестирования действий, выполняемых в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [бесплатно получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом руководстве, состоит из двух основных стандартных блоков:

1. Добавление BambooHR из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="add-bamboohr-from-the-gallery"></a>Добавление BambooHR из коллекции
Чтобы настроить интеграцию BambooHR с Azure AD, добавьте BambooHR из коллекции в список управляемых приложений SaaS следующим образом:

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Область "Корпоративные приложения"][2]
    
3. Чтобы добавить приложение, выберите **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **BambooHR**. В списке результатов выберите **BambooHR**, а затем щелкните **Добавить**.

    ![BambooHR в списке результатов](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение BambooHR с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать аналог этого пользователя в BambooHR. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BambooHR.

Чтобы установить эту связь в BambooHR, присвойте **имени пользователя** в Azure AD значение **имени пользователя** в BambooHR.

Чтобы настроить и проверить единый вход Azure AD в BambooHR, выполните действия в следующих пяти разделах.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе вы включите единый вход Azure AD на портале Azure и настроите единый вход в приложении BambooHR. Для этого выполните следующее:

1. На портале Azure на странице интеграции с приложением **BambooHR** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В окне **единого входа** из раскрывающегося списка **Режим единого входа** выберите параметр **Вход на основе SAML**.
 
    ![Окно единого входа](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. В разделе **Домены и URL-адреса приложения BambooHR** выполните следующие действия.

    ![Раздел "Домены и URL-адреса приложения BambooHR"](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. В поле **URL-адрес входа** введите URL-адрес в следующем формате: `https://<company>.bamboohr.com`.

    Б. В поле **Идентификатор** введите следующее значение: `BambooHR-SAML`.

    > [!NOTE] 
    > Значение **URL-адреса входа** приведено для примера. Замените его на ваш фактический URL-адрес входа. Для получения данного значения обратитесь в [службу поддержки клиентов BambooHR](https://www.bamboohr.com/contact.php). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)** и сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Щелкните **Сохранить**.

    ![Кнопка "Сохранить"](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. В разделе **Настройка BambooHR** выберите **Настроить BambooHR**, чтобы открыть окно **Настройка единого входа**. В разделе **Quick Reference** (Краткий справочник) скопируйте **URL-адрес службы единого входа SAML** для последующего использования.

    ![Настройка BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. В новом окне войдите на свой корпоративный веб-сайт BambooHR в качестве администратора.

8. На домашней странице сделайте следующее.
   
    ![Страница единого входа в BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Единый вход")   

    a. Выберите **Apps** (Приложения).
   
    Б. На панели **Apps** (Приложения) выберите **Single Sign-On** (Единый вход).
   
    c. Выберите **SAML Single Sign-On** (Единый вход SAML).

9. На панели **SAML Single Sign-On** (Единый вход SAML) выполните следующие действия.
   
    ![Панель единого входа SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Единый вход SAML")
   
    a. В поле **SSO Login URL** (URL-адрес для единого входа) вставьте **URL-адрес службы единого входа SAML**, скопированный на портале Azure в 6 шаге.
      
    Б. В Блокноте откройте скачанный с портала Azure сертификат в кодировке base-64, скопируйте его содержимое, а затем вставьте его в поле **X.509 Certificate** (Сертификат X.509).
   
    c. Щелкните **Сохранить**.

> [!TIP]
> Во время настройки приложения можно прочесть краткую версию этих инструкций на [портале Azure](https://portal.azure.com). После добавления приложения из раздела **Active Directory** > **Корпоративные приложения** просто выберите вкладку **Единый вход** и ознакомьтесь со встроенной документацией, воспользовавшись разделом **Настройка** внизу. Чтобы узнать больше, ознакомьтесь со [встроенной документацией по Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе на портале Azure создается тестовый пользователь с именем Britta Simon.

   ![Создание тестового пользователя Britta Simon в Azure AD][100]

Чтобы создать тестового пользователя в Azure AD, выполните следующее.

1. На портале Azure в области слева щелкните **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. В верхней части области **Все пользователи** выберите **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. В окне **Пользователь** сделайте следующее.

    ![Окно пользователя](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-bamboohr-test-user"></a>Создание тестового пользователя BambooHR

Чтобы включить вход для пользователей Azure AD в BambooHR, настройте их вручную следующим образом:

1. Войдите на свой веб-сайт **BambooHR** в качестве администратора.

2. Вверху панели инструментов выберите **Settings** (Параметры).
   
    ![Кнопка параметров](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Параметры")

3. Щелкните **Обзор**.

4. В области слева выберите **Security** (Безопасность) > **Users** (Пользователи).

5. Введите имя пользователя, пароль и адрес электронной почты действительной учетной записи Azure AD, которую необходимо настроить.

6. Щелкните **Сохранить**.
        
>[!NOTE]
>Настроить учетные записи пользователей Azure AD можно также с помощью соответствующих средств создания BambooHR или API.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Разрешите пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к BambooHR.

![Назначение роли пользователя][200] 

Чтобы назначить пользователя Britta Simon в BambooHR, выполните следующие действия.

1. На портале Azure откройте представление "Приложения", перейдите к представлению "Каталог" и выберите **Корпоративные приложения** > **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке **Корпоративные приложения** выберите **BambooHR**.

    ![Ссылка на BambooHR в списке "Корпоративные приложения"](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. В области слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**, затем в области **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. Нажмите кнопку **Select** (Выбрать).

7. В окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Выполните проверку конфигурации единого входа Azure AD с помощью панели доступа.

Выбрав плитку **BambooHR** на панели доступа, вы автоматически войдете в приложение BambooHR.

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список руководств по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png


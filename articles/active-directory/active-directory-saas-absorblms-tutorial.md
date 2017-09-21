---
title: "Руководство. Интеграция Azure Active Directory с Absorb LMS | Документы Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении Absorb LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f5f20fee648cf26b4db49b9a8f5f5e5bcecd453e
ms.contentlocale: ru-ru
ms.lasthandoff: 09/13/2017

---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Руководство. Интеграция Azure Active Directory с Absorb LMS

В этом руководстве описано, как интегрировать приложение Absorb LMS с Azure Active Directory (Azure AD).

Интеграция Absorb LMS с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Absorb LMS.
- Вы можете включить автоматический вход пользователей в Absorb LMS (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Absorb LMS, вам потребуется:

- подписка Azure AD;
- подписка Absorb LMS с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Absorb LMS из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-absorb-lms-from-the-gallery"></a>Добавление Absorb LMS из коллекции
Чтобы настроить интеграцию Absorb LMS с Azure AD, нужно добавить Absorb LMS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Absorb LMS из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Absorb LMS**, выберите **Absorb LMS** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Absorb LMS в списке результатов](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Absorb LMS с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в Absorb LMS соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Absorb LMS.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в Absorb LMS.

Чтобы настроить и проверить единый вход Azure AD в Absorb LMS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Absorb LMS](#create-an-absorb-lms-test-user)** требуется для того, чтобы в Absorb LMS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Absorb LMS.

**Чтобы настроить единый вход Azure AD в Absorb LMS, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Absorb LMS** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Absorb LMS** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.myabsorb.com/Account/SAML`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Absorb LMS](https://www.absorblms.com/support). 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. В разделе **Настройка Absorb LMS** щелкните **Настроить Absorb LMS**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода** из раздела **Краткий справочник**.

    ![Конфигурация Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Absorb LMS в качестве администратора.

9. Щелкните **значок учетной записи** в интерфейсе администратора. 

    ![Настройка единого входа](./media/active-directory-saas-absorblms-tutorial/1.png)

10. Щелкните **Параметры портала**.

    ![Настройка единого входа](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11. Откройте вкладку **Пользователи** .

    ![Настройка единого входа](./media/active-directory-saas-absorblms-tutorial/3.png)

12. Для доступа к полям конфигурации единого входа выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-absorblms-tutorial/4.png)

    а. Выберите **Identity Provider Initiated** (Инициируемый поставщиком удостоверений).

    b. Откройте сертификат, скачанный с портала Azure, в Блокноте. Удалите теги **---BEGIN CERTIFICATE---** и **---END CERTIFICATE---**, а затем вставьте остальное содержимое в текстовое поле **Key** (Ключ).
    
    c. В поле **Id Property** (Свойство идентификатора) выберите подходящий атрибут, настроенный в качестве идентификатора пользователя в Azure AD (например, если в Azure AD выбран userprinciplename, здесь будет выбрано имя пользователя).

    d. В поле **Login URL** (URL-адрес входа) вставьте значение **URL-адрес пользовательского доступа** со страницы **Свойства** приложения на портале Azure.

    д. В поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адреса выхода**, скопированное из окна **Настройка единого входа** портала Azure.

13. Включите параметр **Only Allow SSO Login** (Разрешить только единый вход).

    ![Настройка единого входа](./media/active-directory-saas-absorblms-tutorial/5.png)

14. Нажмите кнопку **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.

### <a name="create-an-absorb-lms-test-user"></a>Создание тестового пользователя Absorb LMS

Чтобы пользователи Azure AD могли входить в Absorb LMS, их нужно подготовить для Absorb LMS.  
Эта подготовка для Absorb LMS выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт организации Absorb LMS в качестве администратора.

2. Откройте вкладку **Пользователи**.

    ![Приглашение пользователей](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. На вкладке **Пользователи** щелкните **Пользователи**.

    ![Приглашение пользователей](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  Выберите **Пользователя** в раскрывающемся списке **Добавить новый**.

    ![Приглашение пользователей](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. На странице **Добавление пользователя** сделайте следующее:

    ![Приглашение пользователей](./media/active-directory-saas-absorblms-tutorial/user.png)

    а. В текстовом поле **Имя** введите имя, например Britta.

    b. В текстовом поле **Фамилия** введите фамилию, например Simon.
    
    c. В текстовом поле **Имя пользователя** введите имя пользователя, например Britta Simon.

    г) В текстовом поле **Пароль** введите пароль пользователя Britta Simon.

    д. В текстовом поле **Подтверждение пароля** введите пароль еще раз.
    
    f. Выберите значение **Активно**.   

6. Нажмите кнопку **Сохранить**.
 
### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Absorb LMS.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Absorb LMS, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Absorb LMS**.

    ![Ссылка на Absorb LMS в списке "Приложения"](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Absorb LMS на панели доступа, вы автоматически войдете в приложение Absorb LMS. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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



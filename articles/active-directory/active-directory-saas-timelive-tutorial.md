---
title: Руководство по интеграции Azure Active Directory с TimeLive | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TimeLive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 34123629-4ad5-465c-a4c1-8299f857e720
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: e25ded9097a8250206f5bec3c76e872044ba597d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-timelive"></a>Руководство по интеграции Azure Active Directory с TimeLive

В этом руководстве описано, как интегрировать TimeLive с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением TimeLive обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к TimeLive.
- Вы можете включить автоматический вход пользователей в TimeLive (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с TimeLive, вам потребуется:

- подписка Azure AD;
- подписка TimeLive с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление TimeLive из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-timelive-from-the-gallery"></a>Добавление TimeLive из коллекции
Чтобы настроить интеграцию TimeLive с Azure AD, необходимо добавить TimeLive из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TimeLive из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **TimeLive**, выберите **TimeLive** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![TimeLive в списке результатов](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение TimeLive с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в TimeLive соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TimeLive.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TimeLive.

Чтобы настроить и проверить единый вход Azure AD в TimeLive, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения TimeLive](#create-a-timelive-test-user)** требуется для того, чтобы в TimeLive существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении TimeLive.

**Чтобы настроить единый вход Azure AD в TimeLive, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **TimeLive** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_samlbase.png)

3. В разделе **Домены и URL-адреса приложения TimeLive** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения TimeLive](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://domainname.livetecs.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://domainname.livetecs.com/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса единого входа. Чтобы получить их, обратитесь к [группе поддержки клиентов TimeLive](mailto:support@livetecs.com). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-timelive-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация TimeLive** щелкните **Настроить TimeLive**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация TimeLive](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_configure.png)

7. В другом окне браузера войдите на ваш корпоративный веб-сайт TimeLive в качестве администратора.

8. Выберите **Preferences** (Параметры) в разделе **Admin Options** (Параметры администрирования).

    ![Конфигурация TimeLive](./media/active-directory-saas-timelive-tutorial/configure1.png)

9. В разделе **Application Preference** (Параметры приложения) сделайте следующее.
    
    ![Конфигурация TimeLive](./media/active-directory-saas-timelive-tutorial/configure2.png)

    a. Выберите вкладку **Security** (Безопасность).

    Б. Установите флажок **Enable Single Sign On (SSO)** (Разрешить единый вход).

    c. Выберите **SAML** из раскрывающегося меню с заголовком **Sign in using Single Sign-On (SSO) with** (Вход с использованием единого входа на основе).

    d. В текстовое поле **SAML SSO URL** (URL-адрес единого входа SAML) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    д. В текстовое поле **Remote logout URL** (URL-адрес удаленного выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    f. Откройте в Блокноте скачанный с портала Azure **сертификат в кодировке Base-64**, скопируйте его содержимое, а затем вставьте его в текстовое поле **X.509 Certificate** (Сертификат X.509).

    ж. Нажмите кнопку **Обновить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-timelive-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-timelive-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-timelive-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-timelive-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-timelive-test-user"></a>Создание тестового пользователя TimeLive

Цель этого раздела — создать пользователя с именем Britta Simon в TimeLive. Приложение TimeLive поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению TimeLive (если он еще не создан).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TimeLive.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в TimeLive, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **TimeLive**.

    ![Ссылка на TimeLive в списке "Приложения"](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "TimeLive" на панели доступа, вы автоматически войдете в приложение TimeLive.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_203.png


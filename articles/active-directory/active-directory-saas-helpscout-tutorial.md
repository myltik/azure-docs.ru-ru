---
title: Учебник. Интеграция Azure Active Directory с Help Scout | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: 596a1ba27516755e586d88ab56a20a1daed63fc2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341310"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Учебник. Интеграция Azure Active Directory с Help Scout

В этом учебнике описано, как интегрировать Help Scout с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Help Scout обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Help Scout.
- Вы можете включить автоматический вход пользователей в Help Scout (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Help Scout, вам потребуется:

- подписка Azure AD;
- подписка Help Scout с поддержкой единого входа.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Help Scout из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-help-scout-from-the-gallery"></a>Добавление Help Scout из коллекции
Чтобы настроить интеграцию Help Scout с Azure AD, необходимо добавить Help Scout из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Help Scout из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Help Scout**, выберите **Help Scout** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Help Scout в списке результатов](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Help Scout с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Help Scout соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Help Scout.

Help Scout использует адреса электронной почты для входа в систему, поэтому для установки связи используйте тот же **адрес электронной почты**, что и для **имени пользователя** в Azure AD.

Чтобы настроить и проверить единый вход Azure AD в Help Scout, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Help Scout](#create-a-help-scout-test-user)** требуется для того, чтобы в Help Scout существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Help Scout.

**Чтобы настроить единый вход Azure AD в Help Scout, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Help Scout** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Если вы хотите настроить приложение в режиме, **инициированном поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Help Scout** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Идентификатор** — это **URI аудитории (идентификатор сущности поставщика службы)** из Help Scout, который начинается с `urn:`.

    Б. **URL-адрес ответа** — это **URL-адрес обратной отправки (URL-адрес службы обработчика утверждений)** из Help Scout, который начинается с `https://`. 

    > [!NOTE] 
    > Значения этих URL-адресов приведены только в качестве примера. Замените эти значения фактическими URL-адресом ответа и идентификатором. Эти значения можно найти на вкладке **Single Sign-On** (Единый вход) в разделе Authentication (Аутентификация), как описано далее в этом руководстве.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Сведения о домене и URL-адресах единого входа приложения Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://secure.helpscout.net/members/login/`.
     
5. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. В разделе **Настройка Help Scout** щелкните **Настроить Help Scout**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Help Scout в качестве администратора.

9. После того как вы вошли в систему, щелкните **Manage** (Управление) в верхнем меню, а затем выберите **Company** (Компания) в раскрывающемся меню.

    ![Настройка единого входа](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. В меню слева выберите **Authentication** (Аутентификация). 

    ![Настройка единого входа](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Отобразится раздел SAML settings (Параметры SAML), после чего выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Скопируйте значение **URL-адреса обратной отправки (URL-адрес службы обработчика утверждений)** и вставьте его в поле **URL-адрес ответа** на портале Azure в разделе **Домены и URL-адреса приложения Help Scout**.
    
    Б. Скопируйте значение **URI аудитории (идентификатор сущности поставщика службы)** и вставьте его в поле **Идентификатор** на портале Azure в разделе **Домены и URL-адреса приложения Help Scout**.

12. Переключите параметр **Enable SAML** (Включить SAML) в положение "Вкл." и выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. В текстовое поле **Single Sign-On URL** (URL-адрес для единого входа) вставьте значение **URL-адреса службы единого входа**, скопированное на портале Azure.
    
    Б. Нажмите кнопку **Upload Certificate** (Отправить сертификат), чтобы передать **сертификат(Base64)**, скачанный с портала Azure.

    c. Введите домен электронной почты вашей организации, например `contoso.com`, в текстовом поле **Email Domains** (Почтовые домены). Несколько доменов можно разделять запятыми. Пользователь или администратор Help Scout, входящий в этот домен на [странице входа Help Scout](https://secure.helpscout.net/members/login/), будет перенаправлен к поставщику удостоверений для проверки подлинности учетных данных.

    d. Наконец, можно включить параметр **Force SAML Sign-on** (Принудительный вход SAML), если вы хотите, чтобы пользователи входили в Help Scout только с помощью этого метода. Если вы все же хотите оставить для них возможность войти в систему с помощью своих учетных данных Help Scout, можете оставить параметр отключенным. Даже если эта функция включена, владелец учетной записи всегда сможет войти в Help Scout с помощью своего пароля учетной записи.

    д. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-help-scout-test-user"></a>Создание тестового пользователя Help Scout

Цель этого раздела — создать пользователя с именем Britta Simon в Help Scout. Приложение Help Scout поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Help Scout, он создается при попытке доступа к приложению Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Help Scout.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Help Scout, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Help Scout**.

    ![Ссылка на Help Scout в списке "Приложения"](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Help Scout на панели доступа, вы автоматически войдете в приложение Help Scout.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


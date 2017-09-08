---
title: "Учебник. Интеграция Azure Active Directory с Help Scout | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении Help Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Учебник. Интеграция Azure Active Directory с Help Scout

В этом учебнике описано, как интегрировать Help Scout с Azure Active Directory (Azure AD).

Интеграция Azure AD с Help Scout обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Help Scout.
- Вы можете включить автоматический вход пользователей в Help Scout с помощью единого входа и учетной записи пользователя Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Help Scout, вам потребуется:

- подписка Azure AD;
- подписка Help Scout с включенным единым входом. 

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий, выполняемых в этом руководстве.

Для проверки действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Help Scout из галереи.
2. Настройка и проверка единого входа Azure AD.

## <a name="add-help-scout-from-the-gallery"></a>Добавление Help Scout из галереи
Чтобы настроить интеграцию Help Scout с Azure AD, в галерее необходимо добавить Help Scout в список управляемых приложений SaaS.

Чтобы добавить Help Scout из галереи, сделайте следующее:

1. На [портале Azure](https://portal.azure.com) в меню слева щелкните **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Страница "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, выберите **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Help Scout**. В результатах поиска выберите **Help Scout**, а затем щелкните **Добавить**.

    ![Help Scout в списке результатов](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Help Scout с использованием тестового имени пользователя *Britta Simon*.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Azure AD соответствует пользователю в Help Scout. Необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Help Scout.

Для этого назначьте **имя пользователя** Azure AD в качестве значения **имени пользователя** в Help Scout.

Чтобы настроить и проверить единый вход Azure Active Directory в Help Scout, вам потребуется выполнить следующие действия:

1. [Настройка единого входа Azure AD](#set-up-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Help Scout](#create-a-help-scout-test-user) требуется, чтобы в Help Scout существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) позволяет пользователю Britta Simon использовать единый вход Azure AD.
5. [Проверка единого входа](#test-single-sign-on) необходима, чтобы убедиться, что конфигурация работает правильно.

### <a name="set-up-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе вы включите единый вход Azure AD на портале Azure, а затем настроите его в приложении Help Scout.

Чтобы настроить единый вход Azure AD в Help Scout, сделайте следующее:

1. На портале Azure на странице интеграции с приложением **Help Scout** выберите **Единый вход**.
 
    ![Ссылка "Настройка единого входа"][4]

2. На странице **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, в разделе **Домены и URL-адреса приложения Help Scout** сделайте следующее:

    1. В поле **Идентификатор** введите URL-адрес в следующем формате: `urn:auth0:helpscout:<instancename>`.

    2. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://helpscout.auth0.com/login/callback?connection=<instancename>`.

    ![Сведения о домене и URL-адресах единого входа приложения Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. Если вы хотите настроить приложение в режиме, инициируемом поставщиком услуг, установите флажок **Показать дополнительные настройки URL-адресов** и сделайте следующее:

    * В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://secure.helpscout.net/members/login/`.

    ![Сведения о домене и URL-адресах единого входа приложения Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > Значения этих URL-адресов приведены только в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить их, обратитесь в [службу поддержки клиентов Help Scout](mailto:help@helpscout.com). 

5. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. Чтобы настроить единый вход на стороне Help Scout, отправьте скачанный XML-файл метаданных в [службу поддержки Help Scout](mailto:help@helpscout.com). Специалисты службы поддержки Help Scout используют этот параметр для правильной настройки подключения единого входа SAML с обеих сторон.

> [!TIP]
> Краткую версию этих инструкций можно также прочесть на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход**. Откройте встроенную документацию в разделе **Настройка** в нижней части страницы. Чтобы узнать больше, ознакомьтесь со [встроенной документацией по Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

Чтобы создать тестового пользователя в Azure AD, сделайте следующее:

1. На портале Azure в меню слева щелкните **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, выберите **Пользователи и группы**, а затем — **Все пользователи**.

    ![Выбор элементов "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части страницы **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    1. В поле **Имя** введите **BrittaSimon**.

    2. В поле **Имя пользователя** введите адрес электронной почты пользователя Britta Simon.

    3. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    4. Нажмите кнопку **Создать**.

        ![Диалоговое окно "Пользователь"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>Создание тестового пользователя Help Scout

Цель этого раздела — создать пользователя с именем Britta Simon в Help Scout. Help Scout поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе не нужно ничего делать. Если пользователь еще не существует в Help Scout, он создается при попытке доступа к приложению Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Здесь описано, как разрешить пользователю Britta Simon использовать единый вход Azure AD, предоставив доступ учетным записям пользователя к Help Scout.

![Назначение роли пользователя][200] 

Чтобы назначить пользователя Britta Simon в Help Scout, сделайте следующее:

1. На портале Azure откройте представление приложений и перейдите к представлению каталога. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Help Scout**.

    ![Ссылка на Help Scout в списке "Приложения"](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Выберите **Добавить**. Затем на странице **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. На странице **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. На странице **Пользователи и группы** щелкните **Выбрать**.

7. На странице **Добавление назначения** выберите **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Выбрав плитку Help Scout на панели доступа, вы должны автоматически войти в приложение Help Scout.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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



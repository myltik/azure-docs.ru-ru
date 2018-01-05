---
title: "Учебник: Интеграция Azure Active Directory с Rollbar | Документы Microsoft"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и Rollbar."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: bb8a81327163513ab721d2ad72da19173b59bc1f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Учебник: Интеграция Azure Active Directory с Rollbar

В этом учебнике вы узнаете, как интегрировать Rollbar с Azure Active Directory (Azure AD).

Интеграция с Azure AD Rollbar предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к Rollbar.
- Позволяет пользователям автоматически получить вошедшего в Rollbar (Single Sign-On) с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Rollbar, необходимы следующие элементы:

- подписка Azure AD;
- Rollbar единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Rollbar из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-rollbar-from-the-gallery"></a>Добавление Rollbar из галереи
Чтобы настроить интеграцию Rollbar в Azure AD, необходимо добавить Rollbar из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Rollbar из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Rollbar**выберите **Rollbar** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![Rollbar в списке результатов](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с Rollbar основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать пользователя аналога в Rollbar для пользователя в Azure AD. Другими словами связи между пользователя Azure AD и соответствующих пользователей в Rollbar необходимо установить.

В Rollbar, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Для настройки и проверки Azure AD единого входа с Rollbar, необходимые для выполнения следующих блоков.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Rollbar](#create-a-rollbar-test-user)**  — на аналог Саймон Britta в Rollbar, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении Rollbar.

**Чтобы настроить Azure AD единого входа с Rollbar, выполните следующие действия.**

1. На портале Azure на **Rollbar** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. На **URL-адреса и домена Rollbar** выполните следующие действия, если вы хотите настроить приложение в **IDP** инициировал режим:

    ![URL-адреса и Rollbar домена единого входа сведения](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://saml.rollbar.com`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://rollbar.com/<accountname>/saml/sso/azure/`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![URL-адреса и Rollbar домена единого входа сведения](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Обратитесь к [Rollbar клиента поддержки](mailto:support@rollbar.com) для получения этих значений. 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. В другом окне браузера Войдите на сайт компании Rollbar с правами администратора.

8. Щелкните **параметры профиля** в правом верхнем углу и выберите **параметры учетной записи имени**.
    
    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/general.png)

9. Нажмите кнопку **поставщика удостоверений** в группе безопасности.

    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. В **поставщика удостоверений SAML** выполните следующие действия:
    
    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. Выберите **AZURE** из **поставщика удостоверений SAML** раскрывающегося списка.

    Б. Откройте файл метаданных в блокноте, скопируйте его содержимое в буфер обмена и вставьте его в **метаданные SAML** текстового поля.

    c. Выберите команду **Сохранить**.

11. После нажатия кнопки сохранения кнопки экрана будет иметь следующий вид. В этом разделе, выполните следующие действия:
    
    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/configure3.png)

    a. Выберите **требуют входа через поставщика удостоверений SAML** флажок.

    Б. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-rollbar-test-user"></a>Создание тестового пользователя Rollbar

Чтобы пользователи Azure AD для входа Rollbar, их необходимо подготовить в Rollbar. В случае Rollbar Подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на сайт компании Rollbar с правами администратора.

2. Щелкните **параметры профиля** в правом верхнем углу и выберите **параметры учетной записи имени**.

    ![Пользователь](./media/active-directory-saas-rollbar-tutorial/general.png)

3. Выберите раздел **Пользователи**.
    
    ![Добавление сотрудника](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. Нажмите кнопку **пригласить членов команды**.

    ![Приглашение пользователей](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. В текстовом поле введите имя пользователя, таких как  **brittasimon@contoso.com**  и нажмите кнопку **Add/приглашения**.

    ![Приглашение пользователей](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. Пользователь получает приглашение, и после принятия его он создан в системе.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к Rollbar.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta Rollbar, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Rollbar**.

    ![Rollbar ссылку в список приложений](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии кнопки Rollbar плитки на панели доступа, вы должны получить автоматически вошедшего в приложение Rollbar.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png


---
title: "Руководство. Интеграция Azure Active Directory с Central Desktop | Документация Майкрософт"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и Central Desktop."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 94c67bef7a0c6ba60fc9c7a60c79a23bf7984fb1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Учебник. Интеграция Azure Active Directory с Central Desktop

В этом учебнике вы узнаете, как интегрировать Central Desktop с Azure Active Directory (Azure AD).

Интеграция Central Desktop с Azure AD предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к Central Desktop.
- Позволяет пользователям автоматически получить входить в Central Desktop с их учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Central Desktop, необходимо следующее:

- подписка Azure AD;
- Подписка единого входа с включенным Central Desktop

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас еще нет среде пробной версии Azure AD, [получить бесплатную пробную версию один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление Central Desktop из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="add-central-desktop-from-the-gallery"></a>Добавить Central Desktop из галереи
Для настройки интеграции Central Desktop в Azure AD, необходимо добавить Central Desktop из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Central Desktop из коллекции, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новые приложения, выберите **новое приложение** кнопку в верхней части диалоговое окно.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Central Desktop**. Выберите **Central Desktop** из панели «результаты», а затем выберите **добавить** кнопку, чтобы добавить приложение.

    ![Central Desktop в списке результатов](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с Central Desktop, на основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать, кто является пользователем аналога в Central Desktop для пользователя в Azure AD. Другими словами необходимо установить связь между пользователем Azure AD и связанных пользователей в Central Desktop.

В Central Desktop предоставляет **Username** то же значение, как **имя пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями.

Для настройки и проверки Azure AD единого входа с Central Desktop, необходимо выполнить следующие стандартные блоки:

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Central Desktop](#create-a-central-desktop-test-user) имел аналог Саймон Britta в Central Desktop, связанного с представлением Azure AD пользователя.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении Central Desktop.

**Чтобы настроить Azure AD единого входа с Central Desktop, выполните следующие действия:**

1. На портале Azure на **Central Desktop** странице интеграции приложений выберите **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить единый вход, в **единого входа** в диалоговом **режим** раскрывающемся списке выберите **входа на базе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. В **центра домена рабочего стола и URL-адреса** статьи, выполните следующие действия:

    ![Центра домена рабочего стола и URL-адреса единого входа сведения](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.centraldesktop.com`.

    Б. В **идентификатор** введите URL-адрес с помощью следующего шаблона:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Обновите эти значения фактический идентификатор, URL-адрес ответа и URL-адрес входа. Обратитесь к [группа поддержки клиента Central Desktop](https://imeetcentral.com/contact-us) для получения этих значений. 

4. В **сертификат подписи SAML** выберите **сертификата**. Затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Нажмите кнопку **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. В **Настройка централизованных рабочих столов** выберите **Настройка Central Desktop** Открытие **Настройка входа** окна. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка централизованных рабочих столов](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Войдите в ваш **Central Desktop** клиента.

8. Перейдите в меню **Параметры**. Выберите **Дополнительно**, а затем выберите **Single Sign On**.

    ![Настройка — Дополнительно](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "Настройка — Дополнительно")

9. На **настройки единого входа** выполните следующие действия:

    ![Единый вход параметры](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "настройки единого входа")
    
    a. Установите флажок **Разрешить единый вход SAML версии 2**.
    
    Б. В **URL-адрес SSO** вставьте **идентификатор сущности SAML** значением, скопированным на портале Azure.
    
    c. В **URL-адрес входа SSO** вставьте **SAML единого входа URL-адрес службы** значением, скопированным на портале Azure.
    
    d. В **URL-адрес выхода SSO** вставьте **URL-адрес выхода** значением, скопированным на портале Azure.

10. В **метод проверки подписи сообщения** статьи, выполните следующие действия:

    ![Метод проверки подписи сообщения](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "метод проверки подписи сообщения") . Выберите **Сертификат**.
    
    Б. В **сертификат SSO** выберите **RSH SHA256**.
    
    c. В блокноте, откройте загруженный сертификат. Затем скопируйте его содержимое сертификата и вставьте его в **сертификат SSO** поля.
        
    d. Установите флажок **Отображать ссылку на страницу входа SAML версии 2**.
    
    д. Выберите **обновление**.

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из **Active Directory** > **корпоративных приложений** выберите **Single Sign-On** вкладку, а затем открыть внедренный Документация по **конфигурации** в нижней. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Чтобы отобразился список пользователей, выберите элемент **Пользователи и группы**. Затем выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-central-desktop-test-user"></a>Создание тестового пользователя Central Desktop

Для пользователей Azure AD могли осуществлять вход их необходимо подготовить в приложении Central Desktop. В этом разделе описывается создание учетных записей пользователей Azure AD в Central Desktop.

> [!NOTE]
> Для подготовки учетных записей Azure AD, можно использовать другие средства создания учетных записей пользователя Central Desktop или интерфейсы API, предоставляемые Central Desktop.

**Чтобы подготовить учетные записи пользователей для Central Desktop, сделайте следующее:**

1. Войдите в клиент Central Desktop.

2. Последовательно выберите пункты **людей** > **внутренние члены**.

3. Выберите **добавить внутренние члены**.

    ![Люди](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "Люди")
    
4. В **электронной почты адрес новых членов** введите учетную запись Azure AD, которую требуется подготовить, а затем выберите **Далее**.

    ![Отправить по электронной почте адреса новых участников](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "адреса новых участников электронной почты")

5. Выберите **добавить внутренние члены**.

    ![Добавить внутренний член](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "добавить внутренний член")
   
   >[!NOTE]
   >Пользователи, которые вы добавляете получения электронной почты, содержащее ссылку для подтверждения для активации своих учетных записей.
   
### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите пользователя Саймон Britta для использования Azure единого входа, предоставляя им доступ к Central Desktop.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta Central Desktop, выполните следующие действия:**

1. На портале Azure откройте представление "Приложения". Перейдите в представление каталога, а затем перейдите к **корпоративных приложений**.

2. Выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Central Desktop**.

    ![В списке приложений ссылку Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В **добавить назначение** выберите **назначить** кнопки.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе тестирования конфигурации Azure AD единого входа с помощью панели доступа.

При выборе Central Desktop плитки на панели доступа, можно автоматически получить входить в приложении Central Desktop.
Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png


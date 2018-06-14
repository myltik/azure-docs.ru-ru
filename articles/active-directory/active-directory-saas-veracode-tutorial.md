---
title: Руководство по интеграции Azure Active Directory с Veracode | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 774cd70f7cc97be8b050cf6ac31063c150b2f657
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352690"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Руководство. Интеграция Azure Active Directory с Veracode

В этом руководстве описано, как интегрировать Veracode с Azure Active Directory (Azure AD).

Интеграция Veracode с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Veracode.
- Вы можете включить автоматический вход пользователей в Veracode (единый вход) с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Veracode, вам потребуется:

- подписка Azure AD;
- Подписка Veracode с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Veracode из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="add-veracode-from-the-gallery"></a>Добавление Veracode из коллекции
Чтобы настроить интеграцию Veracode с Azure AD, необходимо добавить Veracode из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Veracode из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Veracode**, выберите **Veracode** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Veracode в списке результатов](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Veracode с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Veracode соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Veracode.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Veracode.

Чтобы настроить и проверить единый вход Azure AD в Veracode, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Veracode](#create-a-veracode-test-user)** требуется для того, чтобы в Veracode существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Veracode.

**Чтобы настроить единый вход Azure AD в Veracode, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Veracode** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. В разделе **Домен и URL-адреса единого входа в Veracode** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure. 

    ![Настройка единого входа](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Veracode с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.

    Приложение Veracode ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML** . На следующем снимке экрана приведен пример.
    
    ![Атрибуты](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "Атрибуты")

6. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.

    | Имя атрибута | Значение атрибута |
    |--- |--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    a. Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.
    
    ![Атрибуты](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "Атрибуты")
    
    ![Атрибуты](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "Атрибуты")
    
    Б. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
    
    c. В текстовом поле **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. В разделе **Конфигурация Veracode** щелкните **Настроить Veracode**, чтобы открыть окно **Настройка единого входа**. Скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) из раздела **Краткий справочник**.

    ![Конфигурация Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. В другом окне веб-браузера войдите на веб-сайт Veracode вашей компании в качестве администратора.

10. В верхнем меню нажмите **Settings** (Параметры), а затем выберите **Admin** (Администратор).
   
    ![Администрирование](./media/active-directory-saas-veracode-tutorial/ic802911.png "Администрирование")

11. Щелкните вкладку **SAML** .

12. В разделе **Параметры SAML для организации** выполните следующие действия.
   
    ![Администрирование](./media/active-directory-saas-veracode-tutorial/ic802912.png "Администрирование")
   
    a.  В текстовое поле **Issuer** (Издатель) вставьте **идентификатор сущности SAML**, скопированный на портале Azure.
    
    Б. Чтобы загрузить сертификат, скачанный на портале Azure, нажмите кнопку **Choose File** (Выбрать файл).
   
    c. Выберите параметр **Включить саморегистрацию**.

13. В разделе **Self Registration Settings** (Параметры саморегистрации) выполните следующие действия, а затем нажмите кнопку **Сохранить**.
   
    ![Администрирование](./media/active-directory-saas-veracode-tutorial/ic802913.png "Администрирование")
   
    a. Для параметра **New User Activation** (Активация нового пользователя) выберите значение **No Activation Required** (Активация не требуется).
   
    Б. Для параметра **User Data Updates** (Обновления пользовательских данных) выберите значение **Preference Veracode User Data** (Предпочтение пользовательских данных Veracode).
   
    c. Для параметра **Сведения об атрибутах SAML**выберите следующие значения:
      * **роли пользователей;**
      * **администратор политики;**
      * **рецензент;**
      * **руководитель безопасности;**
      * **руководитель;**
      * **отправитель;**
      * **создатель;**
      * **все типы проверки;**
      * **участие в группе;**
      * **группа по умолчанию.**

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-veracode-test-user"></a>Создание тестового пользователя Veracode
Чтобы пользователи Azure AD могли выполнять вход в Veracode, они должны быть подготовлены для Veracode. В случае Veracode подготовка выполняется автоматически. С вашей стороны никакие действия не требуются. В случае необходимости пользователи создаются автоматически при первой попытке входа в систему.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Veracode или API-интерфейсы, предоставляемые Veracode для подготовки учетных записей пользователей Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Veracode.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Veracode, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Veracode**.

    ![Ссылка на Veracode в списке "Приложения"](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Veracode на панели доступа, вы автоматически войдете в приложение Veracode.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png


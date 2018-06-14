---
title: Руководство по интеграции Azure Active Directory с Velpic SAML | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 03ef1ef04d80ac9bb83bcce2082b6cc3f617d812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349453"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Руководство. Интеграция Azure Active Directory с Velpic SAML

В этом руководстве описано, как интегрировать Velpic SAML с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Velpic SAML обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Velpic SAML.
- Вы можете включить автоматический вход пользователей в Velpic SAML (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Velpic SAML, вам потребуется:

- подписка Azure AD;
- подписка на приложение Velpic SAML с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Velpic SAML из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-velpic-saml-from-the-gallery"></a>Добавление Velpic SAML из коллекции
Чтобы настроить интеграцию Velpic SAML с Azure AD, необходимо добавить Velpic SAML из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Velpic SAML из коллекции, выполните следующие действия.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Velpic SAML**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. На панели результатов выберите **Velpic SAML** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Velpic SAML с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какой пользователь в Velpic SAML соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Velpic SAML.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Velpic SAML.

Чтобы настроить и проверить единый вход Azure AD в Velpic SAML, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Velpic SAML](#creating-a-velpic-saml-test-user)** требуется для создания в Velpic SAML пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход в Azure AD на портале управления Azure и настроить его в приложении Velpic SAML.

**Чтобы настроить единый вход Azure AD в Velpic SAML, выполните следующие действия.**

1. На портале управления Azure на странице интеграции с приложением **Velpic SAML** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Введите сведения в разделе **Домены и URL-адреса приложения Velpic SAML**

    ![Настройка единого входа](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. В текстовом поле **URL-адрес для входа** введите значение `https://<sub-domain>.velpicsaml.net`.

    Б. В текстовом поле **Идентификатор** вставьте значение параметра **URL-адрес единого входа**.`https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Обратите внимание, что URL-адрес входа предоставляется командой поддержки Velpic SAML, а значение идентификатора станет доступно только после настройки подключаемого модуля единого входа на стороне Velpic SAML. Скопируйте это значение на странице приложения Velpic SAML и вставьте в это поле.

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. В разделе "Настройка Velpic SAML" щелкните "Настроить Velpic SAML", чтобы открыть окно "Настройка единого входа". Скопируйте идентификатор сущности SAML из раздела "Краткий справочник".

7. В другом окне веб-браузера войдите на корпоративный сайт Velpic SAML с правами администратора.

8. Щелкните вкладку **Manage** (Управление), перейдите к разделу **Integration** (Интеграция) и нажмите там кнопку **Plugins** (Подключаемые модули), чтобы создать новый подключаемый модуль для входа.

    ![Подключаемый модуль](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. Нажмите кнопку **Add plugin** (Добавить подключаемый модуль).
    
    ![Подключаемый модуль](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. Щелкните плитку **SAML** на странице добавления подключаемого модуля.
    
    ![Подключаемый модуль](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. Введите имя нового подключаемого модуля SAML и нажмите кнопку **Add** (Добавить).

    ![Подключаемый модуль](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. Введите следующие сведения:

    ![Подключаемый модуль](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. В текстовом поле **Name** (Имя) введите имя подключаемого модуля SAML.

    Б. В текстовом поле **Issuer URL** (URL-адрес издателя), вставьте **идентификатор сущности SAML**, который вы скопировали в окне **Настройка входа** на портале Azure.

    c. В поле **Provider Metadata Config** (Конфигурация поставщика метаданных) загрузите XML-файл метаданных, который вы скачали с портала Azure.

    d. Также вы можете установить флажок **Auto create new users** (Автоматическое создание новых пользователей), чтобы включить JIT-подготовку SAML. Если этот флаг не установлен, то при попытке входа в Velpic несуществующего пользователя из Azure произойдет ошибка входа. Если флаг установлен, новые пользователи будут автоматически создаваться для Velpic при первом входе. 

    д. Скопируйте значение из текстового поля **Single sign on URL** (URL-адрес единого входа) и вставьте его на портале Azure.
    
    f. Выберите команду **Сохранить**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Создание тестового пользователя Velpic SAML

Этот шаг обычно не является обязательным, так как приложение поддерживает JIT-подготовку пользователей. Но если вы решите отключить автоматическую подготовку пользователей, то пользователя можно создать вручную, как описано ниже.

Войдите на корпоративный сайт Velpic SAML с правами администратора и выполните следующие действия.
    
1. Щелкните вкладку "Manage" (Управление) и перейдите в раздел "Users" (Пользователи), а затем нажмите кнопку "New" (Добавить), чтобы добавить пользователей.

    ![добавить пользователя](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. На странице **Create New User** (Создание пользователя) выполните следующие действия.

    ![user](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. В текстовое поле **First Name** (Имя) введите имя пользователя Britta Simon.

    Б. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя Britta Simon.

    c. В текстовое поле **User Name** (Имя пользователя) введите имя пользователя для пользователя Britta Simon.

    d. В текстовом поле **Электронная почта** введите адрес электронной почты учетной записи Britta Simon.

    д. Все остальные данные являются необязательными, но вы можете внести их, если сочтете нужным.
    
    f. Щелкните **СОХРАНИТЬ**.  

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Velpic SAML.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Velpic SAML, выполните указанные ниже действия.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Velpic SAML**.

    ![Настройка единого входа](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

1. Когда вы нажмете плитку Velpic SAML на панели доступа, должна появиться страница входа в приложение Velpic SAML. На этой странице входа вы увидите кнопку **Log In With Azure AD** (Войти с помощью Azure AD).

    ![Подключаемый модуль](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. Щелкните кнопку **Log In With Azure AD** (Войти с помощью Azure AD), чтобы войти в Velpic с использованием учетной записи Azure AD.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png


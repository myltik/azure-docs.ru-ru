---
title: "Руководство по интеграции Azure Active Directory с OfficeSpace Software | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в OfficeSpace Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: fb61a2d622b0d829a134b6ce5dfef6e9fb44fa1e
ms.lasthandoff: 03/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Учебник. Интеграция Azure Active Directory с OfficeSpace Software

В этом руководстве описано, как интегрировать OfficeSpace Software с Azure Active Directory (Azure AD).

Интеграция OfficeSpace Software с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к OfficeSpace Software.
- Вы можете включить автоматический вход пользователей в OfficeSpace Software (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с OfficeSpace Software, вам потребуется следующее.

- подписка Azure AD;
- подписка OfficeSpace Software с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление OfficeSpace Software из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-officespace-software-from-the-gallery"></a>Добавление OfficeSpace Software из коллекции
Чтобы настроить интеграцию OfficeSpace Software с Azure AD, необходимо добавить OfficeSpace Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OfficeSpace Software из коллекции, выполните следующее.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **OfficeSpace Software**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

5. В области результатов выберите **OfficeSpace Software** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в OfficeSpace Software с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в OfficeSpace Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OfficeSpace Software.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в OfficeSpace Software.

Чтобы настроить и проверить единый вход в Azure AD в OfficeSpace Software, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя OfficeSpace Software](#creating-an-officespace-software-test-user)** требуется для создания в OfficeSpace Software пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении OfficeSpace Software.

**Чтобы настроить единый вход Azure AD в OfficeSpace Software, выполните следующее.**

1. На портале управления Azure на странице интеграции с приложением **OfficeSpace Software** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

3. В разделе **Домены и URL-адреса приложения OfficeSpace Software** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.officespacesoftware.com/users/sign_in/saml`.

    b. В текстовом поле **Идентификатор** введите значение в следующем формате: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Обратитесь к [группе поддержки OfficeSpace Software](mailto:support@officespacesoftware.com), чтобы получить эти значения. 

4. Приложение OfficeSpace Software ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** выберите значение **user.mail** для параметра **Идентификатор пользователя**, а в каждой строке в таблице ниже выполните следующие действия:
    
    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    а. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png)

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)
    
    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

6. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

7. Щелкните **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. В разделе **OfficeSpace Software Configuration** (Конфигурация OfficeSpace Software) щелкните **Configure OfficeSpace Software** (Настройка OfficeSpace Software), чтобы открыть окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_09.png) 

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_10.png)

9. В другом окне веб-браузера войдите в свой клиент OfficeSpace Software в качестве администратора.

10. Выберите **Settings** (Параметры) и щелкните **Connectors** (Соединители).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. Щелкните **SAML Authentication** (Аутентификация SAML).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. В разделе **Проверка подлинности SAML** сделайте следующее:

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    а. В текстовом поле **Logout provider url** (URL-адрес поставщика выхода) введите значение **URL-адрес выхода** из окна настройки приложения Azure AD.

    b. В текстовом поле **Client idp target url** (Целевой URL-адрес IdP клиента) введите значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) из окна настройки приложения Azure AD.

    c. Скопируйте значение поля **Thumbprint** (Отпечаток) из скачанного сертификата и вставьте его в текстовое поле **Client idp cert fingerprint** (Отпечаток сертификата IdP клиента). 

    d. Нажмите кнопку **Сохранить параметры**.

    > [!NOTE]
    > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI) 
  

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-an-officespace-software-test-user"></a>Создание тестового пользователя OfficeSpace Software

Цель этого раздела — создать пользователя Britta Simon в OfficeSpace Software. Приложение OfficeSpace Software поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к OfficeSpace Software (если он еще не создан).

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться к [группе поддержки OfficeSpace Software](mailto:support@officespacesoftware.com).


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к OfficeSpace Software.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в OfficeSpace Software, выполните следующее.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **OfficeSpace Software**.

    ![Настройка единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "OfficeSpace Software" на панели доступа, вы автоматически войдете в приложение OfficeSpace Software.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png

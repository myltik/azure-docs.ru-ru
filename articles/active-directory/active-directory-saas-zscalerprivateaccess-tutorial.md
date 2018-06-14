---
title: Руководство по интеграции Azure Active Directory с Zscaler Private Access (ZPA) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Zscaler Private Access (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 1d5434c57a95e53e4fe37c33f3fe11585fc3177f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354339"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Руководство по интеграции Azure Active Directory с Zscaler Private Access (ZPA)

В этом руководстве описано, как интегрировать Zscaler Private Access (ZPA) с Azure Active Directory (Azure AD).

Интеграция Azure AD с Zscaler Private Access (ZPA) обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Zscaler Private Access (ZPA).
- Вы можете включить автоматический вход пользователей в Zscaler Private Access (ZPA) (единый вход) с их учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Zscaler Private Access (ZPA), вам потребуется:

- подписка Azure AD;
- подписка Zscaler Private Access (ZPA) с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Zscaler Private Access (ZPA) из коллекции.
2. настройка и проверка единого входа в Azure AD.


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Добавление Zscaler Private Access (ZPA) из коллекции
Чтобы настроить интеграцию Zscaler Private Access (ZPA) с Azure AD, необходимо добавить Zscaler Private Access (ZPA) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Private Access (ZPA) из коллекции, выполните следующее.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Zscaler Private Access (ZPA)**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. На панели результатов выберите **Zscaler Private Access (ZPA)** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Zscaler Private Access (ZPA) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо указать, какой пользователь в Zscaler Private Access (ZPA) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Zscaler Private Access (ZPA).

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Zscaler Private Access (ZPA).

Чтобы настроить и проверить единый вход Azure AD в Zscaler Private Access (ZPA), вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Zscaler Private Access (ZPA)](#creating-a-zscaler-private-access-(zpa)-test-user)** требуется для создания в Zscaler Private Access (ZPA) пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Zscaler Private Access (ZPA).

**Чтобы настроить единый вход Azure AD в Zscaler Private Access (ZPA), выполните следующее.**

1. На портале управления Azure на странице интеграции с приложением **Zscaler Private Access (ZPA)** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. В разделе **Домены и URL-адреса приложения Zscaler Private Access (ZPA)** выполните следующее.
    
    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`.

    Б. В текстовом поле **Идентификатор** введите `https://samlsp.private.zscaler.com/auth/metadata`.

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Мы рекомендуем использовать для идентификатора уникальное значение URL-адреса. Обратитесь к [группе поддержки Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket), чтобы получить эти значения.

4. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_400.png)   

5. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. В другом окне веб-браузера войдите на свой корпоративный сайт Zscaler Private Access (ZPA) в качестве администратора.

10. Выберите **Administrator** (Администратор) и щелкните **Idp Configuration** (Конфигурация IdP).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. В разделе **Idp Configuration** (Конфигурация IdP) щелкните **Add New IDP Configuration** (Добавить новую конфигурацию IdP).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. В разделе **New IDP Configuration** (Создание конфигурации IdP) выполните следующее.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Чтобы отправить скачанный файл метаданных, щелкните **Select File** (Выбрать файл).

    Б. Нажмите кнопку **Сохранить** .
    


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Создание тестового пользователя Zscaler Private Access (ZPA)

В этом разделе описано, как создать пользователя Britta Simon в приложении Zscaler Private Access (ZPA). Обратитесь за помощью к [группе поддержки Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket), чтобы добавить пользователей на платформе Zscaler Private Access (ZPA).


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Zscaler Private Access (ZPA).

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Zscaler Private Access (ZPA), выполните следующее.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Zscaler Private Access (ZPA)**.

    ![Настройка единого входа](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Zscaler Private Access (ZPA)" на панели доступа, вы автоматически войдете в свое приложение Zscaler Private Access (ZPA).


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_203.png
---
title: Руководство. Интеграция Azure Active Directory с FirmPlay — Employee Advocacy for Recruiting | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в FirmPlay — Employee Advocacy for Recruiting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: b154901d2e31f493c32e47bd331cc2d4e9fdc1a4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342286"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Руководство. Интеграция Azure Active Directory с FirmPlay — Employee Advocacy for Recruiting

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением FirmPlay — Employee Advocacy for Recruiting.

Интеграция Azure AD с приложением FirmPlay обеспечивает следующие преимущества.

- Вы можете управлять доступом к FirmPlay — Employee Advocacy for Recruiting с помощью Azure AD.
- Вы можете включить автоматический вход пользователей в FirmPlay — Employee Advocacy for Recruiting (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с FirmPlay — Employee Advocacy for Recruiting, вам потребуются следующие компоненты:

- подписка Azure AD;
- подписка на FirmPlay — Employee Advocacy for Recruiting с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление FirmPlay — Employee Advocacy for Recruiting из коллекции
2. настройка и проверка единого входа в Azure AD.


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Добавление FirmPlay — Employee Advocacy for Recruiting из коллекции
Чтобы настроить интеграцию FirmPlay — Employee Advocacy for Recruiting с Azure AD, необходимо добавить приложение FirmPlay из коллекции в список управляемых приложений SaaS.

**Чтобы добавить FirmPlay — Employee Advocacy for Recruiting из коллекции, выполните следующие действия.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **FirmPlay — Employee Advocacy for Recruiting**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. На панели результатов выберите **FirmPlay — Employee Advocacy for Recruiting** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в FirmPlay — Employee Advocacy for Recruiting с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в FirmPlay — Employee Advocacy for Recruiting соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в FirmPlay — Employee Advocacy for Recruiting.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в FirmPlay — Employee Advocacy for Recruiting.

Чтобы настроить и проверить единый вход Azure AD в FirmPlay, выполните следующие действия.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя FirmPlay — Employee Advocacy for Recruiting](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)** требуется для дублирования пользователя Britta Simon в FirmPlay, который связан с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении FirmPlay — Employee Advocacy for Recruiting.

**Чтобы настроить единый вход Azure AD в FirmPlay — Employee Advocacy for Recruiting, выполните следующие действия:**

1. На портале управления Azure на странице интеграции с приложением **FirmPlay — Employee Advocacy for Recruiting** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. В текстовом поле **URL-адрес для входа** раздела **Домены и URL-адреса приложения FirmPlay — Employee Advocacy for Recruiting** введите URL-адрес в следующем формате: `https://<your-subdomain>.firmplay.com/`

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Обратите внимание, что это значение используется только в качестве примера. Вместо него необходимо указать фактический URL-адрес для входа. Чтобы получить это значение, обратитесь в [службу поддержки FirmPlay — Employee Advocacy for Recruiting](mailto:engineering@firmplay.com). 

4. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере. 

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. В разделе **конфигурации FirmPlay — Employee Advocacy for Recruiting** щелкните **Настроить FirmPlay — Employee Advocacy for Recruiting**, чтобы открыть диалоговое окно **Настройка входа**.

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Для настройки единого входа для своего приложения обратитесь в [службу поддержки FirmPlay — Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) и укажите следующие сведения: 

    • скачанный **файл сертификата**;

    • **URL-адрес службы единого входа SAML**;

    • **идентификатор сущности SAML**;

    • **URL-адрес выхода**.
  

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Создание тестового пользователя FirmPlay — Employee Advocacy for Recruiting

В этом разделе вы создадите тестового пользователя FirmPlay — Employee Advocacy for Recruiting с именем Britta Simon. Обратитесь в [службу поддержки FirmPlay — Employee Advocacy for Recruiting](mailto:engineering@firmplay.com), чтобы добавить пользователей в платформу FirmPlay — Employee Advocacy for Recruiting.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе объясняется, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к FirmPlay — Employee Advocacy for Recruiting.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению FirmPlay — Employee Advocacy for Recruiting, выполните следующие действия.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **FirmPlay — Employee Advocacy for Recruiting**.

    ![Настройка единого входа](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Если вы щелкнете плитку FirmPlay — Employee Advocacy for Recruiting на панели доступа, вы автоматически войдете в приложение FirmPlay — Employee Advocacy for Recruiting.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png
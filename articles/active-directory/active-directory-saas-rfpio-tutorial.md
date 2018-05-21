---
title: Руководство по интеграции Azure Active Directory с RFPIO | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 915b6f9c96bc8dbab54e770340acf795df92a3fb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Руководство по интеграции Azure Active Directory с RFPIO

В этом руководстве описано, как интегрировать RFPIO с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением RFPIO обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к RFPIO.
- Вы можете включить автоматический вход пользователей в RFPIO (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с RFPIO, вам потребуется:

- подписка Azure AD;
- подписка RFPIO с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для выполнения действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление RFPIO из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="add-rfpio-from-the-gallery"></a>Добавление RFPIO из коллекции
Чтобы настроить интеграцию RFPIO с Azure AD, необходимо добавить RFPIO из коллекции в список управляемых приложений SaaS.

### <a name="to-add-rfpio-from-the-gallery"></a>Добавление RFPIO из коллекции

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **RFPIO**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. На панели результатов выберите **RFPIO** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение RFPIO с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователем в RFPIO соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RFPIO.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в RFPIO.

Чтобы настроить и проверить единый вход Azure AD в RFPIO, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя RFPIO](#creating-a-rfpio-test-user)** требуется для создания в RFPIO пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении RFPIO.

**Чтобы настроить единый вход Azure AD в RFPIO, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **RFPIO** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения RFPIO** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://www.rfpio.com`.

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    Б. Установите флажок **Показать дополнительные параметры URL-адресов**,

    c. В текстовом поле **Состояние ретранслятора** введите строковое значение. Чтобы получить это значение, обратитесь в [службу поддержки RFPIO](https://www.rfpio.com/contact/). 

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**. 

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://www.app.rfpio.com`

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. В другом окне веб-браузера войдите на веб-сайт **RFPIO** в качестве администратора.

8. Щелкните раскрывающийся список в нижнем левом углу.

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. Щелкните **Organization Settings** (Параметры организации). 

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. Щелкните **FEATURES & INTEGRATION** (Функции и интеграция).

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. В окне **SAML SSO Configuration** (Конфигурация единого входа SAML) щелкните **Edit** (Изменить).

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. В этом разделе выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. Скопируйте содержимое **скачанного XML-файла с метаданными** и вставьте его в текстовое поле **Identity configuration** (Конфигурация удостоверений).

    > [!NOTE]
    >Чтобы скопировать содержимое скачанного **XML-файла с метаданными**, используйте **Notepad++** или соответствующий **редактор XML**. 

    Б. Щелкните **Проверить**.

    c. После нажатия кнопки **Validate** (Проверить) установите переключатель **SAML(Enabled)** (SAML (Включено)) в положение "Вкл.".

    d. Нажмите кнопку **Submit**(Отправить).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-rfpio-test-user"></a>Создание тестового пользователя RFPIO

Чтобы пользователи Azure AD могли выполнять вход в RFPIO, они должны быть подготовлены в RFPIO.  
В случае с RFPIO подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт RFPIO компании в качестве администратора.

2. Щелкните раскрывающийся список в нижнем левом углу.

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. Щелкните **Organization Settings** (Параметры организации). 

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. Щелкните **TEAM MEMBERS** (Участники команды).

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. Щелкните **ADD MEMBERS** (Добавить участников).

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. В разделе **Add New Members** (Добавление новых участников) выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. Введите **адрес электронной почты** в текстовом поле **Enter one email per line** (Введите один адрес электронной почты в каждой строке).

    Б. Выберите **роль** в соответствии с требованиями.

    c. Щелкните **ADD MEMBERS** (Добавить участников).
        
    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к RFPIO, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в RFPIO, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **RFPIO**.

    ![Настройка единого входа](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент RFPIO на панели доступа, вы автоматически войдете в приложение RFPIO.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список руководств по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png

